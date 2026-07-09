# CLAUDE.md - Talon Community Fork Management Guide

This file documents the setup and management of this forked Talon community repository, including how personal customizations are tracked and how to keep the fork synchronized with upstream.

## Repository Structure

### Remotes
- **origin**: `git@github.com:jayostis/knausj_talon.git` (your fork)
- **upstream**: `git@github.com:talonhub/community.git` (official community repo)

### Branches
- **master**: Default branch; clean mirror of upstream (no personal customizations)
- **custom**: Earlier working branch with tracked CSV customizations (superseded)
- **feature/settings_customizations**: **Current daily driver.** A strict superset of
  `custom` — it contains all of `custom`'s commits plus the newer entries
  (`talon,talent`, `claude,cloud`, `claude,clad`, `close,clothes`) and the settings docs.

## Tracked Personal Customizations

The following CSV files in `settings/` contain personal customizations and are tracked in the `custom` branch:

### 1. `settings/abbreviations.csv`
- Personal abbreviations and their spoken forms
- Example: "J peg" → "jpg"
- Auto-generated on first run but customizable

### 2. `settings/file_extensions.csv`
- How to pronounce file extensions
- Example: "dot pie" → ".py"
- Personal pronunciation preferences

### 3. `settings/words_to_replace.csv`
- Auto-corrections and capitalizations
- Example: automatically capitalize "January"
- Custom proper nouns and corrections

## Ignored Files

### Auto-Generated Files (Ignored)
These files are automatically generated and should NOT be tracked:

#### In the tracked root `.gitignore`:
- `core/system_paths-*.talon-list` - Machine-specific paths (Desktop, Documents, etc.)

#### In `settings/.gitignore`:
- `*.csv-converted-to-talon-list` - Auto-converted CSV files for performance

> **Do not put these rules in `.git/info/exclude`.** That file lives inside `.git`, so it is
> never cloned — a rule placed there silently vanishes on the next machine. (This document
> previously claimed the `system_paths` rule lived there; it did not survive, which is exactly
> the failure mode described. It is now in the tracked root `.gitignore`.)

### Why These Are Ignored
- **system_paths files**: Different on each machine (contain machine-specific paths)
- **csv-converted files**: Auto-generated from CSV sources, regenerated on Talon startup.
  Note these are only produced for *legacy* CSVs. `words_to_replace.csv`,
  `abbreviations.csv`, and `file_extensions.csv` are deliberately never converted — see
  `migration_helpers/migration_helpers.py:36-37`.

## Workflow Commands

### Daily Work
```bash
# Always work in the daily-driver branch
cd community
git checkout feature/settings_customizations
```

### Updating from Upstream Community
```bash
# 1. Update master from upstream
git checkout master
git pull upstream master

# 2. Push updated master to your fork (optional, for backup)
git push origin master

# 3. Merge updates into the daily driver
git checkout feature/settings_customizations
git merge master

# 4. Push it back
git push origin feature/settings_customizations
```

### Handling CSV Changes
```bash
# After modifying CSV files
git add settings/*.csv
git commit -m "Update abbreviations/file extensions/words"
git push origin feature/settings_customizations
```

### Syncing Between Machines
```bash
# Machine 1: After making changes
git push origin feature/settings_customizations

# Machine 2: Pull changes
git checkout feature/settings_customizations
git pull origin feature/settings_customizations
```

## Conflict Resolution

If upstream modifies CSV file structure (rare):

```bash
# During merge, if conflicts occur
git status  # Check which files have conflicts

# Keep your customizations
git checkout --ours settings/*.csv

# Or manually merge if you want both changes
# Edit files to resolve conflicts, then:
git add settings/*.csv
git commit -m "Merge upstream, preserve customizations"
```

## How Talon CSV Files Work

### Auto-Generation Process
1. When Talon starts, it checks for CSV files in `settings/`
2. If missing, creates them with defaults via `write_csv_defaults()` in `core/user_settings.py`
3. Files are watched using `@track_csv_list` decorator
4. Changes are immediately picked up by Talon at runtime

### File Processing
- CSV files are the source of truth
- `.csv-converted-to-talon-list` files are generated for performance
- Talon watches CSV files for changes and regenerates compiled versions

## Important Notes

### Branch Usage
- **Never commit directly to master** - Keep it clean for upstream updates
- **Always work in `feature/settings_customizations`** - Your daily driver
- **CSV customizations are safe** - They do not exist on `master`

### Fork Naming
- Fork is named `knausj_talon` on GitHub (historical name)
- Actually syncs with `talonhub/community` (renamed repository)
- GitHub correctly recognizes the relationship

### Update Frequency
- Check for upstream updates monthly or when you hear about new features
- Merge conflicts are rare since community rarely modifies CSV structure
- Most updates merge cleanly without issues

## Setup Summary (For New Machines)

Talon must already be installed, launched once (accept the license agreement), and have a
speech engine selected from the tray icon (**Speech Recognition → Conformer**). The model is
a separate several-hundred-MB download and is *not* part of the installer.

Both repos are cloned directly into the Talon user directory, as siblings:

```powershell
# Windows: Talon user dir is %APPDATA%\talon\user
cd $env:APPDATA\talon\user

# 1. The community fork, on the daily-driver branch
git clone --branch feature/settings_customizations git@github.com:jayostis/knausj_talon.git community
git -C community remote add upstream git@github.com:talonhub/community.git

# 2. Personal command set, a SIBLING of community (not nested inside it)
git clone git@github.com:jayostis/my_talon.git my_talon
```

Quit and relaunch Talon, then check `%APPDATA%\talon\talon.log` for errors.

## File Locations

- Community fork: `C:\Users\Jay\AppData\Roaming\talon\user\community`
- Personal customizations: `C:\Users\Jay\AppData\Roaming\talon\user\my_talon`
  (repo `jayostis/my_talon`, branch `main` — a sibling of `community`, not nested)
- CSV settings: `community/settings/*.csv` (on `feature/settings_customizations`)

`my_talon` depends on `community`: its `settings.talon` sets `user.mode_indicator_show` and
`user.mouse_enable_pop_click`, which are declared by community's `plugin/mode_indicator/`
and `plugin/mouse/`. Loading `my_talon` alone will error.

## Created: 2025-11-13

This setup was created to properly track personal Talon customizations while maintaining easy updates from the community repository.
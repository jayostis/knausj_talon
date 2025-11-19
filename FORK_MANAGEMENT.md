# CLAUDE.md - Talon Community Fork Management Guide

This file documents the setup and management of this forked Talon community repository, including how personal customizations are tracked and how to keep the fork synchronized with upstream.

## Repository Structure

### Remotes
- **origin**: `git@github.com:jayostis/knausj_talon.git` (your fork)
- **upstream**: `git@github.com:talonhub/community.git` (official community repo)

### Branches
- **main**: Clean mirror of upstream/main (no personal customizations)
- **custom**: Working branch with tracked CSV customizations

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

#### In `.git/info/exclude`:
- `core/system_paths-*.talon-list` - Machine-specific paths (Desktop, Documents, etc.)
- `settings/*.csv-converted-to-talon-list` - Compiled versions of CSV files

#### In `settings/.gitignore`:
- `*.csv-converted-to-talon-list` - Auto-converted CSV files for performance

### Why These Are Ignored
- **system_paths files**: Different on each machine (contain machine-specific paths)
- **csv-converted files**: Auto-generated from CSV sources, regenerated on Talon startup

## Workflow Commands

### Daily Work
```bash
# Always work in the custom branch
cd community
git checkout custom
```

### Updating from Upstream Community
```bash
# 1. Update main branch from upstream
git checkout main
git pull upstream main

# 2. Push updated main to your fork (optional, for backup)
git push origin main

# 3. Merge updates into your custom branch
git checkout custom
git merge main

# 4. Push updated custom branch
git push origin custom
```

### Handling CSV Changes
```bash
# After modifying CSV files
git add settings/*.csv
git commit -m "Update abbreviations/file extensions/words"
git push origin custom
```

### Syncing Between Machines
```bash
# Machine 1: After making changes
git push origin custom

# Machine 2: Pull changes
git checkout custom
git pull origin custom
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
- **Never commit directly to main** - Keep it clean for upstream updates
- **Always work in custom branch** - Your daily driver with customizations
- **CSV customizations are safe** - Only exist in custom branch

### Fork Naming
- Fork is named `knausj_talon` on GitHub (historical name)
- Actually syncs with `talonhub/community` (renamed repository)
- GitHub correctly recognizes the relationship

### Update Frequency
- Check for upstream updates monthly or when you hear about new features
- Merge conflicts are rare since community rarely modifies CSV structure
- Most updates merge cleanly without issues

## Setup Summary (For New Machines)

```bash
# Clone your fork
git clone git@github.com:jayostis/knausj_talon.git community
cd community

# Add upstream remote
git remote add upstream git@github.com:talonhub/community.git

# Checkout custom branch
git checkout custom

# You're ready to go!
```

## File Locations

- Main directory: `C:\Users\Jay\AppData\Roaming\talon\user\community`
- Personal customizations: `mine/` directory (separate from community)
- CSV settings: `community/settings/*.csv` (in custom branch)

## Created: 2025-11-13

This setup was created to properly track personal Talon customizations while maintaining easy updates from the community repository.
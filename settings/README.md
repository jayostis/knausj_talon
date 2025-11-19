# Talon Settings Directory Documentation

## Overview

The `community/settings` directory contains CSV configuration files that customize how Talon Voice interprets and processes your voice commands. These files control text abbreviations, file extensions, dictation corrections, and more. Talon automatically watches these files for changes and reloads them in real-time.

**Key Integration Points:**
- CSV tracking system: [`core/user_settings.py`](../core/user_settings.py) (lines 75-92)
- Migration system: [`migration_helpers/migration_helpers.py`](../migration_helpers/migration_helpers.py) (lines 106-224)
- Voice command integration: [`core/edit_text_file/edit_text_file_list.talon-list`](../core/edit_text_file/edit_text_file_list.talon-list) (lines 13-15)

## Quick Reference Table

| File | Status | Safe to Modify | Safe to Delete | Purpose |
|------|--------|----------------|----------------|---------|
| [`abbreviations.csv`](abbreviations.csv) | ✅ ACTIVE | ✅ YES | ❌ NO | Text abbreviation mappings |
| [`file_extensions.csv`](file_extensions.csv) | ✅ ACTIVE | ✅ YES | ❌ NO | Voice commands for file extensions |
| [`words_to_replace.csv`](words_to_replace.csv) | ✅ ACTIVE | ✅ YES | ❌ NO | Dictation corrections |
| [`additional_words.csv-converted-to-talon-list`](additional_words.csv-converted-to-talon-list) | ⚠️ DEPRECATED | ❌ NO | ✅ YES | Migrated to [`core/vocabulary/vocabulary.talon-list`](../core/vocabulary/vocabulary.talon-list) |
| [`alphabet.csv-converted-to-talon-list`](alphabet.csv-converted-to-talon-list) | ⚠️ DEPRECATED | ❌ NO | ✅ YES | Migrated to [`core/keys/letter.talon-list`](../core/keys/letter.talon-list) |
| [`search_engines.csv-converted-to-talon-list`](search_engines.csv-converted-to-talon-list) | ⚠️ DEPRECATED | ❌ NO | ✅ YES | Migrated to [`core/websites_and_search_engines/search_engine.talon-list`](../core/websites_and_search_engines/search_engine.talon-list) |
| [`system_paths.csv-converted-to-talon-list`](system_paths.csv-converted-to-talon-list) | ⚠️ DEPRECATED | ❌ NO | ✅ YES | Migrated to `core/system_paths-{hostname}.talon-list` |
| [`unix_utilities.csv-converted-to-talon-list`](unix_utilities.csv-converted-to-talon-list) | ⚠️ DEPRECATED | ❌ NO | ✅ YES | Migrated to [`tags/terminal/unix_utility.talon-list`](../tags/terminal/unix_utility.talon-list) |
| [`websites.csv-converted-to-talon-list`](websites.csv-converted-to-talon-list) | ⚠️ DEPRECATED | ❌ NO | ✅ YES | Migrated to [`core/websites_and_search_engines/website.talon-list`](../core/websites_and_search_engines/website.talon-list) |

## Active CSV Files

### 1. abbreviations.csv

**Purpose:** Maps spoken forms to abbreviations for text insertion. Used primarily by the spoken forms generation system to intelligently create voice commands for variable names, file names, and other text patterns.

**File References:**
- Primary usage: [`core/create_spoken_forms.py`](../core/create_spoken_forms.py) (lines 55-62, 309-328)
- Default values: [`core/abbreviate/abbreviate.py`](../core/abbreviate/abbreviate.py) (lines 11-449)
- CSV tracking: [`core/user_settings.py`](../core/user_settings.py) (lines 75-92)
- Voice command: [`core/edit_text_file/edit_text_file_list.talon-list`](../core/edit_text_file/edit_text_file_list.talon-list) (line 13)

**CSV Format:**
```csv
Abbreviation,Spoken Form
jpg,J peg
app,application
cfg,config
async,asynchronous
```

**Headers:** `Abbreviation,Spoken Form` (exactly as shown)
- Column 1: The abbreviated form (what gets typed)
- Column 2: The spoken form (what you say)

**How Talon Uses It:**
1. Loaded by `@track_csv_list` decorator in [`core/create_spoken_forms.py`](../core/create_spoken_forms.py) (line 58)
2. Used in `create_abbreviated_forms()` function (lines 309-328) to expand abbreviations
3. Integrated with text formatting commands to convert spoken forms to abbreviated text

**Example Usage:**

Direct abbreviation commands (defined in [`core/text/text.talon`](../core/text/text.talon)):
- `"abbreviate config"` → types `cfg`
- `"brief config"` → types `cfg` (shorter alternative)
- `"abbreviate admin"` → types `admin`
- `"brief admin"` → types `admin`

With formatters (both "abbreviate" and "brief" work):
- `"snake abbreviate config"` → types `cfg` in snake_case
- `"camel brief source"` → types `src` in camelCase
- `"hammer brief async"` → types `ASYNC` in all caps

In compound formatter commands:
- `"snake source control"` → types `src_control` (auto-applies abbreviations)
- `"camel config parser"` → types `cfgParser`
- `"hammer async function"` → types `ASYNC_FUNCTION`

**Voice Commands:**
- `"abbreviate {abbreviation}"` or `"brief {abbreviation}"` - Insert an abbreviation directly
- `"{formatter} abbreviate {abbreviation}"` or `"{formatter} brief {abbreviation}"` - Insert formatted abbreviation
- `"customize abbreviations"` - Opens this file for editing
- `"help search abbreviate"` - Shows all abbreviation-related commands
- `"help formatters"` - Shows all formatters that use abbreviations

**Customization:**
Add your own abbreviations by adding new rows:
```csv
ml,machine learning
ai,artificial intelligence
db,database
```

### 2. file_extensions.csv

**Purpose:** Enables voice-based file extension insertion and recognition. Critical for file name commands and path navigation.

**File References:**
- Primary usage: [`core/create_spoken_forms.py`](../core/create_spoken_forms.py) (lines 44-52, 254-284)
- Default values: [`core/file_extension/file_extension.py`](../core/file_extension/file_extension.py) (lines 8-58)
- Regex compilation: [`core/create_spoken_forms.py`](../core/create_spoken_forms.py) (line 46)
- Voice command: [`core/edit_text_file/edit_text_file_list.talon-list`](../core/edit_text_file/edit_text_file_list.talon-list) (line 14)

**CSV Format:**
```csv
File extension,Name
.py,dot pie
.md,dot mark down
.json,dot jason
.csv,dot csv
```

**Headers:** `File extension,Name` (exactly as shown)
- Column 1: The file extension including the dot (e.g., `.py`)
- Column 2: The spoken form (e.g., `dot pie`)

**How Talon Uses It:**
1. Loaded via `@track_csv_list` decorator in [`core/create_spoken_forms.py`](../core/create_spoken_forms.py) (line 44)
2. Compiled into `FILE_EXTENSIONS_REGEX` for pattern matching (line 46)
3. Used in `create_extension_forms()` function (lines 254-284) to generate file-specific voice commands
4. Enables commands like "config dot pie" → "config.py"

**Example Usage:**
- "dot HTML" → "index.html"
- "dot mark down" → "readme.md"
- "package dot jason" → "package.json"

**Voice Commands:**
- `"customize file extensions"` - Opens this file for editing
- Used in file naming and navigation commands

**Customization:**
Add custom file extensions for your projects:
```csv
.tsx,dot T S X
.jsx,dot J S X
.yaml,dot yam all
```

### 3. words_to_replace.csv

**Purpose:** Auto-corrects commonly misrecognized words during dictation. This is your primary tool for fixing Talon's speech recognition errors.

**File References:**
- Implementation: [`core/vocabulary/vocabulary.py`](../core/vocabulary/vocabulary.py) (lines 49-108, 135-159)
- PhraseReplacer class: [`core/vocabulary/vocabulary.py`](../core/vocabulary/vocabulary.py) (lines 49-108)
- Default values: [`core/vocabulary/vocabulary.py`](../core/vocabulary/vocabulary.py) (lines 16-45)
- Voice command: [`core/edit_text_file/edit_text_file_list.talon-list`](../core/edit_text_file/edit_text_file_list.talon-list) (line 15)
- Action override: [`core/vocabulary/vocabulary.py`](../core/vocabulary/vocabulary.py) (lines 151-159)

**CSV Format:**
```csv
Replacement,Original
January,january
February,february
claude,claud
claude,cloud
```

**Headers:** `Replacement,Original` (exactly as shown)
- Column 1: The replacement word (what gets typed)
- Column 2: The original/spoken word (what Talon recognized)

⚠️ **Note:** This is opposite order from abbreviations.csv!

**How Talon Uses It:**
1. Loaded via `@track_csv_list` decorator in [`core/vocabulary/vocabulary.py`](../core/vocabulary/vocabulary.py) (line 135)
2. Powers the `PhraseReplacer` class for multi-word phrase replacement (lines 49-108)
3. Overrides Talon's default `dictate.replace_words` action (lines 151-159)
4. Supports both single words and multi-word phrases
5. Applied automatically during dictation mode

**Example Usage:**
- Saying "january" → auto-corrects to "January"
- Saying "claud" or "cloud" → auto-corrects to "claude"
- Works with phrases: "machine learning" can be replaced with "ML"

**Voice Commands:**
- `"customize words to replace"` - Opens this file for editing
- `"add selection to words to replace"` - Adds selected text as a replacement ([`core/vocabulary/vocabulary.py`](../core/vocabulary/vocabulary.py) lines 261-271)

**Customization:**
Fix your common dictation errors:
```csv
Talon,talon
Talon,talent
GitHub,github
VS Code,vs code
```

**Important:** Changes apply immediately due to file watching. Test in dictation mode after editing.

## Migration System

### How Migration Works

The migration system automatically converts old CSV files to the new `.talon-list` format on Talon startup.

**Implementation:** [`migration_helpers/migration_helpers.py`](../migration_helpers/migration_helpers.py)
- Conversion function: lines 106-169 (`convert_csv_to_talonlist()`)
- File detection: lines 172-224 (`convert_files()`)
- Auto-run on startup: lines 257-271 (`on_ready()` callback)

### Migration Process

1. **Detection:** Checks for CSV files in settings directory (line 181)
2. **Conversion:** If CSV exists and .talon-list doesn't, performs conversion (lines 183-223)
3. **Backup:** Renames original CSV to `.csv-converted-to-talon-list` (line 218)
4. **Format Translation:**

**CSV Format:**
```csv
Header1,Header2
value1,spoken1
value2,spoken2
```

**Talon-List Format:**
```
list: user.list_name
-
spoken1: value1
spoken2: value2
```

### Migrated Files and New Locations

| Original CSV | New Location | List Name |
|--------------|--------------|-----------|
| `additional_words.csv` | [`core/vocabulary/vocabulary.talon-list`](../core/vocabulary/vocabulary.talon-list) | `user.vocabulary` |
| `alphabet.csv` | [`core/keys/letter.talon-list`](../core/keys/letter.talon-list) | `user.letter` |
| `search_engines.csv` | [`core/websites_and_search_engines/search_engine.talon-list`](../core/websites_and_search_engines/search_engine.talon-list) | `user.search_engine` |
| `system_paths.csv` | `core/system_paths-{hostname}.talon-list` | `user.system_paths` |
| `unix_utilities.csv` | [`tags/terminal/unix_utility.talon-list`](../tags/terminal/unix_utility.talon-list) | `user.unix_utility` |
| `websites.csv` | [`core/websites_and_search_engines/website.talon-list`](../core/websites_and_search_engines/website.talon-list) | `user.website` |

**Note:** The `-converted-to-talon-list` files are backups and can be safely deleted once you've verified the migration.

## Technical Details

### CSV Tracking System

**Implementation:** [`core/user_settings.py`](../core/user_settings.py)

The `@track_csv_list` decorator (lines 75-92) provides:
- **File watching:** Uses `resource.watch()` to monitor CSV changes (line 82)
- **Auto-reload:** Triggers callback when files change (line 85)
- **Default creation:** Creates CSV with defaults if missing (lines 78-79)
- **Error handling:** Validates CSV format and logs warnings (lines 27-49)

**Example usage:**
```python
@track_csv_list("file_extensions.csv", headers=("File extension", "Name"), default=_file_extensions_defaults)
def on_extensions(values):
    # Called whenever file_extensions.csv changes
    pass
```

### File Format Requirements

**Based on:** [`core/user_settings.py`](../core/user_settings.py) `read_csv_list()` function (lines 18-53)

1. **Headers must match exactly** (line 27 check)
2. **Empty lines are skipped** (line 34 - Windows newline handling)
3. **Comments not supported** in active CSV files
4. **Two columns maximum** (line 44 warning)
5. **Whitespace is trimmed** but logged as warning (line 49)

### Integration with Voice Commands

**Edit commands defined in:** [`core/edit_text_file/edit_text_file_list.talon-list`](../core/edit_text_file/edit_text_file_list.talon-list)

Voice commands to edit settings:
- `"customize abbreviations"` → Opens abbreviations.csv (line 13)
- `"customize file extensions"` → Opens file_extensions.csv (line 14)
- `"customize words to replace"` → Opens words_to_replace.csv (line 15)

**Implementation:** [`core/edit_text_file/edit_text_file.py`](../core/edit_text_file/edit_text_file.py) (lines 1-81)

## Common Use Cases

### Adding Project-Specific Abbreviations
```csv
# Add to abbreviations.csv
api,A P I
crud,crud
oauth,O auth
jwt,J W T
```

### Fixing Name Recognition
```csv
# Add to words_to_replace.csv
PyTorch,pie torch
NumPy,numb pie
TensorFlow,tensor flow
```

### Supporting New File Types
```csv
# Add to file_extensions.csv
.ipynb,dot I pie N B
.dockerfile,dot docker file
.tfvars,dot T F vars
```

## Discovering Settings with Talon's Help System

Talon has a built-in help system that lets you explore available commands and settings interactively.

### Useful Help Commands for Settings

**For Abbreviations:**
- `"help search abbreviate"` - Shows the two main abbreviation commands:
  - `(abbreviate | abreviate | brief) {user.abbreviation}` - Direct insertion
  - `<user.formatters> (abbreviate | abreviate | brief) {user.abbreviation}` - With formatting
- `"help formatters"` - Shows all text formatters that use abbreviations behind the scenes

**For File Extensions:**
- `"help search extension"` - Find commands that use file extensions
- `"help search dot"` - Find file extension-related commands

**For Vocabulary/Dictation:**
- `"help search word"` - Find word replacement commands
- `"help search vocabulary"` - Find vocabulary-related commands
- `"help search dictation"` - Find dictation mode commands

**General Help:**
- `"help help"` - Shows all available help topics
- `"help active"` - Shows commands available in current context
- `"help search {phrase}"` - Search for any commands containing phrase

### Editing Settings Files with Voice Commands

You can open any settings file for editing using voice commands:
- `"customize abbreviations"` - Opens abbreviations.csv in your text editor
- `"customize file extensions"` - Opens file_extensions.csv
- `"customize words to replace"` - Opens words_to_replace.csv

These commands automatically open the file and jump to the end for easy additions.

### Testing Your Settings

After modifying CSV files, test them using:

1. **Abbreviations:** Say `"abbreviate {your_abbreviation}"` or `"brief {your_abbreviation}"` to test
2. **File Extensions:** Use in file naming contexts
3. **Words to Replace:** Switch to dictation mode and speak the words

## Troubleshooting

### Changes Not Taking Effect
- Files are watched and auto-reload (check Talon log for errors)
- Ensure headers match exactly as shown above
- Check for trailing whitespace warnings in log

### CSV Format Errors
**Reference:** [`core/user_settings.py`](../core/user_settings.py) error messages
- "Malformed headers" (line 27) - Headers don't match expected format
- "More than two values in row" (line 44) - Extra commas in data
- "Leading/trailing whitespace" (line 49) - Can prevent recognition

### Finding Migrated Files
Check the new locations listed in the migration table above. Original files are backed up with `-converted-to-talon-list` suffix.

## External Documentation

### Official Talon Resources
- [Talon Voice Homepage](https://talonvoice.com/) - Main documentation
- [Talon API Docs](https://talonvoice.com/docs/) - Technical API reference
- [Community Wiki](https://talon.wiki/) - Community documentation
- [Talon Lists Syntax](https://talon.wiki/Customization/talon_lists) - .talon-list format guide

### GitHub Resources
- [Community Repository](https://github.com/talonhub/community) - Main repository
- [Issue Tracker](https://github.com/talonhub/community/issues) - Report problems
- [Automated Tests](https://github.com/talonhub/community#automated-tests) - Testing guide
- [Pre-commit Hooks](https://github.com/talonhub/community#pre-commit-hooks) - Development setup

## Dependencies

### File Dependencies Graph
```
abbreviations.csv
    ↓
create_spoken_forms.py → Variable/file name voice commands
    ↑
file_extensions.csv

words_to_replace.csv
    ↓
vocabulary.py → Dictation correction
```

### Module Dependencies
- `core/user_settings.py` - CSV tracking infrastructure
- `core/create_spoken_forms.py` - Uses abbreviations and file extensions
- `core/vocabulary/vocabulary.py` - Uses words_to_replace
- `migration_helpers/migration_helpers.py` - Handles CSV to .talon-list conversion

## Advanced Customization

### Creating Custom CSV Files

While the three active CSV files cover most needs, you can create custom CSV files:

1. Create your CSV with appropriate headers
2. Use `@track_csv_list` decorator in a Python file to watch it
3. Process the values in your callback function

**Example:** Custom project names
```python
# In your custom .py file
from ..core.user_settings import track_csv_list

@track_csv_list("project_names.csv", headers=("Project", "Spoken"))
def on_projects(values):
    # Process your custom project names
    pass
```

### Programmatic Updates

Add entries programmatically using [`core/user_settings.py`](../core/user_settings.py) `append_to_csv()` function (lines 95-110):

```python
from ..core.user_settings import append_to_csv

# Add a new abbreviation
append_to_csv("abbreviations.csv", ["ml", "machine learning"])
```

## Version History

- **2024 Migration:** CSV files migrated to .talon-list format ([`BREAKING_CHANGES.txt`](../BREAKING_CHANGES.txt) lines 26-28)
- **Current:** Three CSV files remain active (abbreviations, file_extensions, words_to_replace)
- **Future:** May migrate remaining CSVs to .talon-list format

---

*Last updated: November 2024*
*Based on community repository structure at commit referenced in documentation*
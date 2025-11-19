# letter.talon-list Documentation

## Overview
- **Purpose**: Phonetic alphabet for spelling letters in Talon Voice
- **Location**: [`community/core/keys/letter.talon-list`](../../core/keys/letter.talon-list)
- **List Name**: `user.letter`
- **Context**: Command mode, dictation, file manager

## How It Works
Maps spoken words to letters. Say "air" → produces 'a'.

### Main Voice Commands
| Command | Example | Result | Mode | Context/App | Source |
|---------|---------|--------|------|-------------|--------|
| `<user.letter>` | Say "air" | Types 'a' | Command | Any | [keys.talon#L1](../../core/keys/keys.talon#L1) |
| `ship <user.letters>` | Say "ship air bat cap" | Types "ABC" | Command | Any | [keys.talon#L2-3](../../core/keys/keys.talon#L2-3) |
| `spell that <user.letters>` | Say "spell that air bat cap" | Types "abc" | Dictation | Any | [dictation_mode.talon#L70](../../core/modes/dictation_mode.talon#L70) |
| `go {user.letter}` | Say "go cap" | Opens C:\ | Any | Explorer/CMD* | [file_manager_win.talon#L4](../../tags/file_manager/file_manager_win.talon#L4) |

*PowerShell requires [special configuration](../../apps/powershell/README.md)

## Current Defaults (First 3)
| Spoken | Letter | Usage |
|--------|--------|-------|
| air | a | Say "air" → 'a' |
| bat | b | Say "bat" → 'b' |
| cap | c | Say "cap" → 'c' |

## Customization Examples

### Fix Recognition Issues
```
Before: air: a     # Often misheard
After:  apple: a   # Clearer
```

## Voice Commands for Customization

### Opening for Edit
- **Command**: `customize alphabet` → Opens letter.talon-list in text editor ([source](../../core/edit_text_file/edit_text_file.talon))
- **Alternative**: `customize additional words` → Opens vocabulary.talon-list ([source](../../core/edit_text_file/edit_text_file_list.talon-list#L2))
- **View current**: `help alphabet` → Shows all letter mappings ([source](../../core/help/help.talon#L3))

### Workflow Example
1. Say: "customize alphabet"
2. File opens in editor at end
3. Add: `alpha: a` (new phonetic form)
4. Save file → Changes apply immediately

### Limitations
- No inline voice editing (manual edit required)
- No selection-based additions (unlike vocabulary)
- Must use text editor, not voice commands

### Quick Reference
- **File**: [`core/keys/letter.talon-list`](../../core/keys/letter.talon-list)
- **Test changes**: Say "help alphabet"
- **Changes apply**: Immediately on save

## Related Files
- [`core/keys/keys.talon`](../../core/keys/keys.talon) - Key press commands
- [`core/modes/dictation_mode.talon`](../../core/modes/dictation_mode.talon) - Spell mode
- [`tags/file_manager/file_manager_win.talon`](../../tags/file_manager/file_manager_win.talon) - Drive navigation

## Coding Examples

### Using in .talon files
- **Direct list reference**: [`{user.letter}`](../../tags/file_manager/file_manager_win.talon#L4) - Match single letter from list
- **Capture with list**: [`<user.letter>`](../../core/keys/keys.talon#L1) - Capture letter value for use in action
- **Multiple letters**: [`<user.letters>`](../../core/keys/keys.talon#L2) - Chain multiple letters together

### Python implementation
- **Letter capture definition**: [`keys.py#L58-61`](../../core/keys/keys.py#L58-61) - How single letter capture works
- **Letters capture (multiple)**: [`keys.py#L113-116`](../../core/keys/keys.py#L113-116) - Joining multiple letters
- **List usage in action**: [`windows_explorer.py#L141-143`](../../apps/windows_explorer/windows_explorer.py#L141-143) - Volume navigation
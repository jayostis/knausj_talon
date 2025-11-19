# CLAUDE.md

Voice command set for [Talon](https://talonvoice.com/), community-supported. More information can be found at its [GitHub repository](https://github.com/talonhub/community).


## Settings Directory
community\settings


## Common Development Tasks


### Running Tests
*Source: [Talon Community Repository - Automated Tests](https://github.com/talonhub/community#automated-tests)*

The key point: Tests must run in a separate Python environment from Talon itself.

```bash
# Install pytest in your system Python or a separate virtual environment
# NOT in Talon's built-in Python environment at ~/.talon/.venv
#
# For example, using your system Python:
pip install pytest

# Or create a dedicated test environment:
python -m venv .venv
.venv\Scripts\activate  # Windows
# source test_env/bin/activate  # Linux/Mac
pip install pytest

# Run all tests from repository root (e.g., from community/ directory)
pytest

# How it works:
# - Tests use mock Talon APIs from test/stubs/ directory
# - conftest.py handles loading these stubs instead of real Talon APIs
# - This allows testing without having Talon running
```
### Code Formatting
*Source: [Talon Community Repository - Pre-commit Hooks](https://github.com/talonhub/community#pre-commit-hooks)*

```bash
# Install pre-commit (Python 3.9 recommended to match Talon's version)
pip install pre-commit
# Alternative for macOS: brew install pre-commit

# Run on changed files
pre-commit run

# Run on all files
pre-commit run --all-files

# Optional: Install git pre-commit hook for automatic checks
pre-commit install
```

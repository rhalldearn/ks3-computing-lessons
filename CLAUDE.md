# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an **nbdev 3 project** - a literate programming framework where code is written in Jupyter notebooks (`.ipynb` files in `nbs/`) and automatically exported to Python modules. The notebooks serve as both source code and documentation.

This project uses **nbdev 3**, which configures projects via `pyproject.toml` with a `[tool.nbdev]` section (nbdev 2 used `settings.ini`).

**Critical workflow principle**: Never edit Python files in `nbdev_hello/` directly. These are auto-generated from notebooks and will be overwritten. Always edit the source notebooks in `nbs/` instead.

## Architecture

### Source of Truth: Jupyter Notebooks

- **`nbs/` directory**: Contains source notebooks that define the project
  - `index.ipynb` - Generates README.md and documentation homepage
  - `00_core.ipynb` - Generates `nbdev_hello/core.py`
  - Additional notebooks follow `XX_module.ipynb` pattern

- **`nbdev_hello/` directory**: Auto-generated Python package
  - `core.py` - Exported from `00_core.ipynb` (DO NOT EDIT)
  - `_modidx.py` - Auto-generated module index (DO NOT EDIT)
  - `__init__.py` - Auto-generated package initialization (DO NOT EDIT)

- **`_proc/` directory**: Processed notebooks used by nbdev build system

### nbdev Special Comments

Notebooks use special directives in comments:
- `#| default_exp module_name` - Specifies which Python module this notebook exports to
- `#| export` - Marks cells to be exported to the Python module
- `#| hide` - Hides cells from documentation (e.g., imports, nbdev_export calls)

## Essential Commands

### Development Workflow

```bash
# Install package in development mode (first time setup)
pip install -e .

# After editing notebooks in nbs/, run this to sync everything:
nbdev_prepare
```

**`nbdev_prepare`** is the main command - it runs multiple steps:
- Exports notebook cells marked with `#| export` to Python modules
- Tests the notebooks
- Cleans notebook metadata
- Generates README.md from index.ipynb
- Updates documentation

### Individual nbdev Commands

If you need finer control:

```bash
nbdev_export       # Export notebooks to Python modules only
nbdev_test         # Run tests defined in notebooks
nbdev_clean        # Clean notebook metadata
nbdev_readme       # Generate README from index.ipynb
nbdev_docs         # Build documentation site
```

### Testing

Tests are written as executable cells in the notebooks themselves. Run:

```bash
nbdev_test                           # Test all notebooks
nbdev_test --path nbs/00_core.ipynb  # Test a single notebook
```

## Making Changes

1. Edit the appropriate notebook in `nbs/`
2. Run `nbdev_prepare` to export changes to Python modules and update docs
3. Commit both the notebook and generated files

## Documentation

- Documentation is built with Quarto from the notebooks
- GitHub Actions automatically deploys to GitHub Pages on push to main
- Notebooks contain both code and markdown cells that become documentation

## CI/CD

- `.github/workflows/test.yaml` - Runs nbdev3-ci action on all PRs and pushes
- `.github/workflows/deploy.yaml` - Deploys documentation to GitHub Pages on main branch

The CI workflow runs `nbdev_prepare` and checks that all generated files are up to date.

## Configuration

nbdev 3 configuration is in `pyproject.toml` under the `[tool.nbdev]` section:
- `lib_name` - Package name
- `lib_path` - Path to Python package directory
- `nbs_path` - Path to notebooks directory
- `doc_path` - Path for generated documentation
- `repo` - GitHub repository (format: "username/repo")
- `recursive` - Whether to recursively search for notebooks

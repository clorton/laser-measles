# Changelog

## [0.14.0] - 2026-05-17

Added standard results output, snapshot save/load, AI-driven version bumper, strict param validation, and numerous documentation and bug fixes.

### Features

- Added `BaseLaserModel.write_results()` method that produces a canonical `results.json` after `model.run()`, including global and per-patch SEIR summaries, peak infectious counts, attack rates, and final compartment states.
- Added snapshot save/load support for ABM and compartmental models, enabling simulations to be paused and resumed with exact SEIR continuity.
- Added AI-driven `bump_version.py` script that reads commit messages, generates changelog entries, and updates the version in `pyproject.toml` automatically.
- Added GitHub Actions workflow (`build-jenner-doc.yml`) triggered via `workflow_dispatch` to build and upload `combined_mkdocs.md` as a downloadable artifact.
- Added AI Version Bump GitHub Actions workflow with changelog support.

### Bug Fixes

- Fixed `extra='forbid'` (via Pydantic `ConfigDict`) on all `*Params` classes so unknown or misspelled keyword arguments raise a `ValidationError` at construction time instead of being silently discarded.
- Fixed issue #140 where `InfectionParams.distance_exponent` and `mixing_scale` were silently dropped when forwarded to `TransmissionParams`; they are now correctly wired into a `GravityMixing` instance.
- Fixed class docstrings on `*Params` classes that were clobbered when `model_config` was inserted as the first statement in the class body.
- Fixed `add_component` and `prepend_component` to defensively set `instance.name` for custom components that do not inherit from `BaseComponent`, preventing `AttributeError` during `get_instance(str)` lookups.
- Fixed `concat_mkdocs.py` to discover main narrative pages via `rglob` instead of a hardcoded list, preventing silent content loss after doc restructuring; added fail-loud floors for minimum page and content counts.
- Fixed `datetime.now()` call in the version bumper script.
- Fixed `TransmissionProcess.__init__` docstring that incorrectly described `incidence` as a time-series vector; it is a per-tick scalar that resets each tick.

### Breaking Changes

- Removed `verbose` constructor kwarg from all components; `BaseComponent.verbose` is now a dynamic property mirroring `model.params.verbose`, so per-component verbosity overrides are no longer supported.

### Improvements

- Standardized verbose flag so toggling `model.params.verbose` at runtime is immediately reflected in all components without requiring kwarg threading.
- Extracted `_instantiate_component` helper to consolidate component instantiation and `.name` defaulting logic across all three creation sites.
- Increased max tokens on AI response generation for the version bumper.
- CI matrix now uses `py310-ci`/`py314-ci` tox environments with `-m 'not slow'` to prevent transient network failures (e.g., GADM downloads) from blocking PR builds.
- Added Python 3.14 (π-thon) support and bumped doc generation to Python 3.12.
- Required authorization for releases and publishing workflows.
- Added `markdownify` to project dependencies under the `[doc]` extra.

### Documentation

- Added `tutorials/README.md` with a learning-path table of contents for GitHub browsing, describing each tutorial and the `.py` → `.ipynb` pipeline.
- Added `docs/results.md` narrative page describing the `write_results()` JSON output contract, schema field reference, and cross-model comparison example.
- Added snapshotting guide covering workflow, HDF5 structure, component requirements, SIA persistence, and usage examples.
- Removed remaining references to Sphinx and Read the Docs following transition to MkDocs and GitHub Pages.
- Copyedited all documentation files for consistency and clarity.
- Updated `concat_mkdocs.py` summary output to surface per-section ok/skip counters.

### Tests

- Added unit tests for `write_results()` covering full schema validation, global-only tracker mode, missing `StateTracker` error path, and default output path behavior.
- Added parametrized unit tests confirming that bare custom components (no `BaseComponent` inheritance) receive a `.name` attribute for all three creation paths.
- Added unit tests locking in `extra='forbid'` behavior on all `*Params` classes.
- Added comprehensive unit tests for ABM and compartmental snapshot round-trips, SEIR continuity at snapshot boundary, vital dynamics, and vaccination queue rebuild.
- Added tests for `model.params.verbose` dynamic property behavior across all three model variants.

## 0.0.1 (2024-10-18)

* First release on PyPI.

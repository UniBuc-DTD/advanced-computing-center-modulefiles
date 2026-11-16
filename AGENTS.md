# AGENTS.md — modulefile conventions

Module definition files for Environment Modules on the University of Bucharest ACC HPC cluster.

## Structure

- One module per file: `<category>/<name>/<version>` — the filename is the version, no extension.
- Categories: `cuda`, `intel`, `libraries` (plus `libraries/hep`), `mpi`, `nvhpc`, `tools` (plus `tools/hep`).
- A modulefile points at the actual install under the modules root on the cluster (e.g. `/mnt/modules/software/<category>/<name>/<version>`); keep `base`/`prefix` in sync with the real install location.

## Writing modulefiles

- Modulefiles are **Tcl, not Lua**: `#%Module1.0`, `proc ModulesHelp`, `set version`, `module-whatis`, `prepend-path`, `setenv`. Follow the style of an existing file in the same category.
- Default versions come from `<name>/.modulerc` (`module-version <ver> default`). Without one, users must pin the full version (true for gcc, root, madgraph, python, delphes, ...). Add one only when a default is actually wanted — it changes what `module load <name>` resolves to.
- `conflict`/`prereq` use full module paths (`prereq tools/python/3.14`, `conflict libraries/hep/$appname`); partial version prereqs are fine (prefix match). `prereq --optional` marks soft dependencies.
- `intel/` entries are symlinks into the Intel oneAPI install, intentionally git-ignored (`intel/.gitignore` ignores `*`). Don't commit, "fix", or delete them.
- `tools/hep/delphes-parallel/latest`: version is literally `latest`; it captures `which DelphesHepMC2` at load time, which works only because of its `prereq tools/hep/delphes`.

## Verification & commits

- Test with `module load <mod>/<ver>` on a node where this tree is in `MODULEPATH`, then confirm the expected binaries and variables are set.
- Commit messages: imperative, one line ("Set up CUDA v13.4.3", "Add R language support").
- `.gitattributes` normalizes line endings to LF and tags `libraries/`, `mpi/`, `tools/` as Tcl; keep files plain text.

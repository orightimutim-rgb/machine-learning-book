# AGENTS.md

## Cursor Cloud specific instructions

This repo is the code companion for the book *Machine Learning with PyTorch and
Scikit-Learn*. It is **not** a service/web app — there is no server, database, or
build step. Each `chXX/` folder contains Jupyter notebooks (`.ipynb`) and an
equivalent exported Python script (`.py`) that are run interactively for learning.

### Python environment
- Dependencies live in a **conda env named `machine-learning-book`** (Python 3.9),
  created from `environment.yml` and managed by Miniforge at `~/miniforge3`.
- The update script keeps that env in sync with `environment.yml`. `conda` is not
  on a non-interactive shell's PATH by default; use absolute paths:
  - Python: `~/miniforge3/envs/machine-learning-book/bin/python`
  - Pip: `~/miniforge3/envs/machine-learning-book/bin/pip`
  - Jupyter: `~/miniforge3/envs/machine-learning-book/bin/jupyter`
  - Or activate interactively: `conda activate machine-learning-book`.
- **PyTorch is NOT in `environment.yml`** (the book installs it separately). A
  CPU-only `torch` + `torchvision` is pip-installed into the env and persists in
  the snapshot. If you ever need to reinstall:
  `~/miniforge3/envs/machine-learning-book/bin/pip install --index-url https://download.pytorch.org/whl/cpu torch torchvision`

### Running code / "lint, test, build"
- There is **no lint, test, or build system** (no pytest/linters/CI). The closest
  to a test is `python python_environment_check.py`, which verifies installed
  package versions. The only "build" is `./update_python_from_notebook.sh`, which
  regenerates `.py` files from notebooks via `jupyter nbconvert`.
- Run a chapter script from inside its folder with the repo root on `PYTHONPATH`
  (the scripts `import python_environment_check` and read local data files):
  `cd ch13 && MPLBACKEND=Agg PYTHONPATH=/workspace ~/miniforge3/envs/machine-learning-book/bin/python ch13_part2.py`
- **Always set `MPLBACKEND=Agg`** in headless runs; otherwise `plt.show()` emits
  warnings (harmless) and GUI calls can hang. With `Agg` they are no-ops.
- **JupyterLab**: `cd /workspace && ~/miniforge3/envs/machine-learning-book/bin/jupyter lab --no-browser --ip=127.0.0.1 --port=8899 --ServerApp.token="" --ServerApp.password=""`.

### Gotchas
- The `.py` files are auto-converted from notebooks. A few still reference
  notebook-only helpers (e.g. `IPythonImage` in `ch12/ch12_part1.py` and
  `ch12/ch12_part2.py`) and will raise `NameError`/`FileNotFoundError` at those
  lines — this is a pre-existing quirk of the export, not an environment problem.
  The final "convert notebook to script" cell in some notebooks similarly errors
  and is safe to ignore ("Readers may ignore the next cell").
- Some chapters need **optional** extra libraries not installed by default, e.g.
  `mlxtend` (ch13_part1), `transformers` (ch16), `pytorch-lightning`/`torchmetrics`
  (ch13/ch16 bonus), `pytorch-ignite` (ch13), `gym` (ch19). Install per-chapter as
  needed. `networkx` (ch18) is already present (pulled in by torch).
- Many scripts download datasets on demand (e.g. MNIST via torchvision, Iris/Auto-MPG
  from URLs), so network access is required for those chapters. Note `.gitignore`
  ignores `ch12/MNIST` but **not** `ch13/MNIST`; remove any downloaded `chXX/MNIST/`
  data before committing.

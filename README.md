# Zhengzhou-ground-deformation-driver-regimes
Code, workflows, processed data, and supplementary materials for multi-year InSAR and explainable machine learning analysis of urban ground deformation in Zhengzhou, China.


# Zhengzhou deformation analysis

MATLAB and Python code for driver screening, deformation modelling, SHAP-based mechanism partitioning, hydrologic lag analysis, ALE responses and manuscript figures.

[中文说明](README_中文.md) · [Figure index](docs/FIGURES.md) · [Workflow](docs/WORKFLOW.md) · [Environment](environment.txt)

## Contents

| Directory | Purpose |
|---|---|
| `analysis/` | Experimental stages and their numerical routines. Each stage has its own README. |
| `plots/` | Figure code, grouped by the current manuscript figure numbers. |
| `config/` | Shared paths, task registry, experiment templates and figure unit labels. |
| `runtime/` | Shared path resolution, interpreter selection and launch helpers. |
| `data/` | Supplied derived tables, screening workspaces and saved model used by the figures. |
| `preprocessing/` | Raster preparation and coordinate export utilities. |
| `examples/` | Explicitly labelled synthetic deformation scenarios. |
| `legacy/` | Earlier figure candidates retained for comparison; see the figure index. |
| `tools/` | Path configuration and environment checking. |

## Environment

Use Python 3.10 and the versions in `requirements.txt`. XGBoost 3.0.5 and SHAP 0.48.0 match the archived run record. The remaining pins define the compatible environment assembled for this release; they are not presented as a recovered freeze of the original computer.

On Windows, run once from the repository folder:

```bat
setup_environment.bat
```

On Linux or macOS:

```sh
sh setup_environment.sh
```

The setup script creates `.venv` only when it is absent. Subsequent setup calls check the existing environment. Experiment and figure scripts never install packages. For an existing interpreter, set `python_exe` in `config/paths.json` and check it with `tools/check_environment.py`.

MATLAB routines target R2023b. Raster stages use Mapping Toolbox; screening uses Statistics and Machine Learning Toolbox; temporal analysis uses Wavelet Toolbox. Parallel Computing Toolbox is optional for the routines that explicitly enable it. GMT map candidates additionally require GMT 6, GDAL command-line tools and Windows PowerShell.

## Paths and a first figure

The default `project_root` is the bundled `data/` directory. All relative configuration paths are resolved against the repository, irrespective of the current working directory. The default figure destination is `outputs/figures/`.

```bat
.venv\Scripts\python.exe run.py list
.venv\Scripts\python.exe run.py figure Fig14
.venv\Scripts\python.exe run.py figure S7 --no-preview
```

On Linux/macOS, replace `.venv\Scripts\python.exe` with `.venv/bin/python`.

To use an existing project tree:

```bat
.venv\Scripts\python.exe tools\configure.py --project-root "G:\ZHENGZHOU\zhengzhou\1km"
```

External raster and vector locations are separate entries under `external_paths` in `config/paths.json`. `docs/external_inputs.csv` lists their keys. Raw images, source predictor rasters, original GNSS observations and full monthly cache files are not bundled.

In MATLAB, open the repository folder and call:

```matlab
run_project('list')
run_figure('Fig06')
run_figure('Fig10a')
run_project('screen_features')
```

Each MATLAB stage adds only its own helper folder to the search path and restores the previous path afterwards. Do not add the entire repository with `genpath`: several independent experiments intentionally retain local helper functions with the same names.

`preview: true` keeps figure windows open. Python exits after the windows are closed. Use `--no-preview` for unattended Python runs, or set `preview` to `false` in the shared configuration. A noninteractive graphics backend can save files but cannot display a window.

## Experimental stages

| Stage | Input | Main output |
|---|---|---|
| `prepare_features` | Predictor rasters, deformation rate and mask | Long-term descriptors and initial screening workspace |
| `screen_features` | Prepared screening workspace | Driver ranking, subset diagnostics and ablation tables |
| `fit_model` | Predictor rasters and final driver specification | Prepared model table, fitted XGBoost model and SHAP tables |
| `prepare_mechanisms` | Predictor rasters and driver specification | Cross-fit SHAP compositions and initial mixture fits |
| `compare_mechanisms` | Cross-fit SHAP compositions | K=4/K=5 assignments, profiles and comparison tables |
| `prepare_monthly_deformation` | Monthly deformation rasters | Aligned monthly deformation series |
| `prepare_dynamic_cache` | Monthly driver and deformation rasters | Dynamic cube cache |
| `prepare_rate_cache` | Dynamic cache | Centred six-month deformation-rate cache |
| `temporal_m124` | Two monthly caches and K=4 assignments | Discovery/validation windows and lag summaries for M1, M2 and M4 |
| `temporal_m3` | The same caches and K=4 assignments | Runoff relationship summaries for M3 |
| `ale_interactions` | Saved model, prepared model table and K=4 assignments | ALE curves, breakpoint intervals and interactions |
| `mechanism_statistics` | K=4/K=5 outputs | Association, boundary uncertainty, regional diversity and signed SHAP tables |
| `gnss_validation` | Original station observations and InSAR displacement rasters | Matched time series and comparison metrics |

Python entries ending in `_tables` start from prepared CSV files and skip MATLAB raster preparation. The ALE Python backend has separate `ale_inputcheck`, `ale_realdatasmoke`, `ale_selftest`, `ale_threshold` and `ale_interaction` entries. See [the workflow](docs/WORKFLOW.md) before rerunning full analyses.

## Figures and source coverage

The figure index identifies the selected script, data inputs and output name for every main and supplementary figure. Several original script numbers differed from the final manuscript; the dispatcher uses the final numbers.

The package does not claim to recover unavailable source code. The complete Fig. 3 composite and the final Fig. 4 map layout remain unconfirmed; Fig. 5 source code was not found. Fig. 1 is an externally sourced illustration. Identifiable map candidates are in `legacy/`, with their limitations recorded. Code that simulates deformation is labelled as such and is excluded from the experimental task registry.

## Reproducibility and licence

Scientific calculation routines and configured numerical settings were retained. Release changes concern directory organization, portable paths, interpreter handling, code formatting, figure labels and figure entry points. Fig. 10a now reads the saved composition table instead of a rounded, manually transcribed matrix. The GNSS and monthly-deformation input folders are explicit settings; the launchers do not automatically choose a synthetic scenario directory.

The supplied numerical CSVs and saved model are unchanged. Path-bearing JSON files use `{PROJECT}` placeholders, resolved by the dispatcher. Source and data provenance are listed in `docs/source_files.csv` and `docs/data_files.csv`.

Check the supplied model directly with `.venv\Scripts\python.exe tools/verify_derived_inputs.py`.

See [verification notes](docs/VERIFICATION.md) for checks actually performed. This release was not rerun from raw satellite observations, and MATLAB/GMT execution was not available in the verification environment.

The MIT licence applies to the project source code. It does not replace the licences or access conditions of external datasets, software dependencies or published figures. Cite the associated manuscript and the archived code release when reusing the work.

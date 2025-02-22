# kedro-devops

This project intends to demonstrate and define a Dev(ML)Ops pipeline for Kedro

## Setup

To install de project you must have [conda](https://conda.io/projects/conda/en/latest/user-guide/install/index.html) and setup a Python3.7 environment as follows:

```bash
conda create --name kedro-devops python=3.7 -y
```

Then you need to activate your virtualenv

```bash
conda activate kedro-devops
```

> Note: if you are using windows you may need to use a cmd shell instead of a powershell to activate a conda environment

## Overview

This is your new Kedro project, which was generated using `Kedro 0.17.5`.

Take a look at the [Kedro documentation](https://kedro.readthedocs.io) to get started.

## Rules and guidelines

In order to get the best out of the template:

- Don't remove any lines from the `.gitignore` file we provide
- Make sure your results can be reproduced by following a [data engineering convention](https://kedro.readthedocs.io/en/stable/11_faq/01_faq.html#what-is-data-engineering-convention)
- Don't commit data to your repository
- Don't commit any credentials or your local configuration to your repository. Keep all your credentials and local configuration in `conf/local/`

## How to install dependencies

Declare any dependencies in `src/requirements.txt` for `pip` installation and `src/environment.yml` for `conda` installation.

To install them, run:

```
pip install -r src/requirements.txt
```

## How to run Kedro

You can run your Kedro project with:

```
kedro run
```

## How to test your Kedro project

Have a look at the file `src/tests/test_run.py` for instructions on how to write your tests. You can run your tests as follows:

```
kedro test
```

To configure the coverage threshold, look at the `.coveragerc` file.

## Project dependencies

To generate or update the dependency requirements for your project:

```
kedro build-reqs
```

This will copy the contents of `src/requirements.txt` into a new file `src/requirements.in` which will be used as the source for `pip-compile`. You can see the output of the resolution by opening `src/requirements.txt`.

After this, if you'd like to update your project requirements, please update `src/requirements.in` and re-run `kedro build-reqs`.

[Further information about project dependencies](https://kedro.readthedocs.io/en/stable/04_kedro_project_setup/01_dependencies.html#project-specific-dependencies)

## How to work with Kedro and notebooks

> Note: Using `kedro jupyter` or `kedro ipython` to run your notebook provides these variables in scope: `context`, `catalog`, and `startup_error`.

### Jupyter

To use Jupyter notebooks in your Kedro project, you need to install Jupyter:

```
pip install jupyter
```

After installing Jupyter, you can start a local notebook server:

```
kedro jupyter notebook
```

### JupyterLab

To use JupyterLab, you need to install it:

```
pip install jupyterlab
```

You can also start JupyterLab:

```
kedro jupyter lab
```

### IPython

And if you want to run an IPython session:

```
kedro ipython
```

### How to convert notebook cells to nodes in a Kedro project

You can move notebook code over into a Kedro project structure using a mixture of [cell tagging](https://jupyter-notebook.readthedocs.io/en/stable/changelog.html#cell-tags) and Kedro CLI commands.

By adding the `node` tag to a cell and running the command below, the cell's source code will be copied over to a Python file within `src/<package_name>/nodes/`:

```
kedro jupyter convert <filepath_to_my_notebook>
```

> _Note:_ The name of the Python file matches the name of the original notebook.

Alternatively, you may want to transform all your notebooks in one go. Run the following command to convert all notebook files found in the project root directory and under any of its sub-folders:

```
kedro jupyter convert --all
```

### How to ignore notebook output cells in `git`

To automatically strip out all output cell contents before committing to `git`, you can run `kedro activate-nbstripout`. This will add a hook in `.git/config` which will run `nbstripout` before anything is committed to `git`.

> _Note:_ Your output cells will be retained locally.

## Package your Kedro project

[Further information about building project documentation and packaging your project](https://kedro.readthedocs.io/en/stable/03_tutorial/05_package_a_project.html)

## Run your project in Airflow

The easiest way to run your project in Airflow is by [installing the Astronomer CLI](https://www.astronomer.io/docs/cloud/stable/get-started/quickstart#step-4-install-the-astronomer-cli)
and follow the following instructions:

Package your project:

```shell
kedro package
```

Copy the package at the root of the project such that the Docker images
created by the Astronomer CLI can pick it up:

```shell
cp src/dist/*.whl ./
```

Generate a catalog file with placeholders for all the in-memory datasets:

```shell
kedro catalog create --pipeline=__default__
```

Edit the file `conf/base/catalog/__default__.yml` and choose a way to
persist the datasets rather than store them in-memory. E.g.:

```yaml
example_train_x:
  type: pickle.PickleDataSet
  filepath: data/05_model_input/example_train_x.pkl
example_train_y:
  type: pickle.PickleDataSet
  filepath: data/05_model_input/example_train_y.pkl
example_test_x:
  type: pickle.PickleDataSet
  filepath: data/05_model_input/example_test_x.pkl
example_test_y:
  type: pickle.PickleDataSet
  filepath: data/05_model_input/example_test_y.pkl
example_model:
  type: pickle.PickleDataSet
  filepath: data/06_models/example_model.pkl
example_predictions:
  type: pickle.PickleDataSet
  filepath: data/07_model_output/example_predictions.pkl
```

Install the Kedro Airflow plugin and convert your pipeline into an Airflow dag:

```shell
pip install kedro-airflow
kedro airflow create -t dags/
```

Run your local Airflow instance through Astronomer:

```shell
astro dev start
```

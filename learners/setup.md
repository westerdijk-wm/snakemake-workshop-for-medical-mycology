---
title: Setup
---

## Software setup

For this workshop you need to have access to a Unix style command line where you
have can create conda environments.

:::::::::::::::: spoiler

### Windows

Use WSL [Ubuntu](https://apps.microsoft.com/detail/9pdxgncfsczv)

::::::::::::::::::::::::

:::::::::::::::: spoiler

### MacOS

Use Terminal.app

::::::::::::::::::::::::

:::::::::::::::: spoiler

### Linux

Use Terminal

::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::: discussion

If you don't yet have conda installed, then using
[miniforge](https://github.com/conda-forge/miniforge) is a good option.

:::::::::::::::::::::::::::::::::::::::::::::::::::

### Setting up conda environment

Run the following command in the terminal (command line):

```bash
conda create -c conda-forge -c bioconda -c nodefaults --name snakemake snakemake snakedeploy -y
```

This will create the environment that we need and are going to use during the
workshop.

:::::::::::::::::::::::: solution

If all goes well you will see that your environment is created and how it can be
activated.

```text
Downloading and Extracting Packages:

Preparing transaction: done
Verifying transaction: done
Executing transaction: done
#
# To activate this environment, use
#
#     $ conda activate test-snakemake
#
# To deactivate an active environment, use
#
#     $ conda deactivate

```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::: discussion

### Optional editor

It is recommended that you use [Visual Studio Code](https://code.visualstudio.com/)
for working through this material, because it integrates the terminal, file structure
and file editing nicely together.

:::::::::::::::::::::::::::::::::::::::::::::::::::

<!--
## Data Sets

FIXME: place any data you want learners to use in `episodes/data` and then use
       a relative link ( [data zip file](data/lesson-data.zip) ) to provide a
       link to it, replacing the example.com link.

Download the [data zip file](https://example.com/FIXME) and unzip it to your Desktop
-->


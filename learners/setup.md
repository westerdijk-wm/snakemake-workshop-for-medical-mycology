---
title: Summary and Setup
---

This lesson is developed for medical mycologists. There are two main goals with
this learning material:

- teach users how to use existing snakemake workflows and use their data
- give example workflows for the most common questions in medical mycology when it comes to genomics

## Software setup

For this workshop you need to have access to a Unix style command line where you
have can create **conda environments**.

> [!NOTE]
> Conda is an environment manager that allows you to install the specific versions of each package needed to run the analyses

:::::::::::::::: spoiler

### Windows

Use the Windows Subsystem for Linux ([more information](https://learn.microsoft.com/en-us/windows/wsl/about))

[Install the WSL with Ubuntu](https://apps.microsoft.com/detail/9pdxgncfsczv)

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

conda is probably installed in your system already. To check, type the following in a terminal, followed by Enter:
```bash
conda -V
```

If you got an error, you don't yet have conda installed, then using
[miniforge](https://conda-forge.org/download/) is a good option.

:::::::::::::::::::::::::::::::::::::::::::::::::::

### Setting up conda environment

Run the following command in the terminal (command line) to create an environment called **snakemake**:

```bash
conda create -c conda-forge -c bioconda -c nodefaults --name snakemake snakemake snakedeploy -y
```

This will create the environment that we need and are going to use during the
workshop.


::::::::::::::::::::::::::::::::::::: challenge


If all goes well you will see that your environment is created and how it can be
activated.


:::::::::::::::::::::::: solution


```text
Downloading and Extracting Packages:

Preparing transaction: done
Verifying transaction: done
Executing transaction: done
#
# To activate this environment, use
#
#     $ conda activate snakemake
#
# To deactivate an active environment, use
#
#     $ conda deactivate

```


:::::::::::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::::: discussion


### Optional: code editor

It is recommended that you use [Visual Studio Code](https://code.visualstudio.com/)
for working through this material, because it integrates the terminal, file structure
and file editing nicely together.


:::::::::::::::::::::::::::::::::::::::::::::::::::


## Data Sets

You can access expected output data from [https://knaw.data.surf.nl/s/yXEJKWY2KRADkx5](https://knaw.data.surf.nl/s/yXEJKWY2KRADkx5).
The password will be shared during the workshop.


---
title: "Introduction to Snakemake"
teaching: 10 # teaching time in minutes
exercises: 2 # exercise time in minutes
---

:::::::::::::::::::::::::::::::::::::: questions 

- What is Snakemake?
- Why choose Snakemake?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Explain how workflow managers contribute to reproducibility and reusability
- Explain whether Snakemake is a good choice for the given task

::::::::::::::::::::::::::::::::::::::::::::::::

# What is Snakemake?

> [Official Snakemake documation](https://snakemake.readthedocs.io/en/stable/)

Snakemake is workflow management system, a tool to create reproducible and scalable data analyses.
Workflows are described via a human readable, Python based language.
They can be seamlessly scaled to server, cluster, grid and cloud environments, without the need to modify the workflow definition.
Finally, Snakemake workflows can entail a description of required software, which will be automatically deployed to any execution environment.

## How can you use Snakemake?

Use cases:  

- You can create new workflow:
    - You can write up an existing "manual" workflow
    - You can write up a specific analysis for reproducibility
    - You can build the Snakemake file (workflow) while you are analyzing your data
- You can use existing workflows for your data
- You can use existing workflows that you expand further on

::::::::::::::::::::::::::::::::::::: callout

It can be handy to use Snakemake to explore or analyze your data, because then you
will have a reproducible analysis. It will be also clear what kind of steps you have taken.
Snakemake can check if intermediary results have changed as you optimize different steps.

Snakemake creates outputs in your working directory in a way you would create them
by running the steps manually (unlike nextflow). This can help with learning and
adapting in a research environment.

::::::::::::::::::::::::::::::::::::::::::::::::

# Using existing workflows

In this workshop, we are going to be using existing workflows for our analysis.
The goal is to educate users in how to us workflows and adjust inputs and configurations
to meet their needs. Creating workflows is considered as development is more advanced
than reusing existing workflows. Therefore, we will not create our own workflows.


## Finding workflows to use

Most workflows are published on Github, and you can search there.
The Snakemake project is also has a webpage [snakemake-workflow-catalog](https://snakemake.github.io/snakemake-workflow-catalog/docs/all_standardized_workflows.html)
where you can find workflows.

We will be using the following workflows during this workshop:

- [westerdijk-wm/snakemake-mlsa-ani](https://github.com/westerdijk-wm/snakemake-mlsa-ani)
    + Snakemake workflow for MLSA (multi locus sequence analysis) and ANI analysis
- [b-brankovics/bwa-gatk-fasttree-smkwf](https://github.com/b-brankovics/bwa-gatk-fasttree-smkwf/)
    + Variant calling workflow for creating SNP phylogenies
- [westerdijk-wm/smkwf-bwa-gatk-snpeff](https://github.com/westerdijk-wm/smkwf-bwa-gatk-snpeff)
    + A snakemake workflow for mapping, variant-calling and annotating variants using SNPeff 

These example cover the most important methods in genomics for medical mycology:

- MLSA for typing, identification and relatedness analysis
- Phylogenomics: genome based relatedness analysis for outbreak investigation
- Variant annotation in resistance genes: to get the an idea of the level of resistance based on known mutations in known genes

## Setting up the workflow for your analysis

A well designed workflow includes documentation who you should use it.

::::::::::::::::::::::::::::::::::::: challenge 

### Challenge 1: Understanding documentation

Can you find how you should setup a run for [westerdijk-wm/snakemake-mlsa-ani](https://github.com/westerdijk-wm/snakemake-mlsa-ani)?

:::::::::::::::::::::::: solution 

It is found [here](https://github.com/westerdijk-wm/snakemake-mlsa-ani#installation):

### Option 1 — Clone with Git

Ensure Conda and Git are installed, then:

```bash
git clone https://github.com/westerdijk-wm/snakemake-mlsa-ani.git
cd snakemake-mlsa-ani
conda env create -f workflow/envs/mlsa.yaml
conda activate snake-mlsa-ani
```

### Option 2 — Deploy with Snakedeploy

[Snakedeploy](https://snakedeploy.readthedocs.io) deploys the workflow into
any working directory without cloning the full repository, keeping your data
and workflow code cleanly separated:

```bash
conda install bioconda::snakedeploy
snakedeploy deploy-workflow \
    https://github.com/westerdijk-wm/snakemake-mlsa-ani . --branch main
```

This downloads the workflow files and creates a `config/` directory with
template configuration files ready to edit.

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

Great, now you know how you can obtain and run the workflow.
In the next episode, we will use this workflow, and learn more about
the user aspects of snakemake workflows.

::::::::::::::::::::::::::::::::::::: keypoints 

- Know what Snakemake is and where to find more information ([Official Snakemake documentation](https://snakemake.readthedocs.io/en/stable/))
- Know where to find workflows: [snakemake-workflow-catalog](https://snakemake.github.io/snakemake-workflow-catalog/docs/all_standardized_workflows.html)
- Know how to find setup instructions

::::::::::::::::::::::::::::::::::::::::::::::::



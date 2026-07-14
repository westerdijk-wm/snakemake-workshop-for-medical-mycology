---
title: "Using Snakemake workflows, MLSA"
teaching: 20 # teaching time in minutes
exercises: 10 # exercise time in minutes
---

:::::::::::::::::::::::::::::::::::::: questions 

- Where to look for user information in standardized workflows?
- How to run workflows locally?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Identify what are the input parameters and data of a workflow
- Locate which output files will be created
- Execute existing workflow locally

::::::::::::::::::::::::::::::::::::::::::::::::


# Introduction

In this, episode we will explore the [westerdijk-wm/snakemake-mlsa-ani](https://github.com/westerdijk-wm/snakemake-mlsa-ani)
workflow as an example. It helps automate multilocus sequences analysis (MLSA), which is
a standard method in fungal phylogeny (also used for other organisms).

## Workflow overview according to documentation

**snakemake-MLSA-ANI** is a reproducible Snakemake workflow designed to
generate phylogenetic and genomic similarity analyses from assembled genomes.

Starting from genome assemblies, the pipeline:

- validates the reference gene database
- assesses genome assembly quality (QUAST)
- extracts homologous loci using reference sequences (minimap2)
- performs locus-level quality control
- generates multiple sequence alignments (MUSCLE)
- concatenates loci into MLSA supermatrices
- infers phylogenetic trees (IQ-TREE, RAxML, or FastTree)
- optionally computes ANI between genomes (skani, FastANI, or pyANI)
- optionally downloads and incorporates public genomes from NCBI

Originally developed for fungal phylogenetics, but applicable to any organism
with suitable reference loci.

# Deploying a workflow locally

The workflow is available through Github at
[https://github.com/westerdijk-wm/snakemake-mlsa-ani](https://github.com/westerdijk-wm/snakemake-mlsa-ani)
in order to run it locally, we need to get a local copy on our computer first.
For this, we will use `snakedeploy`.

::::::::::::::::::::::::::::::::::::: callout

Remember to setup a `snakemake` environment specified in the

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge 

### Exercise 1: Deploy workflow for local execution

Create a directory, `mlsa` that we will use for this episode,
and change to that directory.

```bash
mkdir mlsa
cd mlsa
```

Then we need to activate our conda environment `snakemake`,
so we can use `` to get the workflow locally.

```bash
conda activate snakemake
snakedeploy deploy-workflow \
    https://github.com/westerdijk-wm/snakemake-mlsa-ani . --tag v1.0.0
```

:::::::::::::::::::::::: solution 

### Output

Your terminal output should end with the following:

```text
Writing template configuration...
Writing template profiles
Writing license
Writing Snakefile with module definition...
Deployment complete, now

  * modify the configuration under ./config
  * check and extend the workflow deployment under ./workflow/Snakefile

according to your needs.
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

## Explore input parameters and files

You can see that `snakedeploy` mentions what we can do as follow-up:

* modify the configuration under `./config`
* check and extend the workflow deployment under `./workflow/Snakefile`

The second option, is for modifying or extending the workflow, we will not do that in
this workshop.

Let's check the `./config` (`./` actually means in this directory, so we could also say `config`):

```bash
ls config/
```

This will list the following files:

- `config/config.yaml`
- `config/public_genomes.txt`
- `config/README.md`
- `config/ref-genes.fas`

In general, README files are the good place to start with. They are meant for humans to read.
However, the online version is nicer to read: [config/README.md](https://github.com/westerdijk-wm/snakemake-mlsa-ani/blob/main/config/README.md)


::::::::::::::::::::::::::::::::::::: challenge 

### Exercise 2: Can you locate the reference sequences?

Checking the README can you find where the reference sequences are stored?

:::::::::::::::::::::::: solution 

### Answer

The can be found in `config/ref-genes.fas`.

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

Here is a summary over the important input files and directories for this workflow:

| Input | Location | Role in the workflow |
|---|---|---|
| Local genome assemblies | `genomes/` | One assembled genome per sample. The sample name is derived from the file name. |
| Reference gene database | `config/ref-genes.fas` | FASTA file containing the reference loci to search for in each genome. |
| Workflow configuration | `config/config.yaml` | Defines selected genes, tree method, ANI option, and public genome settings. |
| Optional public genome list | `config/public_genomes.txt` | Lists NCBI assembly accessions that should be downloaded and included. |

::::::::::::::::::::::::::::::::::::: challenge 

### Exercise 3: Which loci will be analyzed?

According to the README `config/config.yaml` specifies which genes will be used.

```bash
head config/config.yaml
```

1. Which loci are listed?
2. Do these gene names also appear in the reference gene FASTA headers?
3. Why is exact spelling important here?

:::::::::::::::::::::::: solution

### Expected reasoning

Genes that will be analyzed are:

- calmodulin
- actin
- rpb2
- benA

To check that the names occur in the reference gene database, inspect the FASTA
headers:

```bash
# Print lines starting with >
grep '^>' config/ref-genes.fas | head
```

The workflow expects headers of the form:

```text
>{strain}|{gene} optional description
```

Exact spelling matters because the workflow uses these names to decide which
sequences belong to each locus. If `benA` is listed in the configuration but the
reference database uses a different spelling, that locus will not be matched as
intended.

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::


## Running or inspecting the workflow

It is often useful to begin with a dry run. A dry run asks Snakemake
what it would do without executing the commands.

```bash
snakemake --dry-run
# Or 
snakemake -n
```

Since we did not modify anything in the workflow, this shows what is the default run
of the workflow with default input (refernce genes and public genomes).

This workflow is comes with a default `profile` which means certain parameters are pre defined for use.
So our above command is equal to running:

```bash
snakemake -c 10 --use-conda -n
```

::::::::::::::::::::::::::::::::::::: callout

## Dry runs are part of reproducible analysis

A dry run is not only a safety check. It also helps you understand how the workflow
connects inputs to outputs. Before running a workflow on new data, it is good
practice to ask:

- Which files will be created?
- Which rules will run?
- Which existing results will be reused?
- Does the requested target match the biological question?

::::::::::::::::::::::::::::::::::::::::::::::::

We can see that a total of 90 steps will be executed. Let's run the workflow now:

```bash
snakemake --cores 10 --use-conda
```

## Exploring the output of the workflow

Output files are not necessarily well standardized or described for workflows.
In this case, the Github version describes the [results/outputs](https://github.com/westerdijk-wm/snakemake-mlsa-ani/blob/main/docs/outputs.md)

Otherwise the snakemake log or the dry-run information can help you find the relevant output.

This workflow also allows for generating a report, so let's do that and explore the results that way.

```bash
snakemake --report report.html
```

Then open `report.html` in a browser to check the results. The most important are the following:


| Output | Meaning |
|---|---|
| `results/QC/gene-qc-matrix.pdf` | Visual summary of the gene QC matrix. |
| `results/phylogenetics/MLSA.nwk` | Final rerooted MLSA tree in Newick format. |
| `results/phylogenetics/MLSA_tree.pdf` | Visualization of MLSA tree. | 
| `results/ANI/skani/skani.pdf` | MLSA tree combined with ANI heatmap. |

With this you have successfully run an external snakemake workflow by first
using snakedeploy, checking the input parameters and then executing it. 

::::::::::::::::::::::::::::::::::::: keypoints 

- Know where to find input parameters and files for a workflow
- Know how to deploy a local copy of a workflow
- Know what a dry run is
- Know how to run a workflow
- Know how to find the output files

::::::::::::::::::::::::::::::::::::::::::::::::



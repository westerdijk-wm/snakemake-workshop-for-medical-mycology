---
title: "Resistance mutations analysis"
teaching: 20 # teaching time in minutes
exercises: 10 # exercise time in minutes
---

:::::::::::::::::::::::::::::::::::::: questions 

- How do workflows build on each other?
- How to execute workflows in different folders?
- How to generate publication ready mutation overview?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Execute variant calling workflow for resistance mutation analysis
- Compare related workflows
- Identify whether mutations confer resistance or not

::::::::::::::::::::::::::::::::::::::::::::::::


# Introduction

For the final workflow in this workshop, we are going to use workflow that
also uses read-mapping and variant calling like the previous one.
Here we also want to look at the impact of the variants to gene
sequences. We will be looking at genes that when mutated in given ways
confer resistance to the strain against certain antifungals.


## Variant annotation workflow

For this episode we are going to use the [westerdijk-wm/smkwf-bwa-gatk-snpeff](https://github.com/westerdijk-wm/smkwf-bwa-gatk-snpeff)
workflow. This workflow overlaps with the previous one, so it is a good example to
explore how different workflows differ from each other.


::::::::::::::::::::::::::::::::::::: challenge 

### Challenge 1: download the pipeline

Let's move out of the working directory of the previous episode if
you are still there.

For this example we actually will download the whole repository (source) of the workflow.
This is an alternative way, for running workflows locally.

You can do this like this:

```bash
git clone https://github.com/westerdijk-wm/smkwf-bwa-gatk-snpeff
```

This downloads the workflow (the same way we did for the test dataset).

Change to the new directory, and check the content of the directory.

:::::::::::::::::::::::: solution 

#### Solution

```bash
cd smkwf-bwa-gatk-snpeff
ls
```

Output:

```output
CHANGELOG.md  config  LICENSE  profiles  README.md  workflow
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

If you look at the directory content then it looks very similar to what we have seen
previously after deploy. Actually there are some "hidden" folders and files.
On Unix or linux systems, hidden objects start with `.` and are by default not
shown, hence hidden.

::::::::::::::::::::::::::::::::::::: challenge 

### Challenge 2: check hidden files

Run the following command to see all the files and folders:

```bash
ls -a
```

:::::::::::::::::::::::: solution 

#### Output

```output
.  ..  CHANGELOG.md  config  .git  .github  .gitignore  LICENSE  profiles  README.md  .snakemake-workflow-catalog.yml  .test  workflow
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

If you remember `snakedeploy` always prints the following:


* modify the configuration under `./config`
* check and extend the workflow deployment under `./workflow/Snakefile`

Actually, this workflow is built on top of the previous one. And we can see this if
we check `workflow/Snakefile` and compare it to how a deployment from the previous
episode looks like.


::::::::::::::::::::::::::::::::::::: challenge 

### Challenge 3: Compare Snakefiles

Compare the content of the two Snakefiles:

- `workflow/Snakefile` - of this workflow
- `../phylogenomics/workflow/Snakefile` - the deployed version of the previous one

What do you see? Can you spot how the previous workflow was deployed and then modified?

> You don't have to understand the details, just spot the general change.

:::::::::::::::::::::::: solution 

#### Expected reasoning

You can compare files side by side on the command line using `diff -y`

```bash
diff -y workflow/Snakefile ../phylogenomics/workflow/Snakefile 
```

On the left, you see the current workflow, and the right side shows the deployed one
form the previous episode (here are the first 40 lines):

```output
from snakemake.utils import min_version				from snakemake.utils import min_version

							      >
min_version("6.10.0")						min_version("6.10.0")


configfile: "config/config.yaml"				configfile: "config/config.yaml"


# declare https://github.com/b-brankovics/bwa-gatk-fasttree-s	# declare https://github.com/b-brankovics/bwa-gatk-fasttree-s
module bwa_gatk_fasttree_smkwf:					module bwa_gatk_fasttree_smkwf:
    snakefile:						      |	    snakefile: 
        github(						      |	        github("b-brankovics/bwa-gatk-fasttree-smkwf", path="
            "b-brankovics/bwa-gatk-fasttree-smkwf",	      <
            path="workflow/Snakefile",			      <
            branch="main",				      <
        )						      <
    config:							    config:
        config							        config


include: "rules/common.smk"				      <
							      <
							      <
# use all rules from https://github.com/b-brankovics/bwa-gatk	# use all rules from https://github.com/b-brankovics/bwa-gatk
use rule * from bwa_gatk_fasttree_smkwf exclude all	      |	use rule * from bwa_gatk_fasttree_smkwf
							      <
							      <
rule all:						      <
    default_target: True				      <
    input:						      <
        "results/snpeff/all.snvs.selected.vcf",		      <
        "results/snpeff/mutation_table_snvs_wide.tsv",	      <
        "results/snpeff/gene_mutations_all.tsv",	      <
							      <
							      <
rule get_gbk:						      <
    """							      <
    Download data from NCBI and place it to correct location  <
    """							      <
```

You can see that both have a similar block but different formatting that
contains: `github("b-brankovics/bwa-gatk-fasttree-smkwf"`

Then the difference starts with importing the rules:

```python
use rule * from bwa_gatk_fasttree_smkwf
```

This is the modified version:

```python
use rule * from bwa_gatk_fasttree_smkwf exclude all
```

Which is followed by some new rules that are unique to this workflow.

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

We can see here an example of how a workflow can be modified and extended.

To further explore how these workflows differ let's look at their Rule Graphs.


::::::::::::::::::::::::::::::::::::: callout

### Rule graphs

Rule graphs are a way of visualizing the logic or flow of a workflow by
displaying the rules that are executed and how they interact with each other.
This is a good way of checking what a workflow does.

Standardized workflows have `Workflow Rule Graph` section in the catalog.

::::::::::::::::::::::::::::::::::::::::::::::::

Compare the two rule graphs:

- [https://snakemake.github.io/snakemake-workflow-catalog/docs/workflows/b-brankovics/bwa-gatk-fasttree-smkwf#workflow-rule-graph](https://snakemake.github.io/snakemake-workflow-catalog/docs/workflows/b-brankovics/bwa-gatk-fasttree-smkwf#workflow-rule-graph)
- [snakemake.github.io/snakemake-workflow-catalog/docs/workflows/westerdijk-wm/smkwf-bwa-gatk-snpeff#workflow-rule-graph](snakemake.github.io/snakemake-workflow-catalog/docs/workflows/westerdijk-wm/smkwf-bwa-gatk-snpeff#workflow-rule-graph)

You can see that the difference happens after `select_pass_calls`.

## Resistance mutations in _A. fumigatus_

For this example, we will use the same _A. fumigatus_ set as for the previous episode.
You can compare the default input is essentially identical to what we created in the
previous episode (the order of samples may differ).

The most studied topic at the moment in  _A. fumigatus_ is its resistance to
(tri)azoles. This resistance is usually due to mutations in the _cyp51A_ (_erg11_)
coding sequence or promotor region. This data set includes:
- wildtype
- non-resistant mutations
- resistant point mutations
- tandem repeat insertion in the promoter
- resistance mutations in other genes


::::::::::::::::::::::::::::::::::::: challenge 

### Challenge 4: Run the analysis

Although, we use `git clone` instead of `snakedeploy` we can run the analysis
in the same way we have learned.

Can you check the dry-run and then execute the workflow? Below we will check an alternative run option.

:::::::::::::::::::::::: solution 

#### Solution

```bash
snakemake -n
```

Then run:

```bash
snakemake --use-conda -p -c 8
```

Or just `snakemake`

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

## Changing execution directory

An alternative, way of running a workflow is to specify in which directory
it should run. This will not only change where the output is generated, but
also where the workflow will look for input information.

This can be powerful when you need to run a given workflow for multiple data sets,
so you don't need to download/deploy the workflow multiple times.


::::::::::::::::::::::::::::::::::::: challenge 

### Challenge 5: Run workflow in .test directory

To run the workflow in a different working directory we need to only
tell snakemake which ones to use with the (`--dir <path>`) option.

Can you guess what the command is for a dry-run and a run?

:::::::::::::::::::::::: solution 

#### Solution

```bash
snakemake --dir .test/ -n
```

and

```bash
snakemake --dir .test/
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

Check the `.test` directory so you see what will be used for the run.
You can see the usually `config` folder with its contents. Plus `data`
folder with reads.

Run the workflow, and we will look at the results. Remember that it output will be inside `.test`.

## Examining resistance mutation data

Variants data are stored in VCF/BCF files which are relatively hard to read.
The workflow generates these variant files, then it annotates the impact of the
variants: do they cause, frameshift, non-sense mutation, etc. This workflow
then generates more user friendly outputs that we can check if we see relevant mutations.

The main output of the workflow is `.test/results/snpeff/gene_mutations_all.tsv`.
This is similar to what you can see in papers: e.g. [Chen _et al._ 2020](https://www.frontiersin.org/journals/microbiology/articles/10.3389/fmicb.2019.03127/full#T1)

We will use _cyp51A_ as the main example as it is the most relevant, as mentioned above.
We need to check if the mutations that we see are novel or known, and if it is known
whether they help resistance or not.


::::::::::::::::::::::::::::::::::::: challenge 

### Challenge 6: Identify resistance conferring mutations

Some of the mutations are discussed in the above publication ([Chen _et al._ 2020](https://www.frontiersin.org/journals/microbiology/articles/10.3389/fmicb.2019.03127/full#T1)).
You can search the literature for these mutations or combination of mutations.

One source is the [Mardy database](https://www.mardy.net).

Get an overview of _cyp51A_ mutation in our data and check which is relevant for resistance.

:::::::::::::::::::::::: solution 

#### Solution

You can search the [Mardy database](https://www.mardy.net) per organism and you can filter results
for genes and mutations.

Check which mutations are present:

```bash
cut -f1,4 .test/results/snpeff/gene_mutations_all.tsv
```

This prints the sample and its _cyp51A_ genotype:

```output
Sample	cyp51A
09-7500806	Wildtype
12-7505446	TR34/L98H
56C17	TR46/Y121F/T289A
CM7632	F46Y/M172V/N248T/D255E/E427K
V226-02	G54R
```

There are 3 relevant mutation sets:

- TR34/L98H
- TR46/Y121F/T289A
- G54R

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

We can also look at another fungicide, methyl benzimidazole carbamate (MBC).
One of the known, resistance mutations is in _benA_ **F219Y**.

::::::::::::::::::::::::::::::::::::: challenge 

### Challenge 7: Check which sample is resistant to MBC

The gene _benA_ encodes beta tubulin, and in some fungi it is called _tub2_.
(Depending on the version of the dataset you use it will be one of these.)

Which sample carries the resistance allele?

:::::::::::::::::::::::: solution 

#### Solution

Sample `56C17` has the `F219Y` mutation.

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::: keypoints 

- Know how to copy the full workflow (`git clone`)
- Know how workflows can build on each other
- Know how to run a workflow in a different working directory
- Know where to find in resistance mutation information

::::::::::::::::::::::::::::::::::::::::::::::::

---
title: "Phylogenomics workflow"
teaching: 20 # teaching time in minutes
exercises: 10 # exercise time in minutes
---

:::::::::::::::::::::::::::::::::::::: questions 

- What is phylogenmics?
- How to adjust a workflow from defaults to our desired input data?
- What are workflow diagrams?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Practice the documentation exploration skills
- Explore workflow documentation to understand configurations and input data
- Change those configuration and input data to fit the need of an analysis

::::::::::::::::::::::::::::::::::::::::::::::::


<!--
Docker notes
docker  run --rm -ti -v $PWD:/workspace brankovics/snakemake <command>


-->

# Introduction

In the previous episode, we looked at a workflow, explored the documentation,
examined the default inputs, ran it with the defaults, and examined the results.
Now we are going to take another workflow, where we are also going to change
input data to match what we need.

As an example, we will cover another important analysis method that is frequently used:
phylogenomics or SNP-trees. This is a tool that can help us explore the genetic relatedness
of isolates, which can be crucial in investigating potential outbreak events and to use it
for source tracing.

## What is phylogenomics?

The name itself refers to phylogenetics based on genomics. Classically, phylogentics
is based on a single or a couple of loci, usually obtained by PCR and sanger sequencing.
Phylogenomics aims to use the "full" genome instead.

There are three major groups within phylogenomics: 1) based on genome-wide SNP alignment,
2) based on orthologous gene alignments, and 3) alignemnt free based methods (e.g.
kmer similarity). We are going to use the first approach, because it is easy to standardize
and is one of the modern standards. The second option requires more preparation and is
more sensitive to annotation mistakes. The alignment free methods are used more for
initial assessment and result in less reliable results, but makes the analysis
computationally significantly lighter and require less storage.


# Explore b-brankovics/bwa-gatk-fasttree-smkwf

The workflow for this episode is [b-brankovics/bwa-gatk-fasttree-smkwf](https://github.com/b-brankovics/bwa-gatk-fasttree-smkwf).

Let's check the documentation of [Github](https://github.com/b-brankovics/bwa-gatk-fasttree-smkwf)
and at the [Snakemake workflow catalog](https://snakemake.github.io/snakemake-workflow-catalog/docs/workflows/b-brankovics/bwa-gatk-fasttree-smkwf).

We can see the brief description: "_Variant calling workflow for creating SNP phylogenies_"

::::::::::::::::::::::::::::::::::::: challenge 

## Challenge 1: What are the input data for the workflow?

Look at the workflow documentation and identify where are the input
is defined. What are the 2-3 most input parameters sources?

> Hint: Phylogenomics relies on read mapping that requires a reference
genome and sequencing reads for the samples to be analyzed.

:::::::::::::::::::::::: solution 

### Expected answer

Reference genome is defined in `config/config.yaml` in the following section:

```yaml
ref:
  # NCBI/ENA/DBJ assembly accession, e.g. GCA_000001405.28
  accession: GCF_000185945.1
```

In this case, the reference is [GCF_000185945.1](ncbi.nlm.nih.gov/assembly/GCF_000185945.1).
If you check online than you can see that it is _Cryptococcus gattii_ WM276 genome assembly ASM18594v1.

The input data is defined through `config/config.yaml` in the following part:

```yaml
samples: config/samples.tsv
units: config/units.tsv
```

So the actual information is `config/units.tsv` where the input is defined.
Samples is just a list of samples from units to be analyzed.
(This is useful, because the units file requires some work to create,
and having a separate selection for the actual samples to analyze
allows you to change which samples to include, if you find out that some
are other species or that they are bad quality.)

So input reads are defined as follows:

```text
sample	unit	platform	fq1	fq2
CBS11687	1	ILLUMINA	resources/reads/SRR7345539_1.fastq.gz	resources/reads/SRR7345539_2.fastq.gz
MF46	1	ILLUMINA	resources/reads/SRR7345548_1.fastq.gz	resources/reads/SRR7345548_2.fastq.gz
MF34	1	ILLUMINA	resources/reads/SRR7514423_1.fastq.gz	resources/reads/SRR7514423_2.fastq.gz
MF13	1	ILLUMINA	resources/reads/SRR7514425_1.fastq.gz	resources/reads/SRR7514425_2.fastq.gz
MF54	1	ILLUMINA	resources/reads/SRR7514424_1.fastq.gz	resources/reads/SRR7514424_2.fastq.gz
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

So we can see that the default data set are of 
_Cryptococcus gattii_ species complex. It uses a public genome as reference,
and the input data is also public reads. We can recognize this by the IDs in the file
names `SRR###` (`SRR` stands for NCBI SRA, `ERR` stands for EBI SRA, `DRR` stands for DDBJ SRA).

If you check the documentation, you can see that "If the read files (fq1 and fq2) follow the
following naming convention `resources/reads/<SRA_ID>_[12].fastq.gz` and they are not actually
at the given path, then they will be downloaded from SRA DB automatically."
This means we need to write the input specification using this scheme to add public data.

## Deploy the workflow locally

::::::::::::::::::::::::::::::::::::: challenge 

## Challenge 2: Use snakedeploy to download the workflow

Let's move out of the working directory of the previous episode if
you are still there, create a new directory `phylogenomics`,
change to `phylogenomics` and deploy the workflow.

:::::::::::::::::::::::: solution 

### Solution


```bash
mkdir phylogenomics
cd phylogenomics
snakedeploy deploy-workflow https://github.com/b-brankovics/bwa-gatk-fasttree-smkwf . --branch main
```

An alternative way is to create the directory through `snakedeploy`:

```bash
snakedeploy deploy-workflow https://github.com/b-brankovics/bwa-gatk-fasttree-smkwf phylogenomics --branch main
cd phylogenomics
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

We have a local copy, where we can change the input data.

## Adjust input data

For our analysis at the moment, we would like to explore some _Aspergillus fumigatus_
samples and look at their relatedness. There are two genomes that are frequently used
as reference genomes for _A. fumigatus_, Af293 and A1163. The A1163 strain has
a "wildtype" genome for many of its genes, and the next episodes looks at gene mutations,
so let's use A1163 strain as reference.

::::::::::::::::::::::::::::::::::::: challenge 

### Challenge 3: Update the reference genome

1. You need to find the identifier of the genome if A1163.
2. You need to update the configuration to use this genome ID.

:::::::::::::::::::::::: solution 

#### Solution

You can find the ID at the [NCBI genome site](https://www.ncbi.nlm.nih.gov/datasets/genome/),
publications or searching online. Although NCBI/EBI/DDBJ are the sources of truth in this case.

Your `config/config.yaml` should look like this:

```yaml
ref:
  # NCBI/ENA/DBJ assembly accession, e.g. GCA_000001405.28
  accession: GCA_000150145.1
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

The reference has been changed, now we need to change the input reads.
For our example we want to use the following reads:

| sample     | SRA ID      |
| ---        | ---         |
| 12-7505446 | ERR769500   |
| V226-02    | ERR12832798 |
| 56C17      | ERR12832723 |
| 09-7500806 | ERR769502   |
| CM7632     | SRR7418941  |
| CMMS340    | SRR36017306 |
| CMMS341    | SRR36017305 |
| CMMS342    | SRR36017304 |
| V254-50    | ERR12832815 |


::::::::::::::::::::::::::::::::::::: challenge 

### Challenge 4: Update the input reads

Can you update the input reads based on the documentation,
the default data and using the above table?

Update or create a new `config/units.tsv` file.

:::::::::::::::::::::::: solution 

#### Solution

The `config/units.tsv` or other TSV file should look like this:

```text
sample	unit	platform	fq1	fq2
CMMS340	1	ILLUMINA	resources/reads/SRR36017306_1.fastq.gz	resources/reads/SRR36017306_2.fastq.gz
CMMS341	1	ILLUMINA	resources/reads/SRR36017305_1.fastq.gz	resources/reads/SRR36017305_2.fastq.gz
CMMS342	1	ILLUMINA	resources/reads/SRR36017304_1.fastq.gz	resources/reads/SRR36017304_2.fastq.gz
12-7505446	1	ILLUMINA	resources/reads/ERR769500_1.fastq.gz	resources/reads/ERR769500_2.fastq.gz
V226-02	1	ILLUMINA	resources/reads/ERR12832798_1.fastq.gz	resources/reads/ERR12832798_2.fastq.gz
56C17	1	ILLUMINA	resources/reads/ERR12832723_1.fastq.gz	resources/reads/ERR12832723_2.fastq.gz
09-7500806	1	ILLUMINA	resources/reads/ERR769502_1.fastq.gz	resources/reads/ERR769502_2.fastq.gz
V254-50	1	ILLUMINA	resources/reads/ERR12832815_1.fastq.gz	resources/reads/ERR12832815_2.fastq.gz
CM7632	1	ILLUMINA	resources/reads/SRR7418941_1.fastq.gz	resources/reads/SRR7418941_2.fastq.gz
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

Awesome, we are almost set. There are 2 more things to check.
Remember that `config/config.yaml` defines which unit file to use.
If you created a new file that has a different name or relative path,
then you need to update that in `config/config.yaml`.

The final check is that our samples are selected for the analysis.
Now you know that it can be some work to generate a units file, and
why you don't want to just through away your work when you want to
(maybe temporarily) exclude a sample. So the final check is to change,
the samples that will be analyzed, we want to include everything from
our units file.

We can do it the following way:

```bash
cat config/units.tsv | cut -f1 > config/samples.tsv
```

This reads, in the units file, selects the first column (before the first tab)
and saves it to the samples file.

> Actually, the only column checked in the samples files is `sample`,
> so we could use the units file, and even change the `config/config.yaml`
> to use it as samples (`samples: config/units.tsv`).

## Running the analysis

Great work, you have changed the default input to your desired input.
Now, you are ready to run your analysis. In the previous episode,
we already learned how to do it. Let's see how it goes.


::::::::::::::::::::::::::::::::::::: challenge 

### Challenge 5: Check what the workflow will do

These kind of worklfows are computationally intensive, use a lot
of resources and take a long time. So it is a good idea to
check that the proper data will be used.

We have seen that a dry run is good way to check what would happen
if we ran the workflow. Do you remember how to do it? If not
do you know where to find the information. (You can always look things up.)

:::::::::::::::::::::::: solution 

#### Solution

```bash
snakemake -n
```

You can also save the info to file to explore the output:

```bash
snakemake -n >dry-run.txt
```

Again, there is a profile file that is being used, so our command is
equivalent to the following (`profiles/default/config.yaml`):

```bash
snakemake --use-conda -p -c 8 -n
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

Can you find the samples we defined in the output?
Can you see the IDs that we specified?

Now that we see that expected data is used, we can run our analysis.


::::::::::::::::::::::::::::::::::::: challenge 

### Challenge 6: Run the analysis

Do you remember how to do it? If not do you know where to find the information.
(You can always look things up.)

:::::::::::::::::::::::: solution 

#### Solution

```bash
snakemake
```

Or more explicitly:

```bash
snakemake --use-conda -p -c 8
```

This will take a while. Time for coffee, walk or other free time.

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

## Checking the results

This workflow doesn't really define what the output is, but we can guess from the
descriotion: "_Variant calling workflow for creating SNP phylogenies_".
So we will get a phylogeny or tree, probably a Newick (`.nwk` file).
Let's check the dry run info.

We can see that `results/phylogeny/fasttree-snvs.nwk` will be generated.

Let's see how this file looks like. We can use [iTOL](https://itol.embl.de) to visualize it.

## [Bonus/side note] Reduced data set for faster analysis

If you want to explore the results, but you don't want to wait for
long or you don't have access to a strong computer, you can use the reduced
test set of the the workflow. We will use the test set of the workflow from the
next episode: [westerdijk-wm/smkwf-bwa-gatk-snpeff](https://github.com/westerdijk-wm/smkwf-bwa-gatk-snpeff) 

For this we need to download the full repository of the workflow:

```bash
git clone https://github.com/westerdijk-wm/smkwf-bwa-gatk-snpeff
```

This created a `smkwf-bwa-gatk-snpeff` folder in our working directory
you can see that its content is similar to our current directory, but has even
more content.

The standard test data for standardized workflows is located in the `.test`
directory. Check what it contains (`smkwf-bwa-gatk-snpeff/.test`)


::::::::::::::::::::::::::::::::::::: challenge 

### Challenge 6: Update the configuration to use the test set

Replace the config files in the working directorie's `config`
by `smkwf-bwa-gatk-snpeff/.test/config`.

Can you guess where the input data is and where we need to move it?
Then do the move.

:::::::::::::::::::::::: solution 

#### Solution

```bash
mv smkwf-bwa-gatk-snpeff/.test/config .
```

Check `config/units.tsv`, the input shoud be in `data/reads/`
as you can see on `data/reads/12-7505446_1.fastq.gz`.
This is always relative to the folder where `config` folder is.

So we need to the following command:

```bash
mv smkwf-bwa-gatk-snpeff/.test/data/ .
```

Let's check that it worked:

```bash
ls data/reads/12-7505446_1.fastq.gz
```

Now you can go back to the steps to see how to test your configuration and run the analysis.

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

Great work, you have used a second workflow, and update the input to match
your analysis needs. Phylogenomics is quite a complex analysis that requires
a lot of steps and there are many parameters that we can use. Now, you can see
that if we want to do a standard analysis with the defaults settings, we only
need to change the input data and specify it in the configuration.

::::::::::::::::::::::::::::::::::::: callout

The real strength of workflow managers and standardized workflows is to empower
users to run standard analysis reliably by only changing the input data for
the analysis.

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: keypoints 

- Know where to find input parameters and files for a workflow
- Know how to deploy a local copy of a workflow
- Know how to change the input data
- Know how to run a workflow
- Know how where to find test data of standardized snakemake workflows
- Know why standardized (snakemake) workflows are useful for users

::::::::::::::::::::::::::::::::::::::::::::::::


# Gibby (ver 0.1.0)

`Gibby` is a Python package designed to process genomic data, extract peak sequences, compute positional frequency and weight matrices (PFMs, PWMs), and compare scores based on the PWM in order to converge on the best motif. This tool is particularly for motif finding.

- [What's the input?](#input)
- [What's the output?](#output)
- [What is Gibbs Sampling?](#what-is-gibbs-sampling)
- [TL;DR: A short explanation of Gibbs Sampling](#tldr-a-short-explanation-of-gibbs-sampling)
- [All Features](#features)
- [How do I install Gibby?](#installation)
- [How do I use Gibby?](#usage)
- [Examples](#examples)

## Input 
`Gibby` can take HOMER `peaks.txt` files and `.bed` files as input.

## Output
Three files are generated: `PFM.txt`, `PWM.txt`, and `motif.png`. These files include the position frequency matrix and position weight matrix generated by gibbs sampling, and a visualization of the sequence motif showing which motif was most strongly conserved among the peak regions. 

<p align="center">
<img src=https://github.com/kairitanaka/CSE_185_finalProject/assets/86521451/f60bc209-1975-4fe0-9844-40f2f0de3f25)>
</p>

## What is Gibbs Sampling?
Gibbs Sampling is a statistical method used to estimate the distribution of variables when direct sampling is difficult. It is particularly useful in motif finding where we want to identify common patterns (motifs) in a set of sequences. 
<p align="center">
  <img src="https://github.com/kairitanaka/CSE_185_finalProject/assets/64274901/e9ca348f-9c26-45b7-b3d9-da83b10b6a41" alt="gibbs">
</p>

### For example ...
Imagine you are trying to perfect a secret recipe, but you don't have all the ingredients at once. You start by randomly choosing some ingredients and proportions, then you taste the result. Based on how good or bad it tastes, you keep some ingredients, change others, and try again. Each iteration helps you understand what works and what doesn't, gradually leading you to the best recipe.

In the context of genomic sequences, Gibbs Sampling helps us identify common patterns (motifs) by iteratively refining our guesses based on the sequences we have. Each iteration, which involves some randomness, helps to gradually reveal the underlying motifs more accurately.

More images and text to come explaining gibbs ... 

## TL;DR: A short explanation of Gibbs Sampling

# Features

- Utilize Gibbs sampling to **identify motifs** within the peak regions for a given transcription factor.
- Compute Position Frequency/Weight Matrices (PFMs, PWMs)

# Installation

You can install the package via pip:

```
pip install git+https://github.com/kairitanaka/CSE_185_finalProject.git
```
To check if installation was successful, run:
```
gibby -h
```
If you get an error that the command is not found, make sure the directory ~/.local/bin is included in your $PATH environment variable. Or consider adding the directory to your `$PATH` by running:
```
export PATH=$PATH:$HOME/.local/bin
```
This will allow you to simply type `gibby` to run the tool. You will have to repeat this for every new terminal session.

If you come across the error:
```
error: cannot find command 'git'
```
Please make sure to download git! Please check [here](https://github.com/git-guides/install-git) for troubleshooting. 


# Usage

`gibby`, from Gibby (ver 0.1.0), utilizes Gibbs Sampling to find potential motifs that are in peak regions of the genome. The potential motifs are used to generate a position frequency matrix (PFM), a position weight matrix (PWM), and a motif logo based on these matrices. The options appear as below:

```
gibby [-h] -p PEAK_FILE -t PEAK_FILE_TYPE -g GENOME_FASTA_FILE [-s SCORE_THRESHOLD] [-k KMER_SIZE] [-i ITERATIONS]
```
### Required arguments
Assuming you have successfully installed Gibby, running the tool is a fairly simple task. First, it's a good idea to run `gibby -h` to see what options are available. You will notice that Gibby will always require three arguments to be passed: the `PEAK_FILE`, the `PEAK_FILE_TYPE` ("bed" or "homer"), and the `GENOME_FASTA_FILE`. In addition, there are several optional arguments such as `SCORE_THRESHOLD`, `KMER_SIZE`, and `ITERATIONS`. 

### Optional arguments
`SCORE_THRESHOLD` represents the minimum score that the tool will use to filter out low quality peaks. Generally, if you know the general length of the motif that you are looking for, you can specify `KMER_SIZE` to the general length + 5 (to add some leeway). `ITERATIONS` is the number of times you want to run Gibbs Sampling. Running more iterations results in better detection of motifs in exchange for the increasing time it takes to finish the task. We have set default values for these variables which strike a good balance between speed and performance.

## Command Line Arguments

- `-p`, `--peak_file`: Input BED/Homer file with peak information. (required)
- `-t`, `--peak_file_type`: Specify peak file type: 'bed' or 'homer' file. (required)
- `-g`, `--genome_fasta_file`: Genome FASTA file. (required)
- `-s`, `--score_threshold`: Score threshold for filtering peaks. (optional)
- `-k`, `--kmer_size`: Size of k-mers for motif finding. (optional)
- `-i`, `--iterations`: Number of iterations for Gibbs sampler. (optional)


# Examples

## OCT4 (Pou5f1) 
In this example, we will be using the same files that were used in Lab 5 of the CSE 185 course. The files you will need are the following: (1) `peaks.txt` which is the peak file generated by HOMER for OCT4 (which we have also included in our repo) and (2) the `GRCm38.fa` Mus musculus genome assembly in FASTA format which should be available in the CSE 185 public genomes folder. 

Please take a look at the "Usage" section above to get a good idea of what the tool requires.

In this case, we have a HOMER peak file. In addition, suppose we want to choose 75 as the score threshold to filter out low-quality peaks. Running the tool would look like this: 

```
gibby -p ~/lab5/tagdirs/Oct4/peaks.txt -t homer -g ~/public/genomes/GRCm38.fa -s 75
```

You will want to make sure that you have the correct paths for the peak file and the genome file. Running the command, the tool will take some time to fully run. After finishing, you will want to take a look at the generated motif logo which visualizes which motif was most conserved among the peak regions. In our case, we got: 
<p align="center">
<img src=https://github.com/kairitanaka/CSE_185_finalProject/assets/86521451/437e5a4a-e57f-4b51-be1b-27c271150b64)>
</p>

and sometimes its reverse complement:
<p align="center">
<img src=https://github.com/kairitanaka/CSE_185_finalProject/assets/86521451/bd12cc26-be4a-4e9e-aa6b-d68610c6b8e1)>
</p>

Since Gibbs Sampling is stochastic process, your graphs will look somewhat different from our results. However, what *should* be similar are the large letters (nucleotides) stacked at the top and their relative positioning to one another. These large sets of letters represent the motif that Gibby discovered; all the other smaller nucleotides represent "noise" or nucleotides that were not as strongly conserved among peak regions for OCT4. 
Compare your results with the published motifs for OCT4: 
<p align="center">
<img src=https://github.com/kairitanaka/CSE_185_finalProject/assets/86521451/25dfae39-3832-48bf-870c-ab0cf8f3e20d)>
</p>

Reverse complement:
<p align="center">
<img src=https://github.com/kairitanaka/CSE_185_finalProject/assets/86521451/b3eacbfa-9f2c-42e6-9041-ac2ee53c9f54)>
</p>

# That's it! Hopefully they look relatively similar!


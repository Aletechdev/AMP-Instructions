# ALE Resequencing QC and Mutation Finding Pipeline 
**WARNING: Only run these scripts on a copy of the original read files. Keep a backup of all original read files.**

System Requirements
Any Machine: minimum 100GB Storage.


# Background
The mutation pipeline accomplishes the steps described in Pabinger et. al. 2014. “A Survey of Tools for Variant Analysis of next-Generation Genome Sequencing Data.” Briefings in Bioinformatics 15 (2): 256–78.
![](docs/alemutpipe_workflow.png)


# Workflow

## Prepping Data

New metadata generation: https://docs.google.com/spreadsheets/d/1WhKCgSKn5G5ypx8ZUB2a6xeuKvi06DqhTAlUJRoRems/copy

Make sure all the file names in the metadata matches your read files and reference files exactly.

Place all your read files, reference files and metadata files in the same folder/directory
Save the absolute path of this directory, you will need it later.

## Setting up Docker

Create an account for Docker Hub. You can do so [here](https://hub.docker.com/signup)

Follow the instructions on [The Docker website](https://docs.docker.com/install/) to download/install and start docker on your device.

Message Muyao with your docker hub username for access to the pipeline image.

Depending on the size of your reads, it may be necessary to increase the Storage and RAM allocated to docker on Windows and Mac machines.

Input the following command into the terminal (cmd on Windows Machines) to log into docker:

```
docker login
```

If on linux:
```
sudo docker login
```

### Note: Linux users need to precede all docker commands with "sudo"


## Running the Pipeline


Type the following command into your terminal/cmd:

```
docker run -v '/path/to/your/data':/var/data -it --name amp aletechdev/amp bash -i -c "source /root/.bashrc && /amp.sh"
```

Make sure to replace the example path in quotations with your own absolute path to your folder/directory containing all the files

if in linux, use "sudo" before the "docker" command.

The quotes are only necessary if there are special characters in your file path

The results will be placed in the same location as the inputs in a folder named alemutpipe-outputs

## Removing the container

To remove the container used to run the pipeline:

```
docker rm amp
```

## Updating the pipelinee

Remove the container and perform a pull of the latest docker hub image:

```
docker pull muyaowu/alemutpipe
```


## Uploading to aledb

Complete [this form](https://goo.gl/forms/RzPc44dBd6rmTRDs2)


# Development

## Unit tests

To run the project's unit tests, from the root of the project, execute the following:  
`python3 -m unittest tests/name_of_test_file.py`

# Amplifications

This pipeline currently uses a custom script to describe amplifications or copy number variants in the genome. Genome amplifications are identified with a custom script that identified discontinuities in read depth. The discontinuities are designated as amplifications if their read depth exceeded a threshold calculated as (2 * average) - (1 standard deviation) of the read depth for the sample's entire resequenced genome. To reduce small sporadic amplification events, 10 consecutive bases must pass above or below the threshold before a start or end position of an amplification region is designated.


# Copy number variants

This pipeline currently uses CNVnator to find copy number variants in the genome.

It has also been implemented as a separate module to allow running on completed experiments.

The outputs can be found inside each samples's breseq data and gatk folders as calling_output.tsv

```
docker run -v '/path/to/your/experiment':/var/data -it --name cnv aletechdev/amp bash -i -c "time python3 /amp/alemutpipe/cnv_main.py /var/data/"
```

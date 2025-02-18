{
 "cells": [
  {
   "cell_type": "markdown",
   "id": "765e3854",
   "metadata": {},
   "source": [
    "\n",
    "# **BIOINFORMATICS ANALYSIS STRATEGY**\n",
    "\n",
    "\n",
    "\n",
    "Participants\n",
    "- Marguerite Edith M. NIKIEMA\n",
    "- Jean-Marc ZAOURI\n",
    "\n",
    "Tuteurs\n",
    "- Alexis DEREEPER\n",
    "- Sebastien CUNNAC\n",
    "\n",
    "Jupyter inspired by the model created by C. Tranchant (DIADE-IRD), J. Orjuela (DIADE-IRD), F. Sabot (DIADE-IRD) and A. Dereeper (PHIM-IRD)\n",
    "***\n",
    "\n",
    "   \n",
    "   \n",
    "\n",
    "***\n",
    "\n",
    "## **Table of Contents**\n",
    "1. [Introduction and Objectives](#introduction)\n",
    "2. [Cluster Connection and Environment Setup](#setup)\n",
    "3. [Data Management](#data-management)\n",
    "4. [Preprocessing](#preprocessing)\n",
    "5. [Alignment and Assembly](#alignment)\n",
    "6. [Variant Analysis](#variant-analysis)\n",
    "7. [Visualization and Interpretation](#visualization)\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "2995d7fa-c813-41f7-bdaa-aee20a401add",
   "metadata": {},
   "source": [
    "# Introduction and Objectives"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "764350bd-c42c-4465-ad76-1f25256c96ba",
   "metadata": {},
   "outputs": [],
   "source": [
    "Rice = a fundamental crop for global food security.. \n",
    "It is grown in almost every country, including China....\n",
    "Importance of differentiating rice varieties :\n",
    "Improvement of yields, resistance to diseases, and adaptation to climate change..\n",
    "Importance of genotyping for better knowledge of circulating varieties. \n",
    "\n",
    "\n",
    "Prototyping a taxonomic placement method for rice leaf \"reads\" in a Nanopore metagenomic sequencing dataset.\n",
    "Specific objectives\n",
    "Determine to which rice variety the leaf belongs, using the metagenomic sequences obtained through standard Nanopore mode and adaptive sampling mode.\n",
    "Compare the two sequencing modes used for better results.\n",
    "\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "b0439389",
   "metadata": {},
   "source": [
    "# Cluster Connection and Environment Setup"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "90331e6c-ca31-4b52-94d2-faea705197b1",
   "metadata": {},
   "source": [
    "## Connecting to the cluster"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "id": "035b3260-a24e-4c1b-8d40-4b3c23bc1eca",
   "metadata": {
    "collapsed": true,
    "jupyter": {
     "outputs_hidden": true
    }
   },
   "outputs": [
    {
     "ename": "SyntaxError",
     "evalue": "invalid syntax (1365644834.py, line 1)",
     "output_type": "error",
     "traceback": [
      "\u001b[0;36m  Cell \u001b[0;32mIn[1], line 1\u001b[0;36m\u001b[0m\n\u001b[0;31m    ```bash\u001b[0m\n\u001b[0m    ^\u001b[0m\n\u001b[0;31mSyntaxError\u001b[0m\u001b[0;31m:\u001b[0m invalid syntax\n"
     ]
    }
   ],
   "source": [
    "# Connect to cluster using username and ssh command\n",
    "\n",
    "ssh nikema@bioinfo-master1.ird.fr"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "07bb3f89-d404-40f9-a5c1-56c5b985adf1",
   "metadata": {},
   "source": [
    "## Checking available partitions"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "8fc7cd82-fcff-463b-bece-e2674f4285fb",
   "metadata": {},
   "outputs": [],
   "source": [
    "# List partitions and informations\n",
    "sinfo\n",
    "\n",
    "# List partition, node and job (free and used)\n",
    "squeue"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "78a26bf3-b948-464e-984a-cd1b967a782b",
   "metadata": {},
   "source": [
    "## Selecting partition and CPU allocation"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "2b3138cf-9ded-4b58-97b1-0a568af5f295",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Select \"normal\" partition with 8 cpus \n",
    "\n",
    "srun -p normal -c 8 --pty bash -i"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "05a73242",
   "metadata": {},
   "source": [
    "# Data Management"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "259e5a97-4d0c-4e7a-9fb1-b38420829259",
   "metadata": {},
   "source": [
    "## Creating workspace directories"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "43599281-dfbf-4493-b1e2-dddc76dcd0db",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Move to scratch directory for any work on cluster\n",
    "cd /scratch\n",
    "\n",
    "# Create my working directory named \"nikema\" and move in\n",
    "mkdir nikema\n",
    "cd nikema\n",
    "\n",
    "# Create Data directory in working directory\n",
    "mkdir Data"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "42139f4d-292e-4b9f-beaa-ffdd1bea485e",
   "metadata": {},
   "source": [
    "## Copying data from NAS"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "a296a72a-2a46-47bb-aac1-03dae559d28f",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Copy workin data availableon NAS to working directory\n",
    "\n",
    "scp -r san:/share/teams/xplain/CIBIG_2024_tutored_project/seqModeSplitSeqs/ /scratch/nikema/Data"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "b7009de4-b587-40ea-8c3e-1d28f29a4f96",
   "metadata": {},
   "source": [
    "## Listing and inspecting FASTQ files"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "8bef8bb5-3af4-440d-ad6d-c9dbbb886b88",
   "metadata": {},
   "outputs": [],
   "source": [
    "# List Data directory content \n",
    "ls"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "04f7f97a",
   "metadata": {},
   "source": [
    "# Caracterization of Rice variety"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "017f78cc",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Connect to cluster\n",
    "\n",
    "ssh nikema@bioinfo-master1.ird.fr"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "cb932888",
   "metadata": {},
   "outputs": [],
   "source": [
    "# List partitions available\n",
    "\n",
    "sinfo"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "2a7c50cd",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Choice \"normal\" partition with 8 cpus without change the last node\n",
    "\n",
    "srun -p normal --nodelist=node20 -c 8 --pty bash -i"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "b65db3dd",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Move to scratch directory\n",
    "cd /scratch\n",
    "\n",
    "# Now to working directory\n",
    "cd nikema\n",
    "\n",
    "# And Data directory\n",
    "cd Data"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "99106997",
   "metadata": {},
   "outputs": [],
   "source": [
    "# List Data directory content\n",
    "ls"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "0cd071a7",
   "metadata": {},
   "source": [
    "seqModeSplitSeqs"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "9f68489d",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Move to seqModeSplitSeqs directory and list content\n",
    "\n",
    "cd seqModeSplitSeqs/\n",
    "ls"
   ]
  },
  {
   "cell_type": "raw",
   "id": "808ba138-af4c-461f-9edf-2d97300c28ca",
   "metadata": {},
   "source": [
    "NWA116_AS.fastq.gz   NWA119_STD.fastq.gz  NWA123_AS.fastq.gz   NWA126_STD.fastq.gz  NWA130_AS.fastq.gz   NWA143_AS.fastq.gz   NWA149_STD.fastq.gz  NWA153_AS.fastq.gz   NWA156_STD.fastq.gz\n",
    "NWA116_STD.fastq.gz  NWA120_AS.fastq.gz   NWA123_STD.fastq.gz  NWA127_AS.fastq.gz   NWA130_STD.fastq.gz  NWA146_STD.fastq.gz  NWA150_AS.fastq.gz   NWA153_STD.fastq.gz  NWA157_AS.fastq.gz\n",
    "NWA117_AS.fastq.gz   NWA120_STD.fastq.gz  NWA124_AS.fastq.gz   NWA127_STD.fastq.gz  NWA140_STD.fastq.gz  NWA147_AS.fastq.gz   NWA150_STD.fastq.gz  NWA154_AS.fastq.gz   NWA157_STD.fastq.gz\n",
    "NWA117_STD.fastq.gz  NWA121_AS.fastq.gz   NWA124_STD.fastq.gz  NWA128_AS.fastq.gz   NWA141_AS.fastq.gz   NWA147_STD.fastq.gz  NWA151_AS.fastq.gz   NWA154_STD.fastq.gz  NWA158_AS.fastq.gz\n",
    "NWA118_AS.fastq.gz   NWA121_STD.fastq.gz  NWA125_AS.fastq.gz   NWA128_STD.fastq.gz  NWA141_STD.fastq.gz  NWA148_AS.fastq.gz   NWA151_STD.fastq.gz  NWA155_AS.fastq.gz   NWA158_STD.fastq.gz\n",
    "NWA118_STD.fastq.gz  NWA122_AS.fastq.gz   NWA125_STD.fastq.gz  NWA129_AS.fastq.gz   NWA142_AS.fastq.gz   NWA148_STD.fastq.gz  NWA152_AS.fastq.gz   NWA155_STD.fastq.gz\n",
    "NWA119_AS.fastq.gz   NWA122_STD.fastq.gz  NWA126_AS.fastq.gz   NWA129_STD.fastq.gz  NWA142_STD.fastq.gz  NWA149_AS.fastq.gz   NWA152_STD.fastq.gz  NWA156_AS.fastq.gz\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "455639fe",
   "metadata": {},
   "source": [
    "# QUALITY CONTROL\n",
    " "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "c80271f7",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Define variable for output directory in working directory"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "9192755e",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Move to working directory\n",
    "cd /scratch/nikema\n",
    "\n",
    "# Create QC directory in working directory\n",
    "mkdir QC\n",
    "\n",
    "#Move to the create directory\n",
    "cd QC"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "74ee9ce8-84ab-4636-9997-9aa7f1553429",
   "metadata": {},
   "source": [
    "## Quality control using NanoPlot"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "a01ed153",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Load NanoPlot module\n",
    "\n",
    "module load nanoplot/1.43.0"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "94c365fd",
   "metadata": {},
   "source": [
    "Loading nanoplot/1.43.0\n",
    "\n",
    "Loading requirement: singularity/4.0.1"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "dc187e2f",
   "metadata": {},
   "outputs": [],
   "source": [
    "# List NanoPlot help\n",
    "\n",
    "NanoPlot --help"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "c4da65f6",
   "metadata": {},
   "source": [
    "# NANOPLOT "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "46a00cc1",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run command on all STD.fastq.gz; output in STD directory \n",
    "\n",
    "NanoPlot -t 8 --fastq /scratch/nikema/Data/seqModeSplitSeqs/*_STD.fastq.gz -o /scratch/nikema/QC/STD"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "a3731bf0",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run command on all AS.fastq.gz; output in AS directory \n",
    "\n",
    "NanoPlot -t 8 --fastq /scratch/nikema/Data/seqModeSplitSeqs/*_AS.fastq.gz -o /scratch/nikema/QC/AS"
   ]
  },
  {
   "cell_type": "raw",
   "id": "5b9fb020-fb86-47b8-8a74-2379a2a02152",
   "metadata": {},
   "source": [
    "Not work for STD files \n",
    "Necessary to use a script sbatch to be fast"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "b7d9b6d3-3705-441f-896a-358b889074e2",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Move to working directory and create \"scripts\" directory for all scripts\n",
    "cd /scratch/nikema\n",
    "\n",
    "# Create \"scripts dirctory\" and move in\n",
    "mkdir scripts\n",
    "cd scripts"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "1156914d",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Open nano editor and save nanoplot.sh script for quality control\n",
    "\n",
    "nano nanoplot.sh"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "938afa06",
   "metadata": {},
   "outputs": [],
   "source": [
    "#!/bin/bash\n",
    "\n",
    "################# SLURM Configuration #################\n",
    "\n",
    "### Define Job name\n",
    "#SBATCH --job-name=quality_control   # Nom du job\n",
    "\n",
    "### Define partition to use\n",
    "#SBATCH -p normal                   # Partition à utiliser\n",
    "\n",
    "### Define number of CPUs to use\n",
    "#SBATCH --cpus-per-task=8           # Utilisation de 8 CPU par tâche\n",
    "\n",
    "### Specify the node to run on\n",
    "#SBATCH --nodelist=node20           # Spécification du nœud\n",
    "\n",
    "### Define memory (optionnel, mais utile pour des jobs gourmands en mémoire)\n",
    "#SBATCH --mem=16G                   # Exemple de mémoire allouée, ajuster si nécessaire\n",
    "\n",
    "# Chargement des modules nécessaires\n",
    "module load nanoplot/1.43.0        # Chargement du module NanoPlot\n",
    "\n",
    "# Exécution du programme avec les paramètres fournis\n",
    "\n",
    "# Traitement des fichiers _STD.fastq.gz avec 8 threads et sortie dans le répertoire spécifié\n",
    "NanoPlot -t 8 --fastq /scratch/nikema/Data/seqModeSplitSeqs/*_STD.fastq.gz -o /scratch/nikema/QC/STD\n",
    "\n",
    "# Traitement des fichiers _AS.fastq.gz avec 8 threads et sortie dans le répertoire spécifié\n",
    "NanoPlot -t 8 --fastq /scratch/nikema/Data/seqModeSplitSeqs/*_AS.fastq.gz -o /scratch/nikema/QC/AS"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "cdc50602-9a97-46d6-a44d-4704bd59562b",
   "metadata": {},
   "outputs": [],
   "source": [
    "# List scripts directory content\n",
    "\n",
    "ls"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "5aaa803d",
   "metadata": {},
   "source": [
    "nanoplot.sh"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "ad8d7792",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Run nanoplot.sh script\n",
    "\n",
    "sbatch nanoplot.sh "
   ]
  },
  {
   "cell_type": "markdown",
   "id": "5f4ca13b",
   "metadata": {},
   "source": [
    "Submitted batch job 331456"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "9f75076d",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Check submitted job and evolution\n",
    "\n",
    "squeue "
   ]
  },
  {
   "cell_type": "markdown",
   "id": "1e1da0af",
   "metadata": {},
   "source": [
    "            JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)\n",
    "            328668    global R-RPANDA holzmeye  R 3-04:30:16      1 node31\n",
    "            330453   highmem DVWAB003    cubry PD       0:00      1 (AssocGrpCpuLimit)\n",
    "            330454   highmem DVWAB000    cubry PD       0:00      1 (AssocGrpCpuLimit)\n",
    "            330455   highmem DVWAB003    cubry PD       0:00      1 (AssocGrpCpuLimit)\n",
    "            330456   highmem DVWAB002    cubry PD       0:00      1 (AssocGrpCpuLimit)\n",
    "            330457   highmem DVSeqG20    cubry PD       0:00      1 (AssocGrpCpuLimit)\n",
    "            330458   highmem DVWAB000    cubry PD       0:00      1 (AssocGrpCpuLimit)\n",
    "            330459   highmem DVWAB000    cubry PD       0:00      1 (AssocGrpCpuLimit)\n",
    "            330460   highmem DVWAB001    cubry PD       0:00      1 (AssocGrpCpuLimit)\n",
    "\n",
    "329998_[53-180]    normal chaungDo   chaung PD       0:00      1 (AssocGrpCpuLimit)\n",
    "    331199_[29-32]    normal chaungDo   chaung PD       0:00      1 (AssocGrpCpuLimit)\n",
    "            331330    normal delaigue delaigue PD       0:00      1 (Resources)\n",
    "            331456    normal quality_   nikema PD       0:00      1 (Resources)\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "42e806ec",
   "metadata": {},
   "outputs": [],
   "source": [
    "## At theend of run, move to QC directory\n",
    "cd ../QC/\n",
    "\n",
    "#List content\n",
    "ls"
   ]
  },
  {
   "cell_type": "raw",
   "id": "bb11eddb-9091-4983-8095-c710a3c45879",
   "metadata": {},
   "source": [
    "AS \n",
    "STD"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "9483c93d",
   "metadata": {},
   "outputs": [],
   "source": [
    "# LIst AS directory content\n",
    "\n",
    "ls AS/"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "8b29b5b0",
   "metadata": {},
   "source": [
    "LengthvsQualityScatterPlot_dot.html  NanoPlot_20241106_1927.log    Non_weightedHistogramReadlength.html                 WeightedHistogramReadlength.html                 Yield_By_Length.html\n",
    "LengthvsQualityScatterPlot_dot.png   NanoPlot-report.html          Non_weightedHistogramReadlength.png                  WeightedHistogramReadlength.png                  Yield_By_Length.png\n",
    "LengthvsQualityScatterPlot_kde.html  NanoStats_post_filtering.txt  Non_weightedLogTransformed_HistogramReadlength.html  WeightedLogTransformed_HistogramReadlength.html\n",
    "LengthvsQualityScatterPlot_kde.png   NanoStats.txt                 Non_weightedLogTransformed_HistogramReadlength.png   WeightedLogTransformed_HistogramReadlength.png"
   ]
  },
  {
   "cell_type": "raw",
   "id": "12ae2a1e-21ec-4510-9b9f-a3f5bb731cee",
   "metadata": {},
   "source": [
    "The script don't work for STD files \n",
    "error in some files"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "bc6131f7-4275-4915-9cfe-b47470688a56",
   "metadata": {},
   "outputs": [],
   "source": [
    "# List STD directory\n",
    "\n",
    "ls STD/"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "bcef25c1",
   "metadata": {},
   "source": [
    "total 48K\n",
    "\n",
    "-rw-r--r-- 1 nikema xpert 16K  6 nov.  16:54 NanoPlot_20241106_1653.log\n",
    "\n",
    "-rw-r--r-- 1 nikema xpert 16K  6 nov.  16:56 NanoPlot_20241106_1655.log\n",
    "\n",
    "-rw-r--r-- 1 nikema xpert 16K  6 nov.  19:27 NanoPlot_20241106_1925.log"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "8a67c6e0",
   "metadata": {},
   "outputs": [],
   "source": [
    " ### Copy QC results to NAS \n",
    "\n",
    "scp -r /scratch/nikema/QC/ san:/share/teams/xplain/CIBIG_2024_tutored_project/"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "f39182ef",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Copier egalement le résultat QC du NAS, sur sa machine:\n",
    "###option 1 aller sur Fizella\n",
    "###option2 ouvrir un autre terminal \n",
    "#### A partir de la clé ssh du san + le chemin du NAS copier le resultat QC dans mon dossier personnel (projet_tutore2024) tout en svt le chemin\n",
    "####clic Entrée: yes"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "e9636019",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Copy QC results on NAS to local machine\n",
    "\n",
    "scp -r nikema@bioinfo-san.ird.fr:/share/teams/xplain/CIBIG_2024_tutored_project/QC/ /home/edith/Certificat_CIBiG2024/Projet_tutore2024/"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "477df3e2",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Move to Data directory in workind directory\n",
    "cd /scratch/nikema/Data\n",
    "\n",
    "## Concatenate all files in one and lunch NanoPLot again\n",
    "cd seqModeSplitSeqs/\n",
    "cat *_AS* > ./AS.fastq.gz\n",
    "cat *_STD* > ./STD.fastq.gz"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "a4a1b5fa",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Remove single files\n",
    "\n",
    "rm -rf *_AS.fastq.gz\n",
    "rm -rf *_STD.fastq.gz"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "006f2040",
   "metadata": {},
   "outputs": [],
   "source": [
    "### Move to scripts directory\n",
    "cd /scratch/nikema/scripts\n",
    "\n",
    "# Open nano editor and save nanoplot1.sh script\n",
    "nano nanoplot1.sh "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "3de36310-6af2-451e-b019-47e2596dcc80",
   "metadata": {},
   "outputs": [],
   "source": [
    "#!/bin/bash\n",
    "\n",
    "################# SLURM Configuration #################\n",
    "\n",
    "### Define Job name\n",
    "#SBATCH --job-name=quality_control   # Nom du job\n",
    "\n",
    "### Define partition to use\n",
    "#SBATCH -p normal                   # Partition à utiliser\n",
    "\n",
    "### Define number of CPUs to use\n",
    "#SBATCH --cpus-per-task=8           # Utilisation de 8 CPU par tâche\n",
    "\n",
    "### Specify the node to run on\n",
    "#SBATCH --nodelist=node20           # Spécification du nœud\n",
    "\n",
    "### Define memory (optionnel, mais utile pour des jobs gourmands en mémoire)\n",
    "#SBATCH --mem=16G                   # Exemple de mémoire allouée, ajuster si nécessaire\n",
    "\n",
    "# Chargement des modules nécessaires\n",
    "module load nanoplot/1.43.0        # Chargement du module NanoPlot\n",
    "\n",
    "# Exécution du programme avec les paramètres fournis\n",
    "\n",
    "# Traitement des fichiers _STD.fastq.gz avec 8 threads et sortie dans le répertoire spécifié\n",
    "NanoPlot -t 8 --fastq /scratch/nikema/Data/seqModeSplitSeqs/STD.fastq.gz -o /scratch/nikema/QC/STD\n",
    "\n",
    "# Traitement des fichiers _AS.fastq.gz avec 8 threads et sortie dans le répertoire spécifié\n",
    "NanoPlot -t 8 --fastq /scratch/nikema/Data/seqModeSplitSeqs/AS.fastq.gz -o /scratch/nikema/QC/AS"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "d646c601",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Lunch tne sbatch script\n",
    "\n",
    "sbatch nanoplot1.sh "
   ]
  },
  {
   "cell_type": "markdown",
   "id": "2a5bde4e",
   "metadata": {},
   "source": [
    "# MAPPING"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "5a25c002",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Move to working directory\n",
    "cd /scratch/nikema/\n",
    "\n",
    "# Create REF directory\n",
    "mkdir REF\n",
    "\n",
    "# Move to the create directory\n",
    "cd REF"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "45fbf233",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Download rice refecence genme on NCBI Oryza sativa japonica Nipponbare\n",
    "wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/034/140/825/GCF_034140825.1_ASM3414082v1/GCF_034140825.1_ASM3414082v1_genomic.fna.gz\n",
    "\n",
    "## Gunzip\n",
    "gunzip GCF_034140825.1_ASM3414082v1_genomic.fna.gz "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "eb317b8b",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Move to working directory\n",
    "cd /scratch/nikema\n",
    "\n",
    "# Create Mapping directory\n",
    "mkdir Mapping"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "eba53c49",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Move to scripts directory\n",
    "cd scripts/\n",
    "\n",
    "# OPen nano editor and save mapping.sh sbatch script \n",
    "nano mapping.sh "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "12cc7c7e-6eeb-4ec5-bc75-751e0552705e",
   "metadata": {},
   "outputs": [],
   "source": [
    "#!/bin/bash\n",
    "\n",
    "################# SLURM Configuration #################\n",
    "\n",
    "### Define Job name\n",
    "#SBATCH --job-name=mapping   # Nom du job\n",
    "\n",
    "### Define partition to use\n",
    "#SBATCH -p normal                   # Partition à utiliser\n",
    "\n",
    "### Define number of CPUs to use\n",
    "#SBATCH --cpus-per-task=8           # Utilisation de 8 CPU par tâche\n",
    "\n",
    "### Specify the node to run on\n",
    "#SBATCH --nodelist=node20           # Spécification du nœud\n",
    "\n",
    "### Define memory (optionnel, mais utile pour des jobs gourmands en mémoire)\n",
    "#SBATCH --mem=16G                   # Exemple de mémoire allouée, ajuster si nécessaire\n",
    "\n",
    "################# Load Modules #################\n",
    "\n",
    "# Charger les modules nécessaires\n",
    "module load minimap2/2.24\n",
    "module load samtools/1.9\n",
    "\n",
    "################# Start of the Script #################\n",
    "\n",
    "# Liste des fichiers fastq.gz (sans extension)\n",
    "FILES=(\"WA116\" \"NWA119\" \"NWA122\" \"NWA125\" \"NWA128\" \"NWA140\"\n",
    "       \"NWA141\" \"NWA147\" \"NWA150\" \"NWA153\" \"NWA156\"\n",
    "       \"NWA117\" \"NWA120\" \"NWA123\" \"NWA126\" \"NWA129\"\n",
    "       \"NWA142\" \"NWA148\" \"NWA151\" \"NWA154\" \"NWA157\"\n",
    "       \"NWA118\" \"NWA121\" \"NWA124\" \"NWA127\" \"NWA130\"\n",
    "       \"NWA143\" \"NWA149\" \"NWA152\" \"NWA155\" \"NWA158\")\n",
    "\n",
    "# Répertoire des fichiers de référence\n",
    "REFERENCE_GENOME=\"/scratch/nikema/REF/GCF_034140825.1_ASM3414082v1_genomic.fna\"\n",
    "OUTPUT_DIR=\"/scratch/nikema/Mapping/\"\n",
    "\n",
    "# Créer le répertoire de sortie si nécessaire\n",
    "mkdir -p ${OUTPUT_DIR}\n",
    "\n",
    "# Itérer sur les fichiers pour effectuer le mapping et la conversion\n",
    "for i in \"${FILES[@]}\"; do\n",
    "    echo \"Traitement de ${i}...\"\n",
    "\n",
    "    # Mapping des reads sur le génome de référence riz (Format Sam)\n",
    "    minimap2 -t 8 -ax map-ont $REFERENCE_GENOME /scratch/nikema/Data/seqModeSplitSeqs/${i}_AS.fastq.gz > ${OUTPUT_DIR}/reads_${i}_AS_vs_riz.sam\n",
    "    minimap2 -t 8 -ax map-ont $REFERENCE_GENOME /scratch/nikema/Data/seqModeSplitSeqs/${i}_STD.fastq.gz > ${OUTPUT_DIR}/reads_${i}_STD_vs_riz.sam\n",
    "\n",
    "    # Vérifier si les fichiers SAM ont été créés pour AS et STD\n",
    "    for type in AS STD; do\n",
    "        if [[ ! -f ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz.sam ]]; then\n",
    "            echo \"Erreur: le fichier SAM pour ${i}_${type} n'a pas été créé.\"\n",
    "            continue 2  # Passer à l'itération suivante du fichier si erreur\n",
    "        fi\n",
    "    done\n",
    "\n",
    "    # Conversion du fichier SAM en BAM pour AS et STD\n",
    "    for type in AS STD; do\n",
    "        samtools view -b -S ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz.sam -o ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz.bam\n",
    "\n",
    "        # Vérifier si le fichier BAM a été créé\n",
    "        if [[ ! -f ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz.bam ]]; then\n",
    "            echo \"Erreur: la conversion SAM->BAM pour ${i}_${type} a échoué.\"\n",
    "            continue 2  # Passer à l'itération suivante du fichier si erreur\n",
    "        fi\n",
    "\n",
    "        # Filtrer les reads mappés et créer un fichier BAM pour AS et STD\n",
    "        samtools view -@ 8 -hb -F 4 ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz.sam -o ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz_mapped.bam\n",
    "\n",
    "        # Trier les fichiers BAM\n",
    "        samtools sort -o ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz_mapped_sort.bam ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz_mapped.bam\n",
    "        \n",
    "        # Générer les statistiques avec samtools flagstat\n",
    "        samtools flagstat ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz.bam > ${OUTPUT_DIR}/reads_${i}_${type}_flagstat.txt\n",
    "        \n",
    "        # Convertir le fichier BAM en FASTQ\n",
    "        samtools fastq ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz_mapped.bam > ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz_mapped.fastq\n",
    "\n",
    "        # Supprimer les fichiers SAM et BAM intermédiaires pour économiser de l'espace\n",
    "        rm ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz.sam\n",
    "        \n",
    "    done\n",
    "\n",
    "    echo \"Traitement de ${i} terminé.\"\n",
    "done\n",
    "\n",
    "echo \"Fin du traitement.\""
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "549d71b2-3920-42c1-8a24-61a631b2d6f8",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Lunch mapping.sh script\n",
    "\n",
    "sbatch mapping.sh"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "dae97f7c",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Give acces to all directories in my working directory\n",
    "\n",
    "chmod -R 775 scripts/ QC/"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "20dadd6c-ef71-485d-98fb-83d294d80225",
   "metadata": {},
   "source": [
    "Repeat Mapping step using another reference genome"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "a47984a0",
   "metadata": {},
   "outputs": [],
   "source": [
    "##### Copy new ref genome from NAS to REF diectory\n",
    "\n",
    "scp -r san:/share/teams/xplain/CIBIG_2024_tutored_project/REF/MSU7.fasta /scratch/nikema/REF"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "71412e93",
   "metadata": {},
   "outputs": [],
   "source": [
    "# OPen nano editor and save new mapping script\n",
    "\n",
    "nano mapping_pipeline2.sh"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "d68ef264-e184-4502-8d81-bf531fcc75f7",
   "metadata": {},
   "outputs": [],
   "source": [
    "#!/bin/bash\n",
    "\n",
    "################# SLURM Configuration #################\n",
    "\n",
    "### Define Job name\n",
    "#SBATCH --job-name=mapping   # Nom du job\n",
    "\n",
    "### Define partition to use\n",
    "#SBATCH -p normal                   # Partition à utiliser\n",
    "\n",
    "### Define number of CPUs to use\n",
    "#SBATCH --cpus-per-task=8           # Utilisation de 8 CPU par tâche\n",
    "\n",
    "### Specify the node to run on\n",
    "#SBATCH --nodelist=node20           # Spécification du nœud\n",
    "\n",
    "### Define memory (optionnel, mais utile pour des jobs gourmands en mémoire)\n",
    "#SBATCH --mem=16G                   # Exemple de mémoire allouée, ajuster si nécessaire\n",
    "\n",
    "################# Load Modules #################\n",
    "\n",
    "# Charger les modules nécessaires\n",
    "module load minimap2/2.24\n",
    "module load samtools/1.9\n",
    "\n",
    "################# Start of the Script #################\n",
    "\n",
    "# Liste des fichiers fastq.gz (sans extension)\n",
    "FILES=(\"AS\" \"STD\")\n",
    "\n",
    "# Répertoire des fichiers de référence et des données\n",
    "REFERENCE_GENOME=\"/scratch/nikema/REF/MSU7.fasta\"\n",
    "OUTPUT_DIR=\"/scratch/nikema/MAPPING2_OUTPUT/\"\n",
    "INPUT_DIR=\"/scratch/nikema/Data/seqModeSplitSeqs/\"\n",
    "\n",
    "# Créer le répertoire de sortie si nécessaire\n",
    "mkdir -p ${OUTPUT_DIR}\n",
    "\n",
    "# Itérer sur les fichiers pour effectuer le mapping et la conversion\n",
    "for i in \"${FILES[@]}\"; do\n",
    "    echo \"Traitement de ${i}...\"\n",
    "\n",
    "    # Fichier d'entrée unique pour chaque type (AS et STD)\n",
    "    INPUT_FILE=\"${INPUT_DIR}/${i}.fastq.gz\"\n",
    "    \n",
    "    # Fichiers de sortie sans répétition des suffixes AS et STD\n",
    "    SAM_FILE=\"${OUTPUT_DIR}/reads_${i}_vs_riz.sam\"\n",
    "    BAM_FILE=\"${OUTPUT_DIR}/reads_${i}_vs_riz.bam\"\n",
    "    \n",
    "    # Mapping des reads sur le génome de référence riz (Format Sam)\n",
    "    minimap2 -t 8 -ax map-ont $REFERENCE_GENOME $INPUT_FILE > $SAM_FILE\n",
    "\n",
    "    # Conversion du fichier SAM en BAM\n",
    "    samtools view -b -S $SAM_FILE -o $BAM_FILE\n",
    "\n",
    "    # Filtrer les reads mappés et créer un fichier BAM filtré\n",
    "    MAPPED_BAM=\"${OUTPUT_DIR}/reads_${i}_vs_riz_mapped.bam\"\n",
    "    samtools view -@ 8 -hb -F 4 $BAM_FILE -o $MAPPED_BAM\n",
    "    \n",
    "    # Trier les fichiers BAM\n",
    "    SORTED_BAM=\"${OUTPUT_DIR}/reads_${i}_vs_riz_mapped_sort.bam\"\n",
    "    samtools sort -o $SORTED_BAM $MAPPED_BAM\n",
    "    \n",
    "    # Générer les statistiques avec samtools flagstat\n",
    "    samtools flagstat $BAM_FILE > ${OUTPUT_DIR}/reads_${i}_flagstat.txt\n",
    "    \n",
    "    # Convertir le fichier BAM trié en FASTQ\n",
    "    samtools fastq $SORTED_BAM > ${OUTPUT_DIR}/reads_${i}_vs_riz_mapped.fastq\n",
    "    \n",
    "    # Supprimer les fichiers SAM et BAM intermédiaires pour économiser de l'espace\n",
    "    rm $SAM_FILE\n",
    "\n",
    "    echo \"Traitement de ${i} terminé.\"\n",
    "done\n",
    "\n",
    "echo \"Fin du traitement.\""
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "55215eb0",
   "metadata": {},
   "outputs": [],
   "source": [
    "sbatch mapping_pipeline2.sh "
   ]
  },
  {
   "cell_type": "markdown",
   "id": "a4287f81",
   "metadata": {},
   "source": [
    "Submitted batch job 360548"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "739eb2e5",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Copy script and Mapping directories to NAS \n",
    "\n",
    "scp -r /scratch/nikema/scripts/ san:/share/teams/xplain/CIBIG_2024_tutored_project\n",
    "\n",
    "scp -r /scratch/nikema/Mapping/ san:/share/teams/xplain/CIBIG_2024_tutored_project\n",
    "\n",
    "scp -r /scratch/nikema/MAPPING2_OUTPUT/ san:/share/teams/xplain/CIBIG_2024_tutored_project"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "adb8a307",
   "metadata": {},
   "source": [
    "# Ressources de SNP"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "d8c27b9f",
   "metadata": {},
   "outputs": [],
   "source": [
    "#Indexation reference genome\n",
    "\n",
    "## Move to REF directory\n",
    "\n",
    "cd /scratch/nikema/REF/\n",
    "\n",
    "## Load samtools module\n",
    "\n",
    "module load samtools/1.9\n",
    "\n",
    "# Index refseq genome\n",
    "\n",
    "samtools faidx /scratch/nikema/REF/MSU7.fasta"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "a0e4753f-cb74-414f-abd0-b188fadf4621",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Move to scripts directory\n",
    "\n",
    "cd /scratch/nikema/scripts"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "807ba16c",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Open nano editor and save snp.sh script\n",
    "\n",
    "nano snp.sh"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "c7be016e",
   "metadata": {},
   "outputs": [],
   "source": [
    "## script2 snp (repris ok ok)\n",
    "\n",
    "#!/bin/bash\n",
    "#SBATCH --job-name=SNP_Calling              # Job name\n",
    "#SBATCH --ntasks=1                          # One task\n",
    "#SBATCH --cpus-per-task=16                   # Use 16 CPUs (maximum for this job)\n",
    "### Specify the node to run on\n",
    "#SBATCH --nodelist=node20                   # Spécification du nœud\n",
    "\n",
    "\n",
    "# Load required modules\n",
    "module load bcftools/1.18\n",
    "module load vcftools/0.1.16\n",
    "module load htslib/1.19\n",
    "\n",
    "# Define directory paths\n",
    "INPUT_DIR=/scratch/nikema/MAPPING2_OUTPUT      # Path to input BAM files\n",
    "OUTPUT_DIR=/scratch/nikema/SNP                 # Path to store results\n",
    "REF_DIR=/scratch/nikema/REF                    # Path to the reference genome\n",
    "\n",
    "# Create necessary output directories if they don't exist\n",
    "mkdir -p ${OUTPUT_DIR}/bcf_files ${OUTPUT_DIR}/vcf_files ${OUTPUT_DIR}/stats_files\n",
    "\n",
    "# Loop over the sequences (AS and STD)\n",
    "for sequence in AS STD\n",
    "do\n",
    "    echo -e \" ######################\\n Processing for ${sequence} \"\n",
    "\n",
    "    # 1. Generate BCF files from BAM files\n",
    "    echo \"Generating BCF file for ${sequence}...\"\n",
    "    # Define the input BAM file\n",
    "    input_bam=\"${INPUT_DIR}/reads_${sequence}_vs_riz_mapped_sort.bam\"\n",
    "    \n",
    "    # Run mpileup to generate a BCF file\n",
    "    bcftools mpileup --threads 8 \\\n",
    "        -f ${REF_DIR}/MSU7.fasta \\\n",
    "        -O b -o ${OUTPUT_DIR}/bcf_files/${sequence}.bcf \\\n",
    "        ${input_bam}\n",
    "\n",
    "    # 2. Variant calling: Create VCF files from the generated BCF files\n",
    "    echo \"Creating VCF file for ${sequence}...\"\n",
    "    # Run bcftools call to generate VCF from the BCF\n",
    "    bcftools call --threads 8 -v -c \\\n",
    "        -o ${OUTPUT_DIR}/vcf_files/${sequence}.vcf \\\n",
    "        ${OUTPUT_DIR}/bcf_files/${sequence}.bcf\n",
    "\n",
    "    # 3. Compress the VCF files\n",
    "    echo \"Compressing VCF file for ${sequence}...\"\n",
    "    # Compress the VCF file with bgzip\n",
    "    bgzip -c ${OUTPUT_DIR}/vcf_files/${sequence}.vcf > ${OUTPUT_DIR}/vcf_files/${sequence}.vcf.gz\n",
    "    \n",
    "    # 4. Index the compressed VCF file\n",
    "    echo \"Indexing VCF file for ${sequence}...\"\n",
    "    # Create an index for the compressed VCF file\n",
    "    bcftools index ${OUTPUT_DIR}/vcf_files/${sequence}.vcf.gz\n",
    "\n",
    "    # 5. Generate statistics for the VCF file\n",
    "    echo \"Generating statistics for VCF file for ${sequence}...\"\n",
    "    # Use bcftools stats to generate statistics for the VCF\n",
    "    bcftools stats ${OUTPUT_DIR}/vcf_files/${sequence}.vcf.gz > ${OUTPUT_DIR}/stats_files/${sequence}_statistics.txt\n",
    "\n",
    "    # 6. Calculate allele frequencies for the VCF file\n",
    "    echo \"Calculating allele frequencies for VCF file for ${sequence}...\"\n",
    "    # Use vcftools to calculate allele frequencies for the VCF\n",
    "    vcftools --gzvcf ${OUTPUT_DIR}/vcf_files/${sequence}.vcf.gz --freq --out ${OUTPUT_DIR}/stats_files/AF_${sequence} --max-alleles 2\n",
    "\n",
    "done\n",
    "\n",
    "echo \"All steps completed successfully.\"\n",
    "\n",
    "\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "1c3e99bd",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Lunch script\n",
    "\n",
    "sbatch snp.sh "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "44f46a22",
   "metadata": {},
   "outputs": [],
   "source": [
    "squeue"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "8b4f5c63",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Move to SNP directory and list content\n",
    "\n",
    "cd /scratch/nikema/SNP/\n",
    "\n",
    "ls"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "8810ade8",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Move to vcf_filtered directory and list content\n",
    "\n",
    "cd vcf_filtered/\n",
    "\n",
    "ls"
   ]
  },
  {
   "cell_type": "raw",
   "id": "fe1acfbd-0960-4572-989b-facd8153f50f",
   "metadata": {},
   "source": [
    "AS.filtered.vcf.gz  STD.filtered.vcf.gz"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "50e763cc",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Copy Results to NAS\n",
    "\n",
    "scp -r /scratch/nikema/scripts/ san:/share/teams/xplain/CIBIG_2024_tutored_project\n",
    "\n",
    "scp -r /scratch/nikema/SNP/ san:/share/teams/xplain/CIBIG_2024_tutored_project"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "d9f07d05",
   "metadata": {},
   "source": [
    "# STEPS - PCA - DAPC - autres"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "c9e6cfe5",
   "metadata": {},
   "outputs": [],
   "source": [
    "## ## Copy vcf files of 11 refseq genomes from NAS to REF directory in working directory\n",
    "\n",
    "scp -r san:/share/teams/xplain/CIBIG_2024_tutored_project/variants_ressources/rice_species.vcf.gz /scrath/nikema/REF\n",
    "\n",
    "## indexation\n",
    "\n",
    "bcftools index /scratch/nikema/REF/rice_species.vcf.gz"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "e57f2b4e",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Create vcf_merge directory in SNP directory\n",
    "\n",
    "mkdir -p /scratch/nikema/SNP/vcf_merge\n",
    "\n",
    "## Move in\n",
    "\n",
    "cd /scratch/nikema/SNP/vcf_merge"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "0a0c0c19",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Merge files\n",
    "\n",
    "# Load required modules\n",
    "module load bcftools/1.18\n",
    "\n",
    "## fusionner les fichiers compressés vcf.gz apres le snp avec les fichiers vcf.gz des 11 génomes par bcftools avec l'option \"merge\"\n",
    "\n",
    "bcftools merge --threads 2 \\\n",
    "    -Oz -o /scratch/nikema/SNP/vcf_merge/merged_AS.vcf.gz \\\n",
    "    /scratch/nikema/REF/rice_species.vcf.gz \\\n",
    "    /scratch/nikema/SNP/vcf_correct_files/AS_correct.vcf.gz\n",
    "\n",
    "bcftools merge --threads 2 \\\n",
    "    -Oz -o /scratch/nikema/SNP/vcf_merge/merged_STD.vcf.gz \\\n",
    "    /scratch/nikema/REF/rice_species.vcf.gz \\\n",
    "    /scratch/nikema/SNP/vcf_correct_files/STD_correct.vcf.gz"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "4258a746-9995-4205-8eb1-0f8d71ff7cc2",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Move to scripts directory\n",
    "\n",
    "cd/scratch/nikema/scripts\n",
    "\n",
    "# Open nano editor and save script\n",
    "\n",
    "nano"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "6f0c38db",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Script pour corriger les identité, ID_recodage.sh\n",
    "\n",
    "#!/bin/bash\n",
    "\n",
    "#SBATCH --job-name=ID_recodage             # Job name\n",
    "#SBATCH --ntasks=1                          # One task\n",
    "#SBATCH --cpus-per-task=2                  # Use 2 CPUs (maximum for this job)\n",
    "### Specify the node to run on\n",
    "#SBATCH --nodelist=node20                   # Spécification du nœud\n",
    "\n",
    "# Charger les modules nécessaires\n",
    "module load bcftools/1.18\n",
    "\n",
    "# Définir les répertoires d'entrée et de sortie\n",
    "INPUT_DIR=\"/scratch/nikema/SNP/vcf_files\"\n",
    "OUTPUT_DIR=\"/scratch/nikema/SNP/vcf_correct_files\"\n",
    "\n",
    "# Créer un répertoire de sortie si nécessaire\n",
    "mkdir -p \"$OUTPUT_DIR\"\n",
    "\n",
    "# Récupérer les fichiers VCF.gz dans le répertoire d'entrée\n",
    "VCF_FILES=(\"$INPUT_DIR\"/*.vcf.gz)\n",
    "\n",
    "# Boucle sur chaque fichier VCF\n",
    "for VCF_INPUT in \"${VCF_FILES[@]}\"; do\n",
    "    # Extraire le nom de base sans extension\n",
    "    BASENAME=$(basename \"$VCF_INPUT\" .vcf.gz)\n",
    "\n",
    "    # Fichiers intermédiaires\n",
    "    SAMPLES_FILE=\"${OUTPUT_DIR}/${BASENAME}_samples.txt\"\n",
    "    NEW_SAMPLES_FILE=\"${OUTPUT_DIR}/${BASENAME}_new_samples.txt\"\n",
    "    SIMPLIFIED_NAMES_FILE=\"${OUTPUT_DIR}/${BASENAME}_new2_samples.txt\"\n",
    "    VCF_OUTPUT=\"${OUTPUT_DIR}/${BASENAME}_correct.vcf.gz\"\n",
    "\n",
    "    # Étape 1 : Extraire la liste des échantillons\n",
    "    echo \"Extraction des échantillons depuis $VCF_INPUT...\"\n",
    "    bcftools query -l \"$VCF_INPUT\" > \"$SAMPLES_FILE\"\n",
    "\n",
    "    # Étape 2 : Modifier les noms et générer new_samples.txt\n",
    "    echo \"Modification des noms d'échantillons pour créer un fichier de mapping...\"\n",
    "    awk -F'/' '{split($NF, a, \"_\"); print $0 \"\\t\" a[2]}' \"$SAMPLES_FILE\" > \"$NEW_SAMPLES_FILE\"\n",
    "\n",
    "    # Étape 3 : Récupérer uniquement les nouveaux noms d'isolats dans un fichier texte\n",
    "    echo \"Extraction des noms simplifiés dans $SIMPLIFIED_NAMES_FILE...\"\n",
    "    cut -f2 \"$NEW_SAMPLES_FILE\" > \"$SIMPLIFIED_NAMES_FILE\"\n",
    "\n",
    "    # Étape 4 : Appliquer les modifications avec bcftools reheader\n",
    "    echo \"Application des modifications dans le fichier VCF $VCF_INPUT...\"\n",
    "    bcftools reheader -s \"$SIMPLIFIED_NAMES_FILE\" \"$VCF_INPUT\" -o \"$VCF_OUTPUT\"\n",
    "\n",
    "    # Étape 5 : Reindexer le fichier VCF corrigé\n",
    "    echo \"Reindexation du fichier $VCF_OUTPUT...\"\n",
    "    bcftools index \"$VCF_OUTPUT\"\n",
    "\n",
    "    echo \"Traitement terminé pour $VCF_INPUT. Le fichier corrigé est $VCF_OUTPUT.\"\n",
    "done\n",
    "\n",
    "echo \"Tous les fichiers VCF ont été corrigés et reindexés.\""
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "cb57dfe8",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Lunch the script\n",
    "\n",
    "sbatch "
   ]
  },
  {
   "cell_type": "markdown",
   "id": "2a459a39",
   "metadata": {},
   "source": [
    "## Générer une ACP à partir des informations de génotypage contenues dans le fichier VCF"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "508e8d25",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Create PLINK directory in working directory\n",
    "\n",
    "mkdir /scratch/nikema/PLINK\n",
    "\n",
    "# Move in\n",
    "\n",
    "cd /scratch/nikema/PLINK"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "0f85fe14",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Load plink module\n",
    "\n",
    "module load plink/1.9"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "ad2b6f0b",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Lunch\n",
    "\n",
    "plink -vcf /scratch/nikema/SNP/vcf_merge/merged_AS.vcf.gz --allow-extra-chr --distance-matrix --pca 3 --out ./AS/dataset\n",
    "\n",
    "plink -vcf /scratch/nikema/SNP/vcf_merge/merged_STD.vcf.gz --allow-extra-chr --distance-matrix --pca 3 --out ./STD/dataset\n",
    "\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "07537495",
   "metadata": {},
   "source": [
    "## Convertissons le fichier \"eigenvec\" généré en format csv"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "ea118297",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Se déplacer dans le répertoire créé\n",
    "\n",
    "cd /scratch/nikema/scripts"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "ae99c342",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Ouvrir l'éditeur de texte nano\n",
    "\n",
    "nano eigenvec_to_csv.sh"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "270aee9c",
   "metadata": {},
   "outputs": [],
   "source": [
    "#!/bin/bash\n",
    "\n",
    "############# SLURM Configuration ##############\n",
    "\n",
    "### Define Job name\n",
    "#SBATCH --job-name=eigenvec_to_csv\n",
    "\n",
    "### Define partition to use\n",
    "#SBATCH -p normal\n",
    "\n",
    "### Define number of CPUs to use\n",
    "#SBATCH -c 2\n",
    "\n",
    "### Specify the node to run on\n",
    "#SBATCH --nodelist=node20  # Run the job on node20\n",
    "\n",
    "#################################################\n",
    "\n",
    "########### Execution Command ###################\n",
    "\n",
    "# Define directories\n",
    "INPUT_DIR=\"/scratch/nikema/PLINK\"\n",
    "OUTPUT_DIR=\"/scratch/nikema/PLINK\"\n",
    "\n",
    "# List of directories to process\n",
    "DIRECTORIES=(\"AS\" \"STD\")\n",
    "\n",
    "# Loop through each directory\n",
    "for DIR in \"${DIRECTORIES[@]}\"; do\n",
    "    PCA_FILE=\"$INPUT_DIR/$DIR/dataset.eigenvec\"\n",
    "    OUTPUT_CSV=\"$OUTPUT_DIR/$DIR/dataset.csv\"\n",
    "\n",
    "    # Check if PCA results exist\n",
    "    if [ -f \"$PCA_FILE\" ]; then\n",
    "        echo \"Processing PCA results for $DIR...\"\n",
    "\n",
    "        # Convert eigenvec file to CSV format\n",
    "        awk 'NR==1{print \"FID,IID,PC1,PC2,PC3\"} NR>1{print $1\",\"$2\",\"$3\",\"$4\",\"$5}' \"$PCA_FILE\" > \"$OUTPUT_CSV\"\n",
    "\n",
    "        # Check if conversion was successful\n",
    "        if [ $? -eq 0 ]; then\n",
    "            echo \"Conversion successful: $OUTPUT_CSV created.\"\n",
    "        else\n",
    "            echo \"Error: Failed to convert $PCA_FILE to CSV.\"\n",
    "            exit 1\n",
    "        fi\n",
    "    else\n",
    "        echo \"Error: PCA results file not found in $DIR.\"\n",
    "        exit 1\n",
    "    fi\n",
    "done\n",
    "\n",
    "echo \"PCA analysis completed. Results are saved in $OUTPUT_DIR.\""
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "ff650087",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Lancer le script\n",
    "\n",
    "sbash eigenvec_to_csv.sh"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "7895b0d8",
   "metadata": {},
   "source": [
    "## Sortir un plot pour la PCA afin de faciliter la visualisation"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "50f5f89a",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Ouvrir l'éditeur de texte nano\n",
    "\n",
    "nano pca_plot.sh"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "89150761",
   "metadata": {},
   "outputs": [],
   "source": [
    "#!/bin/bash\n",
    "\n",
    "############# SLURM Configuration ##############\n",
    "\n",
    "### Define Job name\n",
    "#SBATCH --job-name=merge_pca_plot\n",
    "\n",
    "### Define partition to use\n",
    "#SBATCH -p normal\n",
    "\n",
    "### Define number of CPUs to use\n",
    "#SBATCH -c 2\n",
    "\n",
    "### Specify the node to run on\n",
    "#SBATCH --nodelist=node20\n",
    "\n",
    "#################################################\n",
    "\n",
    "########### Execution Command ###################\n",
    "\n",
    "module load python/3.12.0  # Charge Python 3.12 sur le cluster\n",
    "\n",
    "# Define directories\n",
    "PCA_RESULTS_DIR=\"/scratch/nikema/PLINK\"\n",
    "OUTPUT_PLOT_DIR=\"/scratch/nikema/PLINK\"\n",
    "\n",
    "# List of directories to process\n",
    "DIRECTORIES=(\"AS\" \"STD\")\n",
    "\n",
    "# Loop through each directory\n",
    "for DIRECTORY in \"${DIRECTORIES[@]}\"; do\n",
    "    PCA_FILE=\"$PCA_RESULTS_DIR/$DIRECTORY/dataset.eigenvec\"\n",
    "    OUTPUT_DIR=\"$OUTPUT_PLOT_DIR/$DIRECTORY\"\n",
    "    OUTPUT_PLOT_2D=\"$OUTPUT_DIR/dataset_2D.png\"\n",
    "    OUTPUT_PLOT_3D=\"$OUTPUT_DIR/dataset_3D.png\"\n",
    "\n",
    "    # Ensure the output directory exists\n",
    "    mkdir -p \"$OUTPUT_DIR\"\n",
    "\n",
    "    # Check if the PCA results file exists\n",
    "    if [ -f \"$PCA_FILE\" ]; then\n",
    "        echo \"Processing PCA results for $DIRECTORY...\"\n",
    "        \n",
    "        # Call the Python script to generate the plots\n",
    "        python3 <<EOF\n",
    "import pandas as pd\n",
    "import matplotlib.pyplot as plt\n",
    "from mpl_toolkits.mplot3d import Axes3D\n",
    "import os\n",
    "\n",
    "# Define input and output paths\n",
    "pca_results_file = \"$PCA_FILE\"\n",
    "output_plot_2D = \"$OUTPUT_PLOT_2D\"\n",
    "output_plot_3D = \"$OUTPUT_PLOT_3D\"\n",
    "\n",
    "# Read PCA results\n",
    "try:\n",
    "    pca_results = pd.read_csv(pca_results_file, sep=r'\\s+', header=None)\n",
    "    pca_results.columns = ['FID', 'IID', 'PC1', 'PC2', 'PC3']\n",
    "except Exception as e:\n",
    "    print(f\"Error reading PCA results file {pca_results_file}: {e}\")\n",
    "    exit(1)\n",
    "\n",
    "# Plot 2D scatter plot for PC1 vs PC2\n",
    "try:\n",
    "    plt.figure(figsize=(8, 6))\n",
    "    plt.scatter(pca_results['PC1'], pca_results['PC2'], s=100)\n",
    "    plt.title('PCA Results: $DIRECTORY (2D)')\n",
    "    plt.xlabel('Principal Component 1')\n",
    "    plt.ylabel('Principal Component 2')\n",
    "    plt.grid()\n",
    "    plt.savefig(output_plot_2D)\n",
    "    plt.close()\n",
    "except Exception as e:\n",
    "    print(f\"Error creating 2D plot for {pca_results_file}: {e}\")\n",
    "    exit(1)\n",
    "\n",
    "# Plot 3D scatter plot for PC1, PC2, and PC3\n",
    "try:\n",
    "    fig = plt.figure(figsize=(10, 8))\n",
    "    ax = fig.add_subplot(111, projection='3d')\n",
    "    scatter = ax.scatter(pca_results['PC1'], pca_results['PC2'], pca_results['PC3'], s=100, c='blue', alpha=0.7)\n",
    "    ax.set_title('PCA Results: $DIRECTORY (3D)')\n",
    "    ax.set_xlabel('Principal Component 1')\n",
    "    ax.set_ylabel('Principal Component 2')\n",
    "    ax.set_zlabel('Principal Component 3')\n",
    "    plt.savefig(output_plot_3D)\n",
    "    plt.close()\n",
    "except Exception as e:\n",
    "    print(f\"Error creating 3D plot for {pca_results_file}: {e}\")\n",
    "    exit(1)\n",
    "\n",
    "# Verify plots were created\n",
    "if not os.path.exists(output_plot_2D) or not os.path.exists(output_plot_3D):\n",
    "    print(f\"Error: Output plots not created for {pca_results_file}\")\n",
    "    exit(1)\n",
    "EOF\n",
    "\n",
    "        # Check if the Python script executed successfully\n",
    "        if [ $? -eq 0 ]; then\n",
    "            echo \"Plots successfully created for $DIRECTORY: $OUTPUT_PLOT_2D, $OUTPUT_PLOT_3D\"\n",
    "        else\n",
    "            echo \"Error: Failed to create plots for $DIRECTORY.\"\n",
    "            exit 1\n",
    "        fi\n",
    "    else\n",
    "        echo \"Error: PCA results file not found in $DIRECTORY.\"\n",
    "        exit 1\n",
    "    fi\n",
    "done\n",
    "\n",
    "echo \"All PCA plots created successfully.\"\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "d04931e1",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Lancer le script\n",
    "\n",
    "sbash pca_plot.sh"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "5b55c881",
   "metadata": {},
   "outputs": [],
   "source": [
    "## voir le contenu du slurm (log)du job si ça a marché\n",
    "\n",
    "less slurm-374509.out \n",
    "\n",
    "## possible de supprimer après\n",
    "\n",
    "rm slurm-374509.out "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "69b1df89",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Faire un regroupement avec un script (éditeur de texte nano)\n",
    "\n",
    "nano clustering_analysis.py"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "7d865498",
   "metadata": {},
   "outputs": [],
   "source": [
    "import sys\n",
    "import os\n",
    "import pandas as pd\n",
    "import matplotlib.pyplot as plt\n",
    "from mpl_toolkits.mplot3d import Axes3D\n",
    "from sklearn.cluster import KMeans, DBSCAN\n",
    "from sklearn.metrics import silhouette_score\n",
    "\n",
    "# Check for input arguments\n",
    "if len(sys.argv) != 3:\n",
    "    print(\"Usage: python clustering_analysis.py <PCA_FILE> <OUTPUT_DIR>\")\n",
    "    sys.exit(1)\n",
    "\n",
    "# Input and output paths\n",
    "pca_file = sys.argv[1]\n",
    "output_dir = sys.argv[2]\n",
    "\n",
    "# Create output directory if it doesn't exist\n",
    "os.makedirs(output_dir, exist_ok=True)\n",
    "\n",
    "# Load PCA data\n",
    "try:\n",
    "    print(f\"Loading PCA data from {pca_file}...\")\n",
    "    data = pd.read_csv(pca_file, sep=r'\\s+', header=None)\n",
    "    data.columns = ['FID', 'IID', 'PC1', 'PC2', 'PC3']\n",
    "    X = data[['PC1', 'PC2', 'PC3']]\n",
    "except Exception as e:\n",
    "    print(f\"Error loading PCA data: {e}\")\n",
    "    sys.exit(1)\n",
    "\n",
    "# Determine optimal number of clusters using the elbow method\n",
    "print(\"Calculating optimal number of clusters (Elbow Method)...\")\n",
    "inertia = []\n",
    "k_values = range(1, 10)\n",
    "for k in k_values:\n",
    "    kmeans = KMeans(n_clusters=k, random_state=42, n_init='auto')\n",
    "    kmeans.fit(X)\n",
    "    inertia.append(kmeans.inertia_)\n",
    "\n",
    "plt.figure(figsize=(8, 5))\n",
    "plt.plot(k_values, inertia, marker='o')\n",
    "plt.title('Elbow Method')\n",
    "plt.xlabel('Number of Clusters (k)')\n",
    "plt.ylabel('Inertia')\n",
    "elbow_plot_path = os.path.join(output_dir, 'elbow_method.png')\n",
    "plt.savefig(elbow_plot_path)\n",
    "print(f\"Elbow Method plot saved to {elbow_plot_path}\")\n",
    "\n",
    "# Apply k-means clustering with k=3 (or adjust based on elbow results)\n",
    "print(\"Applying k-means clustering...\")\n",
    "kmeans = KMeans(n_clusters=3, random_state=42, n_init='auto')\n",
    "data['Cluster'] = kmeans.fit_predict(X)\n",
    "\n",
    "# Sauvegarder les résultats avec les clusters dans un fichier CSV\n",
    "output_csv = os.path.join(output_dir, 'clustered_data.csv')\n",
    "data.to_csv(output_csv, index=False)\n",
    "print(f\"Données annotées avec clusters sauvegardées dans {output_csv}\")\n",
    "\n",
    "\n",
    "# Visualize k-means clusters in 3D\n",
    "print(\"Generating 3D k-means plot...\")\n",
    "fig = plt.figure(figsize=(10, 8))\n",
    "ax = fig.add_subplot(111, projection='3d')\n",
    "scatter = ax.scatter(data['PC1'], data['PC2'], data['PC3'], c=data['Cluster'], cmap='viridis', s=100)\n",
    "plt.colorbar(scatter, ax=ax)\n",
    "ax.set_title('k-means Clustering (k=3)')\n",
    "ax.set_xlabel('PC1')\n",
    "ax.set_ylabel('PC2')\n",
    "ax.set_zlabel('PC3')\n",
    "kmeans_plot_path = os.path.join(output_dir, 'kmeans_pca_plot_3d.png')\n",
    "plt.savefig(kmeans_plot_path)\n",
    "print(f\"3D k-means plot saved to {kmeans_plot_path}\")\n",
    "\n",
    "# Apply DBSCAN clustering\n",
    "print(\"Applying DBSCAN clustering...\")\n",
    "dbscan = DBSCAN(eps=0.1, min_samples=3)\n",
    "data['DBSCAN_Cluster'] = dbscan.fit_predict(X)\n",
    "\n",
    "# Visualize DBSCAN clusters in 3D\n",
    "fig = plt.figure(figsize=(10, 8))\n",
    "ax = fig.add_subplot(111, projection='3d')\n",
    "scatter = ax.scatter(data['PC1'], data['PC2'], data['PC3'], c=data['DBSCAN_Cluster'], cmap='plasma', s=100)\n",
    "plt.colorbar(scatter, ax=ax)\n",
    "ax.set_title('DBSCAN Clustering')\n",
    "ax.set_xlabel('PC1')\n",
    "ax.set_ylabel('PC2')\n",
    "ax.set_zlabel('PC3')\n",
    "dbscan_plot_path = os.path.join(output_dir, 'dbscan_pca_plot_3d.png')\n",
    "plt.savefig(dbscan_plot_path)\n",
    "print(f\"3D DBSCAN plot saved to {dbscan_plot_path}\")\n",
    "\n",
    "# Calculate silhouette scores for different k\n",
    "print(\"Calculating silhouette scores...\")\n",
    "silhouette_scores = []\n",
    "for k in range(2, 10):\n",
    "    kmeans = KMeans(n_clusters=k, random_state=42, n_init='auto')\n",
    "    labels = kmeans.fit_predict(X)\n",
    "    silhouette_scores.append(silhouette_score(X, labels))\n",
    "\n",
    "plt.figure(figsize=(8, 5))\n",
    "plt.plot(range(2, 10), silhouette_scores, marker='o')\n",
    "plt.title('Silhouette Scores')\n",
    "plt.xlabel('Number of Clusters (k)')\n",
    "plt.ylabel('Silhouette Score')\n",
    "silhouette_plot_path = os.path.join(output_dir, 'silhouette_scores.png')\n",
    "plt.savefig(silhouette_plot_path)\n",
    "print(f\"Silhouette Scores plot saved to {silhouette_plot_path}\")\n",
    "\n",
    "print(\"Clustering analysis completed.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "2049457e",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Ouvrir l'éditeur de texte nano\n",
    "\n",
    "nano pca_learning.sh"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "274e9e05",
   "metadata": {},
   "outputs": [],
   "source": [
    "#!/bin/bash\n",
    "\n",
    "############# SLURM Configuration ##############\n",
    "#SBATCH --job-name=pca_learning\n",
    "#SBATCH -p normal\n",
    "#SBATCH -c 2\n",
    "#SBATCH --nodelist=node20\n",
    "\n",
    "#################################################\n",
    "\n",
    "# Load necessary modules\n",
    "module load python/3.12.0\n",
    "\n",
    "# Define directories\n",
    "PCA_RESULTS_DIR=\"/scratch/nikema/PLINK\"\n",
    "OUTPUT_PLOT_DIR=\"/scratch/nikema/PLINK/PCA\"\n",
    "\n",
    "# Create output directory if it doesn't exist\n",
    "mkdir -p \"$OUTPUT_PLOT_DIR\"\n",
    "\n",
    "# List of subdirectories to process\n",
    "DIRECTORIES=(\"AS\" \"STD\")\n",
    "\n",
    "# Loop over each directory\n",
    "for DIRECTORY in \"${DIRECTORIES[@]}\"; do\n",
    "    PCA_FILE=\"$PCA_RESULTS_DIR/$DIRECTORY/dataset.eigenvec\"\n",
    "    OUTPUT_DIR=\"$OUTPUT_PLOT_DIR/$DIRECTORY\"\n",
    "\n",
    "    # Check if the PCA file exists\n",
    "    if [[ -f \"$PCA_FILE\" ]]; then\n",
    "        mkdir -p \"$OUTPUT_DIR\"\n",
    "        echo \"Processing PCA file: $PCA_FILE\"\n",
    "\n",
    "        # Run the Python script for clustering and plotting\n",
    "        python3 clustering_analysis.py \"$PCA_FILE\" \"$OUTPUT_DIR\"\n",
    "\n",
    "        echo \"Processing completed for directory: $DIRECTORY\"\n",
    "    else\n",
    "        echo \"PCA file not found: $PCA_FILE\"\n",
    "    fi\n",
    "done"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "5c634b58",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Lancer le script\n",
    "\n",
    "sbash pca_learning.sh"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "16f6757f",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Donner les droits\n",
    "\n",
    "chmod -R 775 scripts/ QC/ MAPPING2_OUTPUT/ REF/ SNP/ PLINK/"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "74c3fb31",
   "metadata": {},
   "outputs": [],
   "source": [
    "Aller visualiser les plots"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "4f89856a",
   "metadata": {},
   "outputs": [],
   "source": [
    "#### Copier les données du cluster vers disque dur (le repertoire commun)\n",
    "\n",
    "scp -r PLINK/ san:/share/teams/xplain/CIBIG_2024_tutored_project\n",
    "\n",
    "scp -r PLINK/ san:/share/teams/xplain/CIBIG_2024_tutored_project"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "e15000d9",
   "metadata": {},
   "outputs": [],
   "source": [
    "## copier les données du disque dur vers ma machine\n",
    "scp -r nikema@bioinfo-san.ird.fr:/share/teams/xplain/CIBIG_2024_tutored_project/PLINK/ /home/edith/Certificat_CIBiG2024/Projet_tutore2024/\n",
    "\n",
    "scp -r nikema@bioinfo-san.ird.fr:/share/teams/xplain/CIBIG_2024_tutored_project/PLINK/ /home/edith/Certificat_CIBiG2024/Projet_tutore2024/"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "d584863f",
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "markdown",
   "id": "827b155e",
   "metadata": {},
   "source": [
    "## Analyzing and Interpreting the Generated Plots"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "0971d98e",
   "metadata": {},
   "source": [
    "## Discriminant analysis of principal components (DAPC)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "3d06acdb",
   "metadata": {},
   "outputs": [],
   "source": [
    "Avec R et adegenet :\n",
    "\n",
    "Installez et chargez le package :\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "68a84b3c",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Charger les bibliothèques nécessaires\n",
    "if (!requireNamespace(\"adegenet\")) install.packages(\"adegenet\")\n",
    "if (!requireNamespace(\"factoextra\")) install.packages(\"factoextra\") # Pour le clustering\n",
    "if (!requireNamespace(\"ggplot2\")) install.packages(\"ggplot2\")\n",
    "if (!requireNamespace(\"dplyr\")) install.packages(\"dplyr\")\n",
    "if (!requireNamespace(\"scatterplot3d\")) install.packages(\"scatterplot3d\")\n",
    "\n",
    "library(adegenet)\n",
    "library(factoextra)\n",
    "library(ggplot2)\n",
    "library(dplyr)\n",
    "library(scatterplot3d)\n",
    "\n",
    "# Définir les chemins d'entrée et de sortie\n",
    "input_file <- \"/home/edith/Certificat_CIBiG2024/Projet_tutore2024/PLINK/AS/dataset.eigenvec\"\n",
    "output_dir <- \"/home/edith/Certificat_CIBiG2024/Projet_tutore2024/PLINK/DAPC/AS\"\n",
    "\n",
    "# Créer le répertoire de sortie si nécessaire\n",
    "if (!dir.exists(output_dir)) {\n",
    "  dir.create(output_dir, recursive = TRUE)\n",
    "  cat(\"Répertoire de sortie créé :\", output_dir, \"\\n\")\n",
    "}\n",
    "\n",
    "# Charger les données PCA\n",
    "cat(\"Chargement des données PCA...\\n\")\n",
    "pca_data <- read.table(input_file, header = FALSE)\n",
    "colnames(pca_data) <- c(\"FID\", \"IID\", \"PC1\", \"PC2\", \"PC3\")  # Modifier selon vos colonnes\n",
    "X <- pca_data %>% select(PC1, PC2, PC3)\n",
    "\n",
    "# Étape 1 : Clustering des individus (k-means)\n",
    "cat(\"Application de k-means pour générer des groupes...\\n\")\n",
    "set.seed(42)  # Pour assurer la reproductibilité\n",
    "n_clusters <- 3  # Ajustez selon vos besoins ou utilisez la méthode du coude\n",
    "kmeans_result <- kmeans(X, centers = n_clusters)\n",
    "\n",
    "# Ajouter les groupes au jeu de données\n",
    "pca_data$Group <- as.factor(kmeans_result$cluster)\n",
    "\n",
    "# Étape 2 : DAPC\n",
    "cat(\"Optimisation du nombre de PCs pour DAPC...\\n\")\n",
    "dapc_initial <- dapc(X, pca_data$Group)\n",
    "optimal_pcs <- optim.a.score(dapc_initial)\n",
    "n_pcs <- optimal_pcs$n.pca\n",
    "cat(paste(\"Nombre optimal de PCs :\", n_pcs, \"\\n\"))\n",
    "\n",
    "# Réaliser la DAPC avec le nombre optimal de PCs\n",
    "dapc_result <- dapc(X, pca_data$Group, n.pca = n_pcs)\n",
    "\n",
    "# Ajouter les couleurs pour chaque groupe\n",
    "colors <- rainbow(n_clusters)\n",
    "\n",
    "# Étape 3 : Visualisation des résultats\n",
    "cat(\"Génération des graphiques DAPC...\\n\")\n",
    "\n",
    "# Graphique DAPC 2D avec noms des isolats\n",
    "scatter_file <- file.path(output_dir, \"dapc_scatter_with_labels.png\")\n",
    "png(scatter_file, width = 800, height = 600)\n",
    "scatter(dapc_result, col = colors, pch = 19, cex = 1.5, \n",
    "        posi.da = \"topleft\", scree.pca = FALSE, leg = TRUE)\n",
    "text(dapc_result$ind.coord[, 1], dapc_result$ind.coord[, 2], \n",
    "     labels = pca_data$IID, cex = 0.7, pos = 4, col = \"black\")\n",
    "dev.off()\n",
    "cat(\"Graphique DAPC 2D sauvegardé dans :\", scatter_file, \"\\n\")\n",
    "\n",
    "# Graphique DAPC 3D avec noms des isolats\n",
    "scatter3d_file <- file.path(output_dir, \"dapc_scatter3d_with_labels.png\")\n",
    "png(scatter3d_file, width = 800, height = 600)\n",
    "scatter3d <- scatterplot3d(dapc_result$ind.coord[, 1], \n",
    "                            dapc_result$ind.coord[, 2], \n",
    "                            dapc_result$ind.coord[, 3], \n",
    "                            color = colors[kmeans_result$cluster], \n",
    "                            pch = 19, main = \"DAPC: Projection 3D\")\n",
    "text(scatter3d$xyz.convert(dapc_result$ind.coord[, 1], \n",
    "                            dapc_result$ind.coord[, 2], \n",
    "                            dapc_result$ind.coord[, 3]), \n",
    "     labels = pca_data$IID, col = \"black\", cex = 0.7, pos = 4)\n",
    "dev.off()\n",
    "cat(\"Graphique DAPC 3D sauvegardé dans :\", scatter3d_file, \"\\n\")\n",
    "\n",
    "# Étape 4 : Sauvegarder les résultats\n",
    "results_file <- file.path(output_dir, \"dapc_results_with_clusters.csv\")\n",
    "write.csv(data.frame(dapc_result$ind.coord, Group = pca_data$Group, IID = pca_data$IID), \n",
    "          results_file, row.names = FALSE)\n",
    "cat(\"Résultats DAPC sauvegardés dans :\", results_file, \"\\n\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "4637d6a2",
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "68c17f1e",
   "metadata": {},
   "outputs": [],
   "source": [
    "##\n",
    "\n",
    "chmod -R 775 scripts/ QC/ MAPPING2_OUTPUT/ REF/ SNP/ PLINK/\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "2db97d14",
   "metadata": {},
   "outputs": [],
   "source": [
    "## Copy everything from the (shared) directory to my local computer\n",
    "scp -r nikema@bioinfo-san.ird.fr:/share/teams/xplain/CIBIG_2024_tutored_project/ /home/edith/Certificat_CIBiG2024/Projet_tutore2024/"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "7961f7f8",
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "3237cead",
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "f7c160fa",
   "metadata": {},
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3 (ipykernel)",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.10.12"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}

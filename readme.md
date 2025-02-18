# **BIOINFORMATICS ANALYSIS STRATEGY**

## **Participants**
- Marguerite Edith M. NIKIEMA  
- Jean-Marc ZAOURI  

## **Tuteurs**
- Alexis DEREEPER  
- Sebastien CUNNAC  

*Jupyter Notebook inspired by the model created by C. Tranchant (DIADE-IRD), J. Orjuela (DIADE-IRD), F. Sabot (DIADE-IRD), and A. Dereeper (PHIM-IRD).*

---

## **Table of Contents**
1. [Introduction and Objectives](#introduction-and-objectives)
2. [Cluster Connection and Environment Setup](#cluster-connection-and-environment-setup)
3. [Data Management](#data-management)
4. [Preprocessing](#preprocessing)
5. [Alignment and Assembly](#alignment-and-assembly)
6. [Variant Analysis](#variant-analysis)
7. [Visualization and Interpretation](#visualization-and-interpretation)

---

## **Introduction and Objectives**
Rice (*Oryza sativa*) is a fundamental crop for global food security. It is cultivated in various countries, including China. The ability to differentiate rice varieties is crucial for yield improvement, disease resistance, and climate adaptation.

This project aims to prototype a taxonomic placement method for rice leaf "reads" in a Nanopore metagenomic sequencing dataset. 

### **Specific Objectives**
- Determine the rice variety of a leaf sample using metagenomic sequences obtained through standard Nanopore mode and adaptive sampling mode.
- Compare the two sequencing modes to assess their efficiency.

---

## **Cluster Connection and Environment Setup**
### **Connecting to the cluster**
```bash
ssh nikema@bioinfo-master1.ird.fr
```

### **Checking available partitions**
```bash
sinfo
squeue
```

### **Selecting a partition and CPU allocation**
```bash
srun -p normal -c 8 --pty bash -i
```

---

## **Data Management**
### **Creating workspace directories**
```bash
cd /scratch
mkdir nikema
cd nikema
mkdir Data
```

### **Copying data from NAS**
```bash
scp -r san:/share/teams/xplain/CIBIG_2024_tutored_project/seqModeSplitSeqs/ /scratch/nikema/Data
```

### **Listing and inspecting FASTQ files**
```bash
ls /scratch/nikema/Data/seqModeSplitSeqs/
```

---

## **Quality Control (QC) Using NanoPlot**
### **Loading NanoPlot module**
```bash
module load nanoplot/1.43.0
```

### **Running QC on all FASTQ files**
```bash
NanoPlot -t 8 --fastq /scratch/nikema/Data/seqModeSplitSeqs/*_STD.fastq.gz -o /scratch/nikema/QC/STD
NanoPlot -t 8 --fastq /scratch/nikema/Data/seqModeSplitSeqs/*_AS.fastq.gz -o /scratch/nikema/QC/AS
```

### **Running QC via SLURM script**

```bash
nano nanoplot.sh
```

```bash
#!/bin/bash
################# SLURM Configuration #################
### Define Job name
#SBATCH --job-name=quality_control   # Nom du job
### Define partition to use
#SBATCH -p normal                   # Partition à utiliser
### Define number of CPUs to use
#SBATCH --cpus-per-task=8           # Utilisation de 8 CPU par tâche
### Specify the node to run on
#SBATCH --nodelist=node20           # Spécification du nœud
### Define memory (optionnel, mais utile pour des jobs gourmands en mémoire)
#SBATCH --mem=16G                   # Exemple de mémoire allouée, ajuster si nécessaire
# Chargement des modules nécessaires
module load nanoplot/1.43.0        # Chargement du module NanoPlot
# Exécution du programme avec les paramètres fournis
# Traitement des fichiers _STD.fastq.gz avec 8 threads et sortie dans le répertoire spécifié
NanoPlot -t 8 --fastq /scratch/nikema/Data/seqModeSplitSeqs/STD.fastq.gz -o /scratch/nikema/QC/STD
# Traitement des fichiers _AS.fastq.gz avec 8 threads et sortie dans le répertoire spécifié
NanoPlot -t 8 --fastq /scratch/nikema/Data/seqModeSplitSeqs/AS.fastq.gz -o /scratch/nikema/QC/AS
```

```bash
sbatch nanoplot.sh
```

---

## **Mapping Reads to the Reference Genome**
### **Downloading and indexing the reference genome**
```bash
wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/034/140/825/GCF_034140825.1_ASM3414082v1/GCF_034140825.1_ASM3414082v1_genomic.fna.gz
gunzip GCF_034140825.1_ASM3414082v1_genomic.fna.gz
```

### **Running mapping with Minimap2 and Samtools**

```bash
nano mapping.sh
```

```bash
#!/bin/bash
################# SLURM Configuration #################
#SBATCH --job-name=mapping   # Nom du job
### Define partition to use
#SBATCH -p normal                   # Partition à utiliser
### Define number of CPUs to use
#SBATCH --cpus-per-task=8           # Utilisation de 8 CPU par tâche
### Specify the node to run on
#SBATCH --nodelist=node20           # Spécification du nœud
### Define memory (optionnel, mais utile pour des jobs gourmands en mémoire)
#SBATCH --mem=16G                   # Exemple de mémoire allouée, ajuster si nécessaire
################# Load Modules #################
# Charger les modules nécessaires
module load minimap2/2.24
module load samtools/1.9
################# Start of the Script #################
# Liste des fichiers fastq.gz (sans extension)
FILES=(\"WA116\" \"NWA119\" \"NWA122\" \"NWA125\" \"NWA128\" \"NWA140\"
       \"NWA141\" \"NWA147\" \"NWA150\" \"NWA153\" \"NWA156\"
       \"NWA118\" \"NWA121\" \"NWA124\" \"NWA127\" \"NWA130\"
       \"NWA143\" \"NWA149\" \"NWA152\" \"NWA155\" \"NWA158\")
# Répertoire des fichiers de référence
REFERENCE_GENOME=\"/scratch/nikema/REF/GCF_034140825.1_ASM3414082v1_genomic.fna
OUTPUT_DIR=\"/scratch/nikema/Mapping/\"
# Créer le répertoire de sortie si nécessaire
mkdir -p ${OUTPUT_DIR}
# Itérer sur les fichiers pour effectuer le mapping et la conversion
for i in \"${FILES[@]}\"; 
    echo \"Traitement de ${i}...\"
    minimap2 -t 8 -ax map-ont $REFERENCE_GENOME /scratch/nikema/Data/seqModeSplitSeqs/${i}_AS.fastq.gz > ${OUTPUT_DIR}/reads_${i}_AS_vs_riz.sam
    minimap2 -t 8 -ax map-ont $REFERENCE_GENOME /scratch/nikema/Data/seqModeSplitSeqs/${i}_STD.fastq.gz > ${OUTPUT_DIR}/reads_${i}_STD_vs_riz.sam
    # Vérifier si les fichiers SAM ont été créés pour AS et STD
    for type in AS STD; do
            echo \"Erreur: le fichier SAM pour ${i}_${type} n'a pas été créé.\
            continue 2  # Passer à l'itération suivante du fichier si erreur
        fi
    done
    # Conversion du fichier SAM en BAM pour AS et STD
    for type in AS STD; do
        samtools view -b -S ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz.sam -o ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz.bam
        # Vérifier si le fichier BAM a été créé
        if [[ ! -f ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz.bam ]]; then
            echo \"Erreur: la conversion SAM->BAM pour ${i}_${type} a échoué.\
            continue 2  # Passer à l'itération suivante du fichier si erreur
        fi
        # Filtrer les reads mappés et créer un fichier BAM pour AS et STD
        samtools view -@ 8 -hb -F 4 ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz.sam -o ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz_mapped.bam
       # Trier les fichiers BAM
        samtools sort -o ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz_mapped_sort.bam ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz_mapped.bam
        # Générer les statistiques avec samtools flagstat
       samtools flagstat ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz.bam > ${OUTPUT_DIR}/reads_${i}_${type}_flagstat.txt
         # Convertir le fichier BAM en FASTQ
        samtools fastq ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz_mapped.bam > ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz_mapped.fastq
       # Supprimer les fichiers SAM et BAM intermédiaires pour économiser de l'espace
        rm ${OUTPUT_DIR}/reads_${i}_${type}_vs_riz.sam
        
    done
    echo \"Traitement de ${i} terminé
done
echo \"Fin du traitement.
```

```bash
sbatch mapping.sh
```

---

## **SNP Variant Calling**
### **Generating VCF Files**
```bash
nano snp.sh
```

```bash
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
```

```bash
sbatch snp.sh
```

### **Merging VCF Files with Reference Data**
```bash
bcftools merge --threads 2 -Oz -o /scratch/nikema/SNP/vcf_merge/merged_AS.vcf.gz /scratch/nikema/REF/rice_species.vcf.gz /scratch/nikema/SNP/vcf_correct_files/AS_correct.vcf.gz
```

### **Performing PCA on VCF Data**
```bash
plink -vcf /scratch/nikema/SNP/vcf_merge/merged_AS.vcf.gz --allow-extra-chr --distance-matrix --pca 3 --out ./AS/dataset
```

---

## **Visualization and Interpretation**
### **Generating PCA Plots**

```bash
nano pca_plot.sh
```

```bash
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
```

```bash
sbatch pca_plot.sh
```


This README provides an overview of the bioinformatics analysis strategy, ensuring reproducibility and clarity in workflow execution.

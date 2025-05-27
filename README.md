
# 🔬 Shotgun Microbiome Docker Pipeline

This README provides a complete guide for running a shotgun metagenomics pipeline using a Docker image built with MetaPhlAn and a custom annotation script.

---

## 🐳 Step 1: Pull the Docker Image

To get started, pull the Docker image:

```bash
docker pull cosmos9526/shotgunmicrobiome:latest
```

This image includes:

* MetaPhlAn for taxonomic profiling
* Python with dependencies for annotation
* A ready-to-run shell environment

---

## 🚀 Step 2: Run the Shotgun Microbiome Pipeline

```bash
sudo docker run --rm --entrypoint /bin/bash -it \
    --user root \
    -v /home/milad/milad/shotgun/result:/data/result \
    -v /home/milad/milad/shotgun:/data/shotgun \
    cosmos9526/shotgunmicrobiome:latest \
    -c "mkdir -p /data/result && \
        metaphlan /data/shotgun/insub732_2_R1.fastq.gz,/data/shotgun/insub732_2_R2.fastq.gz \
        --input_type fastq \
        --bowtie2out /data/result/metagenome.bowtie2.bz2 \
        --nproc 5 \
        -o /data/result/profiled_metagenome.txt"
```

### 🧾 Explanation of Parameters:

| Flag                     | Description                                            |
| ------------------------ | ------------------------------------------------------ |
| `--rm`                   | Removes the container after execution to keep it clean |
| `--entrypoint /bin/bash` | Executes Bash as the entrypoint                        |
| `--user root`            | Runs as the root user                                  |
| `-v ...:/data/result`    | Mounts the result directory from the host              |
| `-v ...:/data/shotgun`   | Mounts the input directory from the host               |
| Inside `-c`              | Executes the main pipeline:                            |
| `metaphlan`              | Runs MetaPhlAn with input FASTQ files                  |
| `--bowtie2out`           | Path to intermediate alignment output                  |
| `--nproc`                | Number of threads used                                 |
| `-o`                     | Output path for taxonomic profile result               |

---

## 🧾 Input File Naming Convention

The input FASTQ files must be **paired-end** and follow this naming structure:

```
<sample_name>_R1.fastq.gz   # Forward reads
<sample_name>_R2.fastq.gz   # Reverse reads
```

📝 **Example:**

```
insub732_2_R1.fastq.gz
insub732_2_R2.fastq.gz
```

This naming ensures correct pairing by tools like MetaPhlAn. Avoid renaming files to anything that breaks the `_R1/_R2` structure.

---

## 🧬 Step 3: Annotate the Results

After generating the taxonomic profile, annotate the results using the following command:

```bash
sudo docker run --rm --entrypoint /bin/bash -it \
    --user root \
    -v /home/milad/milad/shotgun:/data \
    -v /home/milad/milad/shotgun/result:/result \
    cosmos9526/shotgunmicrobiome:latest \
    -c "PYTHONUNBUFFERED=1 /opt/miniforge/bin/python /anot.py --output_directory /result"
```

This step executes the annotation Python script and writes the output into the result directory.

---

## 📂 Output

* `profiled_metagenome.txt`: MetaPhlAn taxonomic profile output
* `metagenome.bowtie2.bz2`: Intermediate Bowtie2 alignment
* Annotated JSON or TSV files in `/result`

---

For questions or customization, refer to the Docker Hub page or contact the image author.

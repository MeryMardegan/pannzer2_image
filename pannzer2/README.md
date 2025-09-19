# PANNZER2 – Docker/Apptainer Image

This repository provides a containerized environment for running **[PANNZER2 Web Server](http://ekhidna2.biocenter.helsinki.fi/sanspanz/)** (Protein ANNotation with Z-scoRE), a tool for large-scale automatic functional annotation of proteins.  

> Note: The PANNZER2 web server is occasionally offline. If the link is unavailable, please try again later or consult the original publication.

The image is designed for use in **HPC environments** with [Apptainer/Singularity](https://apptainer.org/) as well as on local machines with Docker.

---

## Features

- **Base OS:** Ubuntu 22.04 (LTS)
- **Included system dependencies:**
  - Python 3 and pip
  - Perl
  - ncbi-blast+
  - build-essential, cmake, unzip
  - wget, curl, git, ca-certificates
- **Python packages (via `requirements.txt`):**
  - numpy
  - scipy
  - pandas
  - statsmodels
- **Pre-cloned repository:** [ztane/pannzer2](https://github.com/ztane/pannzer2)  
- **Symlinks:** `runsanspanz.py` and `pannzer2.py` available globally in the `$PATH`.

---

## Usage with Docker (local)

### Build locally
```bash
docker build -t pannzer2:latest .
```

### Run with server mode (-R)
```bash
docker run --rm -v $(pwd):/data pannzer2:latest \
    runsanspanzy.py -R -m Pannzer -s "Specie" \
    -i /data/input.faa -o /data/results/csv
```

-v $(pwd):/data mounts the current directory to /data inside the container
-R instructs PANNZER2 to use the remote server (no local databases required).

## Usage with Apptainer/Singularity (HPC)
### Pull from **GitHub Container Registry (GHCR)**

```bash
module load apptainer
apptainer pull pannzer2.sif docker://ghcr.io/<MeryMardegan>/pannzer2:latest
```

### Run PANNZER2 with **Apptainer**
```bash
apptainer exec -B $PWD:/data pannzer2.sif \
  runsanspanz.py -R -m Pannzer -s "Homo sapiens" \
  -i /data/input.faa -o /data/results.csv
```

-B $PWD:/data binds the current working directory to /data inside the container.
Input FASTA (input.faa) and output (results.csv) must be in the bound directory.
As long as -R is provided, no local databases are required.

## Image tags

>The GitHub Actions workflow publishes multiple tags:

- **latest** → always points to the most recent build from the main branch.
- **sha-<commit>** → build tied to a specific commit (short SHA).
- **vX.Y.Z** → build from a Git tag (semantic versioning).

    Example:

    ghcr.io/<your-username>/pannzer2:latest
    ghcr.io/<your-username>/pannzer2:v0.1.0
    ghcr.io/<your-username>/pannzer2:sha-a1b2c3d

## Development notes

The `Dockerfile` is located in the `pannzer2/` directory.
`.dockerignore` excludes unnecessary files (e.g., raw FASTA/FASTQ, results, `.git`).

The build workflow (`.github/workflows/build.yml`) uses GitHub Actions to:

- Build the image with **docker/build-push-action**
- Push it to GHCR automatically  
- Generate `latest`, SHA-based, and version tags  

## Important notes

- With -R, sequences are sent to the **public PANNZER2 server**. 
    - For sensitive data, consider setting up local databases (not included in this image).

- Remote mode may have **usage limits** and depends on internet access from your compute node.

- This image is optimized for **functional annotation testing**; 
    - For heavy production pipelines, you may need **local DBs**.

---

# Contributing

- Pull requests and issues are welcome!

- To extend this image (e.g., with local databases or additional bioinformatics tools), create a new `Dockerfile` based on this one.

- Please pin package versions (e.g., `numpy==...`) in `requirements.txt` if you require **strict reproducibility**.

# References

Koskinen, P., Törönen, P., Nokso-Koivisto, J., Holm, L. (2015). PANNZER: High-throughput functional annotation of uncharacterized proteins in an error-prone environment. Protein Science.
Apptainer documentation
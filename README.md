````markdown
# Lab Cluster Usage Guide

Welcome to the lab cluster! This guide covers everything you need to know to get started with Slurm, interactive allocations, batch submissions, and resource requests (CPU, GPU). Use this as your quick reference for day-to-day work on our HPC system.

---

## Table of Contents

1. [Accessing the Cluster](#accessing-the-cluster)
2. [Environment Modules & Conda](#environment-modules--conda)
3. [Interactive Sessions](#interactive-sessions)
   - [salloc](#salloc)
   - [srun](#srun)
4. [Batch Submissions](#batch-submissions)
   - [sbatch](#sbatch)
   - [Job Script Template](#job-script-template)
5. [Resource Requests](#resource-requests)
   - [CPU](#cpu)
   - [GPU](#gpu)
   - [Memory](#memory)
   - [Time](#time)
6. [Managing Jobs](#managing-jobs)
7. [Logs and Output Files](#logs-and-output-files)
8. [Useful Slurm Commands](#useful-slurm-commands)
9. [Environment Variables](#environment-variables)
10. [Example Workflows](#example-workflows)
11. [Troubleshooting](#troubleshooting)
12. [Best Practices](#best-practices)

---

## Accessing the Cluster

1. **SSH into the login node**:
   ```sh
   ssh your_username@lab.cluster.domain.edu
````

2. **Change to your project directory**:

   ```sh
   cd ~/projects/my_project
   ```

---

## Environment Modules & Conda

### Modules

Use the module system to load system-managed software:

```sh
module avail              # list available modules
module load python/3.12    # load Python 3.12
module list               # show loaded modules
```

Unload when done:

```sh
module unload python/3.12
```

### Conda Environments

For custom Python setups, use Conda:

```sh
module load anaconda3            # load Anaconda base
conda create --name myenv python=3.12  # create env with Python 3.12
conda activate myenv             # activate environment
conda install numpy scipy        # install packages
```

Freeze dependencies:

```sh
conda env export > environment.yml
```

---

## Interactive Sessions

### salloc

Request an interactive allocation:

```sh
salloc --nodes=1 --ntasks=1 \
       --cpus-per-task=4 --mem=16G \
       --gres=gpu:1 --time=02:00:00
```

Once allocated, move to a shell:

```sh
bash
```

### srun

Spawn an interactive shell directly:

```sh
srun --pty --nodes=1 --cpus-per-task=4 \
     --mem=16G --gres=gpu:1 --time=02:00:00 bash
```

---

## Batch Submissions

### sbatch

Submit a job script:

```sh
sbatch job_script.sh
```

### Job Script Template

```bash
#!/bin/bash
#SBATCH --job-name=my_job
#SBATCH --output=slurm-%j.out
#SBATCH --error=slurm-%j.err
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=4
#SBATCH --mem=16G
#SBATCH --gres=gpu:1            # request GPU
#SBATCH --partition=gpu
#SBATCH --time=04:00:00

# Load modules or conda
module load python/3.12
# or:
# module load anaconda3
# source activate myenv

# Restrict visible GPUs
export CUDA_VISIBLE_DEVICES=0

srun python script.py --arg1 val1
```

Submit:

```sh
sbatch job_script.sh
```

---

## Resource Requests

### CPU

```sh
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=8
```

### GPU

```sh
#SBATCH --partition=gpu
#SBATCH --gres=gpu:2
```

### Memory

```sh
#SBATCH --mem=64G
```

### Time

```sh
#SBATCH --time=12:00:00
```

---

## Managing Jobs

* **View jobs**:

  ```sh
  squeue -u $USER
  ```
* **Cancel job**:

  ```sh
  scancel <jobid>
  ```
* **Update pending job**:

  ```sh
  scontrol update JobId=<jobid> TimeLimit=10:00:00
  ```

---

## Logs and Output Files

Defaults:

* **STDOUT**: `slurm-<jobid>.out`
* **STDERR**: `slurm-<jobid>.err`

Custom paths:

```sh
#SBATCH --output=logs/%x-%j.out
#SBATCH --error=logs/%x-%j.err
```

---

## Useful Slurm Commands

* **Job accounting**:

  ```sh
  sacct -j <jobid>
  ```
* **Efficiency report**:

  ```sh
  seff <jobid>
  ```
* **Live stats**:

  ```sh
  sstat -j <jobid>.batch
  ```

---

## Environment Variables

Set variables in scripts or interactively:

```sh
# Select GPU devices
export CUDA_VISIBLE_DEVICES=0,1

# Set thread count
export OMP_NUM_THREADS=4
```

---

## Example Workflows

### GPU Jupyter Notebook (Interactive)

```sh
salloc --nodes=1 --cpus-per-task=4 --mem=32G \
       --gres=gpu:1 --time=2:00:00

module load anaconda3
conda activate myenv
jupyter notebook --ip=0.0.0.0 --port=8888 --no-browser
```

Port-forward locally:

```sh
ssh -N -L 8888:localhost:8888 your_username@lab.cluster.domain.edu
```

### Large Data Preprocessing (Batch)

```bash
#!/bin/bash
#SBATCH --job-name=preprocess
#SBATCH --output=logs/preprocess-%j.out
#SBATCH --cpus-per-task=12
#SBATCH --mem=128G
#SBATCH --time=06:00:00

module load python/3.12
export OMP_NUM_THREADS=12
srun python preprocess.py --input bigdata.csv
```

Submit:

```sh
sbatch preprocess.sh
```

---

## Troubleshooting

* **Pending too long?**

  * Inspect: `scontrol show job <jobid>`
  * Reduce resource requests

* **Module errors?**

  * Check `module avail`
  * Confirm installed versions

* **CUDA\_VISIBLE\_DEVICES not working?**

  * Verify GPUs: `nvidia-smi`
  * Confirm environment variable set before job launch

---

## Best Practices

* Use **Git** for scripts and versioning.
* Test interactively on small allocations.
* Organize outputs: `logs/`, `results/`.
* Document module versions and parameters.
* Use **Conda** or `venv` for isolated environments.
* Clean up old files and monitor quota.

---

*For full documentation, consult the official HPC guide: [https://www.uh.edu/research/rcdc/support-and-services/user-guide/](https://www.uh.edu/research/rcdc/support-and-services/user-guide/)*


````markdown
# Lab Cluster Usage Guide

Welcome to the lab cluster! This guide will walk you through everything you need to know to get started with Slurm, interactive allocations, batch submissions, and resource requests (CPU, GPU). Use this as your quick reference for day-to-day work on our HPC system.

---

## Table of Contents

1. [Accessing the Cluster](#accessing-the-cluster)
2. [Environment Modules](#environment-modules)
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
7. [Example Workflows](#example-workflows)
8. [Best Practices](#best-practices)

---

## Accessing the Cluster

1. **SSH into the login node**:
   ```sh
   ssh your_username@lab.cluster.domain.edu
````

2. **Change directory to your home/project**:

   ```sh
   cd ~/projects/my_project
   ```

## Environment Modules

Use the module system to load software:

```sh
module avail           # list all available modules
module load python/3.9 # load Python 3.9
module list            # show loaded modules
```

When finished with a module:

```sh
module unload python/3.9
```

---

## Interactive Sessions

### salloc

Request an interactive allocation directly:

```sh
salloc --nodes=1 --ntasks=1 --cpus-per-task=4 --mem=16G --time=02:00:00
```

Once granted, you will be placed on a compute node. Then launch your shell or job:

```sh
bash  # or any shell of your choice
```

### srun

Alternatively, use `srun` from the login node:

```sh
srun --pty --nodes=1 --cpus-per-task=4 --mem=16G --time=02:00:00 bash
```

This directly spawns an interactive shell on a compute node.

---

## Batch Submissions

All long-running tasks should be submitted via `sbatch`.

### sbatch

```sh
sbatch job_script.sh
```

Check job output in the working directory (default files: `slurm-<jobid>.out`).

### Job Script Template

```bash
#!/bin/bash
#SBATCH --job-name=my_job         # job name
#SBATCH --output=slurm-%j.out     # standard output (%j expands to jobID)
#SBATCH --error=slurm-%j.err      # standard error
#SBATCH --nodes=1                 # number of nodes
#SBATCH --ntasks=1                # number of tasks (MPI ranks)
#SBATCH --cpus-per-task=4         # CPU cores per task
#SBATCH --mem=16G                 # memory per node
#SBATCH --time=04:00:00           # time (HH:MM:SS)

module load python/3.9

srun python script.py --arg1 val1
```

Save as `job_script.sh` and submit with `sbatch job_script.sh`.

---

## Resource Requests

### CPU

* `--cpus-per-task=<n>`: cores per task
* `--ntasks=<n>`: number of MPI tasks

### GPU

Request GPUs (if available) by adding:

```bash
#SBATCH --gres=gpu:1           # request 1 GPU
#SBATCH --partition=gpu        # optional: specify GPU partition
```

Or interactively:

```sh
salloc --nodes=1 --ntasks=1 --gres=gpu:1 --time=01:00:00
```

### Memory

* `--mem=<size>`: memory per node (e.g., `--mem=32G`)

### Time

* `--time=HH:MM:SS`: walltime limit

---

## Managing Jobs

* **View queue**:

  ```sh
  squeue -u your_username
  ```
* **Cancel job**:

  ```sh
  scancel <jobid>
  ```
* **Check job details**:

  ```sh
  scontrol show job <jobid>
  ```

---

## Example Workflows

### Quick Python Test (Interactive)

```sh
salloc --nodes=1 --cpus-per-task=2 --mem=4G --time=00:30:00
module load python/3.9
python -c "import numpy; print(numpy.__version__)"
exit
```

### GPU Training (Batch)

```bash
#!/bin/bash
#SBATCH --job-name=gpu_train
#SBATCH --partition=gpu
#SBATCH --gres=gpu:2
#SBATCH --cpus-per-task=8
#SBATCH --mem=64G
#SBATCH --time=08:00:00

module load cuda/11.7
module load python/3.9

srun python train_model.py --epochs 50 --batch-size 64
```

Save as `train_gpu.sh`, then:

```sh
sbatch train_gpu.sh
```

---

## Best Practices

* **Small Tests**: Always test scripts interactively or with small resources before large runs.
* **Clean Up**: Remove old output and error files to avoid clutter.
* **Documentation**: Comment your job scripts for clarity and reproducibility.
* **Version Control**: Keep your code and scripts in Git for tracking changes.

---

*For questions or help, contact the system administrator or refer to the full user guide at [https://www.uh.edu/research/rcdc/support-and-services/user-guide/](https://www.uh.edu/research/rcdc/support-and-services/user-guide/)*

```
```


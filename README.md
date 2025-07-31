# 💻 Lab Cluster Usage Guide

Welcome to the Lab Cluster! This guide helps you get started with accessing the cluster, submitting jobs, using SLURM, GPUs, Python environments, and more.

---

## 📚 Table of Contents

* [🔑 Accessing the Cluster](#-accessing-the-cluster)
* [📁 File Transfers & Permissions](#-file-transfers--permissions)
* [⚙️ SLURM Basics](#️-slurm-basics)
* [🚀 Running Jobs with sbatch](#-running-jobs-with-sbatch)
* [🖥️ Interactive Jobs with salloc](#️-interactive-jobs-with-salloc)
* [🎮 GPU & CPU Requests](#-gpu--cpu-requests)
* [🐍 Python Environment Setup (conda)](#-python-environment-setup-conda)
* [🧠 Setting CUDA\_VISIBLE\_DEVICES](#-setting-cuda_visible_devices)

---

## 🔑 Accessing the Cluster

1. **SSH into the login node**:

   ```bash
   ssh your_username@lab.cluster.domain.edu
   ```

---

## 📁 File Transfers & Permissions

### Copy Files to/from the Cluster

* **Copy files to cluster**:

  ```bash
  scp your_file.py your_username@lab.cluster.domain.edu:~/target_folder/
  ```
* **Copy files from cluster**:

  ```bash
  scp your_username@lab.cluster.domain.edu:~/target_folder/output.log ./
  ```

### File Permissions

* **Give read permission to your group**:

  ```bash
  chmod g+r your_file.py
  ```

---

## ⚙️ SLURM Basics

* Check node availability:

  ```bash
  sinfo
  ```
* Check your jobs:

  ```bash
  squeue -u your_username
  ```
* Cancel a job:

  ```bash
  scancel <job_id>
  ```

---

## 🚀 Running Jobs with sbatch

Example SLURM script (`run_job.sh`):

```bash
#!/bin/bash
#SBATCH --job-name=my_job
#SBATCH --output=output.log
#SBATCH --error=error.log
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=4
#SBATCH --mem=16G
#SBATCH --time=02:00:00
#SBATCH --partition=gpu
#SBATCH --gres=gpu:1

module load anaconda3
conda activate myenv
python my_script.py
```

Submit the job:

```bash
sbatch run_job.sh
```

---

## 🖥️ Interactive Jobs with salloc

Run an interactive session on a GPU node:

```bash
salloc --partition=gpu --gres=gpu:1 --mem=16G --cpus-per-task=4 --time=01:00:00
```

---

## 🎮 GPU & CPU Requests

* Request specific GPUs:

  ```bash
  salloc --gres=gpu:A100:1
  ```
* Limit visible devices (e.g., in Python):

  ```bash
  export CUDA_VISIBLE_DEVICES=0
  ```

---

## 🐍 Python Environment Setup (conda)

Create a new Python 3.12 environment:

```bash
conda create -n myenv python=3.12
```

Activate the environment:

```bash
conda activate myenv
```

Install packages:

```bash
conda install numpy pandas matplotlib
```

---

## 🧠 Setting CUDA\_VISIBLE\_DEVICES

Use this environment variable to control GPU visibility:

```bash
export CUDA_VISIBLE_DEVICES=0,1
```

In Python:

```python
import os
os.environ["CUDA_VISIBLE_DEVICES"] = "0"
```

---

Happy coding! 💻✨

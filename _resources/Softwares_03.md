---
title: "Conda environment settings"
collection: resources
permalink: /conda_envs/
author_profile: true
category: softwares
order: 3
---

This is a tutorial to: 
1. download and install conda -- the environment and package management tool
2. build a conda environment for computational biophysicist

# Download and installation
It is recommended to use either `miniconda` or `miniforge` (mamba). The `mamba` has become more popular due to its quick speed at resolving dependencies.

access the two packages from the following links and choose one you like:
1. [miniconda](https://www.anaconda.com/docs/getting-started/miniconda/main)
2. [miniforge/mamba](https://github.com/conda-forge/miniforge)

Follow their instructions to install them. Personally I use `mamba`. All commands you run use `conda` can be run by simply replacing `conda` with `mamba`.

# Manage environment
After installation, the `mamba` or `conda` comes with an initial `base` environment. Of note, we almost never mess with the clean `base` environment. Instead, we create our own with:

```python
mamba create -n bio python=3.12

# OR 

conda create -n bio python=3.12
```

For the python version, you usually needs to decide on what version is compatibale with most packages you are going to install in the created environment. If you are not sure, try not using the latest and the oldest python versions. For example, the current latest python is 3.13 as the time of writing this note. I would go for 3.10, 3.11 or 3.12.

We can check and remote an environment with:

```python
# check all envs you have created
mamba env list

# remove environment
mamba deactivate # if you are currently inside this environment
mamba remove -n [env_name] --all
```

# Configure a environment for computational biophysicists

```python
mamba activate bio  # always activate the environment before installing any packages
mamba install -c conda-forge numpy pandas matplotlib scipy
pip install jupyterlab
pip install seaborn biopython mdtraj mdanalysis

# Optional: openmm
mamba install -c conda-forge openmm
# if you have nvidia GPU cards
mamba install -c conda-forge openmm cuda-version=12.0
# of note, the cuda-version should match with your system nvidim-smi cuda version
# you can check with the command: nvidim-smi

# Optional: psfgen
mamba install -c conda-forge psfgen
```

The `biopython`, `mdtraj` and `mdanalysis` are packages quiet often used for PDB and MD trajectory analysis. `seaborn` and `matplotlib` are popular packages for scientific plot. 

You can further configure the environment according to your own needs.

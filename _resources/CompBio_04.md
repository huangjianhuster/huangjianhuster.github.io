---
title: "Intrinsically disordered protein trajectory analysis"
collection: resources
category: computational biophysics
order: 4
---

This is a short tutorial of analyzing IDP (intrinsically disordered protein) trajectories. Prerequisites are the psf file (structural file) of your IDP system and the corresponding xtc (trajectory file).

# Dependencies
Assuming you have already created a basic conda environment with numpy, pandas, matplotlib, we need to install the following packages if they have not been installed:
```python
pip install MDAnalysis
pip install mdtraj
```

Install Jian's packages: 
1. clone the github repository:
```text
git clone git@github.com:huangjianhuster/EnsembleAnalysis.git
```
2. installation:
``` bash
cd EnsembleAnalysis
pip install .
```


# $R_{g}$ and end-to-end distance
$R_{g}$ and end-to-end distance of a IDP conformation characterize its compactness or global dimension in the 3D space. Check the following link for a simple example using *MDAnalysis* for those calculations:
```text
https://docs.mdanalysis.org/1.0.1/documentation_pages/overview.html

# also check the following link for definition of Rg
https://www.youtube.com/watch?v=U15FVwQ8ynQ

# the end-to-end distance is simply a measurement of the distance between the N-termus and the C-termus of an IDP chain
```

We can also use the above-installed *EnsembleAnalysis* package (which internally also uses MDanalysis functions for $R_{g}$ and end-to-end distance calculations):

```python
from EnsembleAnalysis.core.ensemble import *

psf = "/path/to/your/idp/psf/file"
xtc = "/path/to/your/idp/xtc/file"

en = IdpEnsemble(psf, xtc)
# get R_g
rg = en.get_rg()
# get end-to-end distances
e2e = en.get_end2end()
```

The `rg` matrix have a shape of `(4, frames)`, where the rows are radius of gyration for the whole, projection on x-axis, on y-axis and on z-axis respectively. Usually the first row is the $R_{g}$ you want to report and plot.

The `e2e` will be an array with length of `frames`. Each value corresponds to the end2end value for the frame.

> [!NOTE] Check the source code
> You are welcome to look into the source code of `EnsembleAnalysis`, check location `EnsembleAnalysis/core/ensemble.py`


# Secondary structure analysis
As we are sampling IDP conformations using MD simulations, transient secondary structures, such as $\alpha$-helix, $\beta$-sheet, will be sampled in each frames. This analysis allows us to see the percentages of those secondary structures for each residue location.

```python
ss = en.get_ss()
```

the `ss` will be a dictionary which has two keys `helix` and `sheet`, and their values are the average percentages of the secondary structures for all residue indices of the IDP chain.

# Contact frequencies
If you want to calculate contact frequencies between two atom group selections, for example `resid 1:10` and `resid 60:70`,

```python
selection1 = "protein and resid 1:10 and name CA"
selection2 = "protein and resid 60:70 and name CA"
contact_frq = en.contact_freqs(ag1_sele=selection1, ag2_sele=selection2, cutoff=6)
```

The cutoff is a distance cutoff to count as a "contact" between two atoms.

The `contact_frq` will be a dictionary:
```python
{'distances': distances_all, # all pairwise distances of all frames
'contact_freqs': contact_freqs, # contact frequencies of all pairs
'contact_pairs': contact_pairs} # contact pair with frequencies in a descending
```

# Solvent accessible surface area (SASA)
SASA characterizes the solvent exposed area of a selected atom group. It's better to use `mdtraj` package for this calculation. Check the following link for details:

```text
https://mdtraj.org/1.9.4/examples/solvent-accessible-surface-area.html
```

An example code:
```python
import mdtraj as md

traj = md.load(psf, xtc)
sasa = md.shrake_rupley(traj)
```

Another way is to use `biopython`, which usually is used for only one PDB (conformation) instead of a trajectory. Thus, less recommended.

```text
https://biopython.org/docs/dev/api/Bio.PDB.SASA.html
```

**Caveat**: those SASA calculation algorithm usually by default uses all-atom definition for vdw radii definition of elements, such as C, H, O, N etc. When calculating SASA for a coarse-grained model, you may need to manually supplement the radii definition for coarse-grained beads.


# Plot your data
It is suggested to use either `matplotlib` or `seaborn` to plot your data. There are many resources online you can find that use those two packages for scientific plot:

```text
https://matplotlib.org/stable/index.html

https://seaborn.pydata.org/
```

You are welcome to use chatgpt or any other ai tools to help your write your plot code.

# To be continued...

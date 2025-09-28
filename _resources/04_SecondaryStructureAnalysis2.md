---
title: "Secondary structure analysis using DSSP"
excerpt: "use the STRIDE algorithm to assign helix, sheet and coil to protein structures or trajectories"
collection: resources
category: computational biophysics
order: 3
---

DSSP (Dictionary of Secondary Structure of Proteins) is one of the **most widely used algorithms** for assigning secondary structures to protein residues based on a 3D structure (PDB coordinates)

The **DSSP** algorithm was originally described in the following paper:

> Kabsch W, Sander C (1983). “Dictionary of protein secondary structure: pattern recognition of hydrogen-bonded and geometrical features”. Biopolymers 22 (12): 2577-637. doi:10.1002/bip.360221211

Please also check another algorithm **STRIDE** for secondary structure analysis: [STRIDE for secondary structure](https://huang-jian.com/resources/03_SecondaryStructureAnalysis1/).

# 1. For starts
- The algorithm takes **atomic coordinates** of a protein (N, Cα, C, O atoms) from a PDB file or trajectory frame.
- DSSP primarily focuses on **backbone atoms** to determine the local geometry.

## 2. Hydrogen bond detection

Secondary structure is largely determined by **hydrogen bonding patterns** in the backbone:

- DSSP uses an **electrostatic model** to estimate H-bond energies.
- The energy between a donor N-H and acceptor C=O is calculated using:

$$
E = 0.084\left( \frac{1}{r_{ON}} + \frac{1}{r_{CH}} - \frac{1}{r_{OH}} - \frac{1}{r_{CN}} \right) \cdot 332 \text{ kcal/mol}
$$

where $r_{XY}$ are distances between atoms.

- If the H-bond energy is below a threshold (≈ -0.5 kcal/mol), a hydrogen bond is considered **present**.

## 3. Helix and sheet assignment

DSSP assigns secondary structures based on **hydrogen bonding patterns and backbone geometry**:

| Structure                 | Definition |
| ------------------------- | --------------------------------- |
| **H** (α-helix)           | Residue i forms H-bonds with i+4 (C=O…H-N)       |
| **G** (3-10 helix)        | Residue i forms H-bonds with i+3     |
| **I** (π-helix)           | Residue i forms H-bonds with i+5       |
| **E** (extended β-strand) | Part of β-sheet; participates in inter-strand H-bonds   |
| **B** (isolated β-bridge) | Single H-bonded residue not in extended sheet      |
| **T** (turn)              | Residue forms H-bond with i+3 (like 3-10) but not regular helix |
| **S** (bend)              | Geometric bend without H-bonds  |
| **C / ‘-’**               | Coil / other / unassigned    |

- DSSP looks for **patterns of hydrogen bonds along the backbone**.
- For example, a stretch of 4 residues with i→i+4 H-bonds is assigned as α-helix (H).
- β-strands are assigned if residues participate in **inter-strand hydrogen bonds**, forming a β-sheet.


# 4. In practice

DSSP has been implemented into the `MDAnalysis` package:

> https://docs.mdanalysis.org/dev/documentation_pages/analysis/dssp.html


> [!NOTE] Caveat
> Since DSSP considers geometry of the backbone of residues for assigning secondary structures, for example, `i`-`i+4` for hydrogen bond detection, we need to select upstream and downstream at least +5 residues for calculating secondary structures.

**For example:** 

if we want to calculate the secondary structures of the segment of residue index 10 to 20, we need to at least select residue index 5 to 25.

With the above being said, the starting and the ending residues of your selected segment will almost always be assigned as "C" (coil).

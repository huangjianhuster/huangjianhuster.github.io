---
title: "TMEM16F: Activation Mechanism and Dual Ion/Lipid Permeation Pathways"
excerpt: "How does TMEM16F open its pore to conduct both ions and lipids? <br/><a href='/research/TMEM16F/' class='image'><img src='/images/research_images/TMEM16F.png' width=300></a><br/>"
collection: research
category: Ion channels
date: 2022-01-01
order: 1
---

**TMEM16F** is a Ca<sup>2+</sup>-activated phospholipid scramblase with a remarkable dual identity: it translocates phospholipids across the plasma membrane bilayer (scramblase activity) while also functioning as a non-selective ion channel. This dual functionality underlies critical physiological processes including blood coagulation, bone development, and viral membrane fusion. Loss-of-function mutations cause Scott syndrome, a bleeding disorder. Despite years of structural studies that have revealed the closed and intermediate conformations of mammalian TMEM16F, an open, conductive state had never been captured — leaving the activation mechanism and the molecular basis of its permeability largely unknown.

*[Jia\*, Huang\*, & Chen, Biophys. J. 121, 3445–3457 (2022). [DOI](https://doi.org/10.1016/j.bpj.2022.08.011)]*

---

## The Inner Activation Gate

<img src='/images/research_images/TMEM16F.png' width=550>

*Structure of Ca²⁺-bound mTMEM16F (PDB: 6QP6). The pore-forming helices TM4 (red), TM5 (blue), and TM6 (green) are highlighted. Inner gate residues **F518**, **Y563**, and **I612** form a hydrophobic constriction that seals the pore in the closed state. Proposed lipid and ion conduction pathways are indicated by arrows.*

In the closed Ca²⁺-bound state, the membrane-facing pore of mTMEM16F is sealed by a hydrophobic constriction formed by **F518** (TM4), **Y563** (TM5), and **I612** (TM6). The narrowest point near the middle of the membrane-facing pore is too constricted for either ions or lipids — the pore radius is well below the ~2 Å minimum needed for hydrated ion passage. A [previous study](https://www.nature.com/articles/s41467-019-09778-7) showed that charged mutations of these inner gate residues (e.g., F518K, Y563K) produce constitutively active scramblases, even in the absence of Ca²⁺. These gain-of-function mutations provided a key opportunity to study the open state via simulation.

---

## Spontaneous Pore Opening by Inner Gate Charged Mutations

<img src='/images/research_images/TMEM16F_pore_opening.png' width=700>

**(Top)** Distance between Cα atoms of inner gate residues F/K518 and I612 as a function of simulation time for WT mTMEM16F, F518K, Y563K, and F518K/Y563K. The red arrow marks a spontaneous opening event in a Y563K simulation. **(Bottom left/center)** Representative closed (WT, sim 1, 1 μs) and open (F518K/Y563K, sim 10, 4 μs) conformations, with green mesh showing the pore profile (HOLE). **(Bottom right)** Average pore radius profiles: the open mutant (black) reaches ~6 Å — sufficient for fully hydrated ions — compared to <2 Å in WT (blue).*

We performed six sets of multi-microsecond atomistic MD simulations totaling hundreds of microseconds across WT, F518K, Y563K, and F518K/Y563K mutants. Key findings:

- **Single charged mutations are sufficient** to trigger spontaneous pore opening: F518K or Y563K each promoted pore dilation in 6 out of 12 independent pores, increasing the 518–612 inner gate distance from ~5 Å to over 10 Å. No spontaneous opening was observed in any of the three parallel WT simulations.
- **The double mutant F518K/Y563K** drives rapid and stable opening: the 518–612 distance jumped from ~5 Å to ~14–15 Å within 0.5 μs and remained there throughout all three independent simulations.
- **The open pore is well-hydrated**: water molecules within 6 Å of pore-centering residue S566 increased from ~5 (closed) to ~20 (open), confirming a fully hydrated, ion-conductive lumen.
- **The open state of mTMEM16F resembles fungal nhTMEM16** (backbone RMSD of TM4–6 < 4 Å), suggesting a conserved activation mechanism across TMEM16 family scramblases.
- **Free-energy analysis** (umbrella sampling) shows the open state is ~1.5 kcal/mol less stable than the closed state in WT, giving an estimated open-state population of ~8% at room temperature — consistent with why cryo-EM has failed to capture it. The F518K/Y563K double mutant flips this balance: the open state becomes ~5 kcal/mol *more* stable, explaining constitutive activity.

---

## Pathway and Mechanism of Lipid Translocation

Dilation of the pore exposes hydrophilic patches in the upper pore region (residues V453, R478, S510, S514, K518, I521, S566, L603, E604, T607, Q608, I611), whose lipid headgroup contact probabilities increase more than 3-fold compared to WT. This hydrophilic environment lowers the free-energy barrier for phospholipid headgroup entry.

A spontaneous, full translocation of a POPE phospholipid molecule from the inner to the outer leaflet was directly observed during a F518K/Y563K simulation — the headgroup spending >2 μs threading through the pore before rapidly diffusing to the extracellular side. This is fully consistent with the **credit card model**: the polar headgroup is guided along the hydrophilic pore-lining residues while the hydrophobic acyl tails remain anchored in the bilayer.

---

## Ion and Lipid Pathways Overlap, Then Diverge

<img src='/images/research_images/TMEM16F_pathways_ion_lipid.png' width=700>

*Ion and phospholipid permeation pathways of mTMEM16F. Blue spheres show Cl⁻ positions from 19 independent meta-dynamics permeation events. Orange mesh shows phospholipid headgroup density from F518K/Y563K mutant simulations. (A) Side view: pathways overlap on the intracellular side up to the inner gate, then diverge above it. (B) Top view of ion pathway exit through the extracellular vestibule walled by TM4–6. (C) Key residues (S593, E594, E595, D597, K574) lining the extracellular ion exit route.*

Well-tempered meta-dynamics simulations of Cl⁻ permeation through the activated WT mTMEM16F generated 19 independent ion-crossing events. Overlaying the ion density with the phospholipid headgroup density from the mutant simulations reveals a striking pathway architecture:

- **From the intracellular side up to the inner gate**, ion and lipid pathways are **coincident** — both traverse the same hydrophilic channel through the dilated inner gate region.
- **Above the inner gate, the pathways diverge**: phospholipid headgroups exit laterally through the hydrophilic membrane-facing groove, following the credit card model; Cl⁻ ions instead exit through a separate **extracellular vestibule** walled by TM4–6 and lined by polar/charged residues (S593, E594, E595, D597, K574) that provide a well-hydrated exit route.

This architecture implies the two transport activities are mechanistically linked but not fully coupled: lipid headgroups in the groove may increase pore hydrophilicity and thereby facilitate ion conduction, yet ion and lipid fluxes are not required to be co-transported.

---

<iframe width="560" height="433" src="https://www.youtube.com/embed/kbpEf4WfJj8" title="mTMEM16F is an ion channel and also a lipid scramblase." frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

*MD simulation showing the F518K/Y563K double mutant undergoing spontaneous pore opening and conducting a lipid molecule through the dilated pore.*

---

## Significance

This work provides the first atomistic picture of an open, conductive state of mTMEM16F, resolving longstanding questions about how it is activated and how it conducts both ions and lipids. The key contributions are: (1) direct observation of spontaneous pore opening by inner gate charge mutations; (2) thermodynamic explanation for why the open state of WT mTMEM16F is rarely captured structurally; (3) identification of overlapping-then-diverging ion and lipid permeation pathways that rationalize the dual functionality; and (4) evidence that the activation mechanism is likely conserved across TMEM16 family scramblases. These insights provide a structural framework for understanding disease mutations and for designing pharmacological agents targeting TMEM16F in coagulation disorders, bone disease, and viral infection.

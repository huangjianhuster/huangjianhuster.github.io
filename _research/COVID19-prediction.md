---
title: "Prediction of infectivity-enhancing mutations of the SARS-CoV-2 spike protein"
excerpt: "In silico affinity maturation identifies single point mutations on the SARS-CoV-2 RBD that can enhance binding to the hACE2 receptor, with 6 out of 9 predictions confirmed by SPR assay. <br/><a href='/research/COVID19-prediction/' class='image'><img src='/images/research_images/spike.png' width=500></a><br/>"
collection: research
category: Protein signaling and engineering
date: 2021-05-10
order: 2
---

The **receptor binding domain (RBD)** of the SARS-CoV-2 spike (S) protein mediates viral attachment to the host cell receptor hACE2 (human angiotensin-converting enzyme 2). Mutations on the RBD can alter binding affinity and potentially enhance infectivity or enable immune escape from vaccines and neutralizing antibodies. This work applies *in silico* affinity maturation to systematically predict which single point mutations at the RBD–hACE2 interface would strengthen binding, and validates the top candidates experimentally by surface plasmon resonance (SPR).

*[Xue, Wu, Guo, Wu, **Huang**, Lai, Liu, Li, Wang & Wang, RSC Advances (2021). [DOI](https://doi.org/10.1039/D1RA00426C)]*

---

## Background

<img src='/images/research_images/spike.png' width=700>

*Crystal structure of the SARS-CoV-2 S protein RBD (blue) bound to the hACE2 receptor (green), PDB ID: 6M0J. The interface residues subjected to in silico mutagenesis are highlighted.*

Cell entry is the first step of SARS-CoV-2 infection. The homo-trimeric spike glycoprotein on the viral envelope drives host cell adhesion through the interaction of its RBD with hACE2. The wild-type (WT) RBD binds hACE2 with a dissociation constant (K<sub>D</sub>) in the nanomolar range. Most naturally occurring RBD missense mutations reduce infectivity, but certain variants show increased resistance to neutralizing antibodies or enhanced receptor binding. Identifying such mutations in advance is critical for the design of broad-spectrum vaccines and antibodies that remain effective against future variants.

---

## Computational Method: Rosetta Flex ddG

The Rosetta **Flex ddG** protocol estimates the change in protein–protein binding free energy (ΔΔG) upon mutation. Unlike single-structure protocols, it generates an ensemble of 48 models per mutant to account for conformational plasticity near the mutation site.

**Protocol steps:**

1. **Structure preparation** — The RBD–hACE2 co-crystal structure (PDB: 6M0J) was relaxed using the Rosetta FastRelax mover to remove clashes while preserving the experimental geometry.
2. **Backrub sampling** — For each mutant, backbone perturbations (backrub moves) are applied around the mutation site to generate a structural ensemble capturing local flexibility.
3. **Repacking and global relaxation** — Both the wild-type and mutant structures are repacked and minimized using the same protocol, ensuring a fair comparison.
4. **Binding energy calculation** — The interface binding energy (dG\_cross) is computed with the Rosetta InterfaceAnalyzerMover for each of the 48 models. The ΔΔG score is taken as the average difference in dG\_cross between mutant and WT ensembles.

**Mutation coverage:** All 25 RBD residues within 8 Å of the RBD–hACE2 interface were subjected to saturation mutagenesis. Each residue was mutated to all 19 alternative natural amino acids, yielding **475 single point mutants** in total. Mutants were ranked by average ΔΔG score; those with large negative values (predicted to strengthen binding) were prioritized for experimental follow-up.

---

## Key Results

### Binding affinity predictions and SPR validation

Of the 475 mutants screened, **114 had negative predicted ΔΔG scores**, indicating potential affinity enhancement. After manual structural inspection of the top-ranked candidates, 9 mutants were selected for SPR affinity measurement.

**6 out of 9 predicted mutants were experimentally confirmed to have improved binding affinity** compared to the WT (K<sub>D</sub> = 21.08 ± 3.01 nM):

| Mutant | Predicted ΔΔG (REU) | SPR K<sub>D</sub> (nM) | Experimental outcome |
|--------|--------------------:|----------------------:|----------------------|
| WT     | 0.00                | 21.08 ± 3.01          | —                    |
| Q498W  | −3.66 ± 1.80        | 7.10                  | Stabilizing ✓        |
| Q498R  | −2.04 ± 1.34        | 11.60                 | Stabilizing ✓        |
| T500W  | −1.90 ± 0.56        | 21.80                 | False positive ✗     |
| S477H  | −1.39 ± 1.16        | 13.90                 | Stabilizing ✓        |
| Y505W  | −1.23 ± 0.41        | 16.10                 | Stabilizing ✓        |
| T500R  | −1.21 ± 1.38        | 12.20                 | Stabilizing ✓        |
| N501V  | −1.02 ± 1.09        | 158.50                | False positive ✗     |
| Y489W  | −1.01 ± 0.50        | 38.90                 | False positive ✗     |
| Q493M  | −0.82 ± 1.39        | 6.90                  | Stabilizing ✓        |

The best binder is **Q493M**, with a K<sub>D</sub> of 6.90 nM — a **3-fold affinity improvement** over WT. The overall Pearson correlation coefficient between predicted ΔΔG and SPR-measured K<sub>D</sub> is **r = 0.41**, consistent with benchmarks reported for the Flex ddG method.

Notably, 4 of the 5 affinity-enhancing mutation sites (Q493, Q498, T500, Y505) are key hACE2-contacting residues, and 3 of them (S477, Q493, Q498) are positions where SARS-CoV-2 already differs from SARS-CoV-1 — suggesting the natural SARS-CoV-2 sequence is not yet optimized for maximal receptor binding.

---

### Structural mechanisms of affinity enhancement

Analysis of the Rosetta InterfaceAnalyzerMover outputs — specifically the change in interface hydrogen bond count (Δhbonds\_int) and buried solvent-accessible area (ΔSASA) — reveals two dominant mechanisms:

**Newly formed hydrogen bonds:**
- **Q498W/R**: The WT Q498 hydrogen-bonds with hACE2 N42. The W498 mutant redirects to form a hydrogen bond with hACE2 D38; the R498 mutant can bridge both N42 and D38, contributing on average 1.65 additional interface hydrogen bonds.
- **S477H**: Forms a new hydrogen bond with the terminal NH<sub>2</sub> of hACE2 S19 (average +0.77 hydrogen bonds).
- **T500R**: The WT T500 hydrogen-bonds with hACE2 Y41. T500R establishes new bonds with hACE2 E329 and N330 (average +0.71 hydrogen bonds).

**Enhanced hydrophobic contacts:**
- **Y505W**: The WT Y505 forms no hydrogen bonds at the interface; the bulkier tryptophan ring increases hydrophobic burial (larger ΔSASA\_hphobic), with essentially no change in hydrogen bond count.
- **Q493M**: The only confirmed stabilizing mutant with a *decrease* in hydrogen bond count (−0.92). The loss of the Q493–hACE2 E35 hydrogen bond is more than compensated by a dramatic increase in hydrophobic burial (ΔSASA\_hphobic = +71.23 Å<sup>2</sup>), illustrating that hydrophobic interactions alone can drive significant affinity gains.

---

### False positive analysis

Three predicted stabilizing mutants (T500W, N501V, Y489W) were experimentally neutral or destabilizing. The root cause in each case is an existing WT hydrogen bond that is disrupted during backrub sampling in the Flex ddG protocol:

- **T500W**: The backrub step mostly preserves the T500–Y41 hydrogen bond in WT models (only −0.08 change in bond count), masking the fact that the tryptophan substitution actually destroys this contact. The method over-credits the hydrophobic gain while missing the hydrogen bond loss.
- **N501V**: In the crystal structure, N501 adopts a rotamer where its NH<sub>2</sub> hydrogen-bonds with hACE2 Y41. However, backrub perturbations rotate the amide oxygen toward Y41 instead, increasing the donor–acceptor distance to 3.6 Å and effectively removing the bond from the ΔΔG calculation.
- **Y489W**: Similarly, the Y489–hACE2 Y83 hydrogen bond (3.5 Å in the crystal structure) is stretched beyond 3.7 Å in most backrub WT models, leading the protocol to underweight the penalty for losing this contact.

Visual inspection of WT model ensembles — checking whether key crystal-structure hydrogen bonds are preserved after backrub — can help identify and exclude likely false positives before committing to wet-lab validation.

---

### Agreement with deep mutational scanning

To further benchmark prediction quality, predicted ΔΔG scores were compared against a published deep mutational scanning (DMS) dataset covering all single-point RBD mutations (Starr *et al.*, *Cell* 2020). The Pearson correlation between Flex ddG predictions and DMS-measured log(K<sub>D,mut</sub>/K<sub>D,WT</sub>) is **r = 0.37**. Using the DMS data as ground truth, the Flex ddG method achieves a **precision of 0.18** and a **recall of 0.50** for identifying stabilizing mutations — indicating meaningful enrichment of true positives despite a moderate false positive rate.

As an independent validation, the Y453F mutation — found in SARS-CoV-2 strains circulating between humans and minks in Denmark — was predicted by Flex ddG to be stabilizing (−0.30 REU), consistent with its maintained transmissibility and the DMS result.

---

## Significance

This work demonstrates that Rosetta Flex ddG *in silico* affinity maturation can prospectively identify infectivity-enhancing mutations on the SARS-CoV-2 RBD. The computational cost is low — no molecular dynamics simulation is required — making the approach suitable for rapid deployment at the onset of a viral outbreak. The predicted mutations are directly relevant to vaccine and antibody design: they define epitope positions where future variants may escape immune surveillance, and they can be incorporated into multivalent vaccine constructs to broaden coverage.

Code for the Flex ddG workflow is available at [github.com/XtalPi/FlexddG](https://doi.org/10.1039/D1RA00426C).

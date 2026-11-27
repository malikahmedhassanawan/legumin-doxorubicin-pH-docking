# Legumin–doxorubicin pH docking

Docking and pKa survey of doxorubicin binding to a pea legumin hexamer at pH 7.4 and pH 5.0. This repository holds the inputs, Vina and AutoDock4 results, PROPKA output, summary tables and Colab notebook behind the preprint:

**No Docking-Level pH Response of Doxorubicin Binding to a Legumin Hexamer: A Docking and pKa Survey of Candidate Release Sites**

Malik Ahmed Hassan Awan, Department of Food Science and Technology, Bahauddin Zakariya University, Multan, Pakistan (ORCID [0009-0002-7395-6639](https://orcid.org/0009-0002-7395-6639)).

Preprint: ChemRxiv, November 2026 https://doi.org/10.26434/chemrxiv.15012892/v1
Large files: https://doi.org/10.5281/zenodo.24519789

## The question

A drug carrier should hold its cargo at blood pH (about 7.4) and release it in acidic compartments (about pH 5 to 6). Doxorubicin carries a sugar amine with a pKa near 8, so it is positively charged at both pH values. If a legumin hexamer were to release it through electrostatics alone, the change would have to come from acidic residues turning neutral, or histidines turning positive, next to the bound drug. This project tests that at the docking level.

## Result in short

No site shows a meaningful pH response once protonation is modelled with PROPKA pKa values. The largest shift in the AutoDock4 electrostatic term between pH 7.4 and pH 5.0 is +0.36 kcal/mol at pocket 1 (His196, chain A, pKa 5.55). Next comes +0.32 kcal/mol at hot1 (Asp296, chain C, pKa 5.02). Both are below kT at 310 K (0.62 kcal/mol). A receptor protonated wholesale with PDB2PQR/PROPKA at pH 5.0 (121 residues in protonated forms) gives +1.4 to +2.3 kcal/mol at every site, about seven times the pH-aware value at hot1, even at sites with no titratable residue nearby. That shift reflects the net charge of the receptor and not anything close to the drug.

The wild-type hexamer therefore shows no docking-level pH switch under this approximation. This does not rule out a switch that depends on the hexamer loosening, an engineered histidine, or a pH-cleavable linker.

## Shift in the electrostatic term, pH 7.4 to pH 5.0 (kcal/mol)

| Site | Titrated residue(s) near the amine (pKa) | pH-aware | Over-protonated |
|---|---|---|---|
| hot1 | Asp296 (C) 5.02 | +0.32 | +2.34 |
| hot2 | Glu291 (A) 5.97, Asp354 (F) 4.37 | +0.14 | +1.82 |
| hot4 | Asp354 (D) 5.02, Glu109 (E) | +0.12 | +1.77 |
| pocket 6 (central void) | Glu335 (B) 4.82 | +0.05 | +1.68 |
| centre box | Glu335 (A) 5.86 | +0.15 | +1.86 |
| siteA (blind-docking groove) | none within 10 Å | 0.00 | +1.42 |
| pocket 1 | His196 (A) 5.55 | +0.36 | +1.68 |
| pocket 7 | none within 10 Å | 0.00 | +1.76 |

Positive values mean weaker binding. Residue numbers follow UniProt P02857.

## What was done

1. **Model.** Hexamer of pea (*Pisum sativum*) legumin A, UniProt P02857, predicted with AlphaFold Server with the signal peptide (residues 1 to 21) removed. Six chains of 496 residues (UniProt 22 to 517), 2,976 residues in total. Mean pLDDT 77.7 over Cα atoms, ipTM 0.80, pTM 0.81. The model numbers residues 1 to 496; UniProt numbering is the model number plus 21, and the paper uses UniProt numbering throughout.
2. **Molecular dynamics.** GROMACS 2026.3, amber99sb-ildn, TIP3P, 310 K, rhombic dodecahedral box with 1.0 nm solute-to-box distance, 0.15 M NaCl plus neutralising Na⁺. Energy minimisation, then 100 ps NVT and 100 ps NPT with position restraints, then 30 ns of unrestrained production (V-rescale thermostat, C-rescale barostat, isotropic, τp 2.0 ps, 1 bar, 2 fs time step, PME, 1.0 nm cut-offs, frames every 10 ps). The run was resumed across Colab sessions in 1 ns segments.
3. **Frames.** Six frames from the 25 to 30 ns window for blind docking. The last frame (29.93 ns) was used for site-specific docking, rescoring and PROPKA.
4. **Docking.** AutoDock Vina 1.2.0 (9 modes, seed 1) with doxorubicin from PubChem CID 31703. Eight sites: a blind-docking groove (siteA), the protein centre, an internal void (pocket 6), two further fpocket pockets (1 and 7), and three interface sites (hot1, hot2, hot4) chosen from the over-protonated pH 5.0 receptor.
5. **Protonation models.** pH 7.4 and over-protonated pH 5.0 receptors from PDB2PQR 3.8.0 with PROPKA. A third, pH-aware pH 5.0 receptor edits the pH 7.4 receptor by hand at each site, titrating only acids and histidines within 10 Å of the drug amine whose PROPKA pKa lies near the target range.
6. **Rescoring.** The best pose at each site was rescored with AutoDock4 (AutoGrid 4.2.9, AutoDock 4.2.6), because Vina has no electrostatic term. Receptor charges are formal charges, the ligand carries +1.0 on its amine nitrogen, and only differences between receptors at the same pose are interpreted.
7. **pKa.** PROPKA 3.5.1 (bundled with PDB2PQR 3.8.0) on frame 29. 110 residues across the hexamer have predicted pKa values that could change charge near pH 5 to 6, mostly in acid-rich interface stretches, and values for the same residue differ strongly between chains.
8. **Controls.** One change at a time on the pH 7.4 receptor at hot1 and at pocket 1, to see which residues drive each shift.

## Repository layout

| Folder | Contents |
|---|---|
| `structures/` | Receptor structures, with the doxorubicin ligand in `structures/ligand/` |
| `md/` | Molecular dynamics inputs, with trajectory analysis in `md/analysis/` |
| `docking/` | Vina and AutoDock4 inputs, logs and poses, with rescoring in `docking/autodock4/` |
| `pka_pockets/` | PROPKA output and fpocket pocket detection (`pka_pockets/fpocket/`) |
| `results/` | Summary tables behind the paper (see below) |
| `figures/` | Figures used in the paper |
| `notebook/` | Colab notebook with the full workflow |

Also in the root: [`LICENSE`](LICENSE) and [`CITATION.cff`](CITATION.cff).

## Summary tables

All four files are in [`results/`](results/).

| File | Contents | Paper |
|---|---|---|
| [`autodock4_summary_pH_aware.csv`](results/autodock4_summary_pH_aware.csv) | Electrostatic and free-energy terms at all eight sites for the three receptors, with shifts | Tables 2 and 3, Figure 3 |
| [`autodock4_controls.csv`](results/autodock4_controls.csv) | One-change-at-a-time controls at hot1 and pocket 1 | Tables 4 and 5 |
| [`pKa_check_nearest_acids.csv`](results/pKa_check_nearest_acids.csv) | PROPKA pKa of the acids and histidines nearest each amine | Table 7 |
| [`distances_amine_to_charged_groups.csv`](results/distances_amine_to_charged_groups.csv) | Distance from the drug amine to nearby charged groups | Table 7, Figures 4 and 5 |

## Large files

Trajectories, full receptor sets and other large files are archived on Zenodo: https://doi.org/10.5281/zenodo.24519789 (record: https://zenodo.org/records/24519789).

## Limitations

- One receptor frame (29.93 ns) and one pose per site. Vina was repeated on a second frame at two sites only, and search-to-search variability was not measured.
- The 30 ns trajectory was not fully equilibrated. Backbone RMSD (after least-squares fit to the backbone, relative to the first production frame) rose from 0.33 nm in the first 5 ns to 0.43 nm in the last 5 ns, with the drift still slightly positive.
- Rigid-receptor docking cannot show the hexamer opening or reorganising.
- AutoDock4 energies use crude formal charges, and absolute values are not reliable.
- PROPKA was applied to one snapshot of the empty protein. Very high predicted pKa values (above 9) in crowded acidic clusters are probably overestimates.
- The pH-aware receptors were edited by hand and only within 10 Å of the amine, so the zero shifts at siteA and pocket 7 follow from the design.
- hot1, hot2 and hot4 were chosen from the over-protonated pH 5.0 receptor, so a larger shift there is partly built in. hot3 and hot5 were not docked.
- No replicates, no uncertainty estimates and no experimental validation. A pH 7.4 versus pH 5.0 MD comparison was set up but not run.

## Citation

Awan, M. A. H. No docking-level pH response of doxorubicin binding to a legumin hexamer: a docking and pKa survey of candidate release sites. ChemRxiv, 2026. https://doi.org/10.26434/chemrxiv.15012892/v1

Archive: Awan, M. A. H. Legumin–doxorubicin pH docking data. Zenodo. https://doi.org/10.5281/zenodo.24519789

GitHub also offers "Cite this repository" in the sidebar, which reads [`CITATION.cff`](CITATION.cff).

## Licence

Released under the [MIT License](LICENSE).

## Contact

Malik Ahmed Hassan Awan, [varietyhavenllc@gmail.com](mailto:varietyhavenllc@gmail.com)

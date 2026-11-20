# legumin-doxorubicin-ph-docking

Files behind my preprint on whether a legumin hexamer could carry doxorubicin at blood pH (7.4) and let it go at endosomal pH (5.0).

I predicted the hexamer with AlphaFold Server, ran 30 ns of molecular dynamics in GROMACS, docked doxorubicin to several sites with AutoDock Vina, and rescored four of those sites with AutoDock4 at both pH values. PROPKA gave the residue pKa values.

Preprint: [ChemRxiv link]
Trajectories and large files: [Zenodo DOI]

## What I found

No site showed a meaningful pH response once protonation was modelled realistically. The largest shift in the electrostatic term was +0.36 kcal/mol at one interface site (hot1), and about +0.32 of that came from half-neutralising Asp296 in chain C (predicted pKa 5.02). A receptor protonated with PDB2PQR/PROPKA at pH 5.0 gave a shift of +2.34 at the same site, roughly six times larger, which is why I treat that model as an over-protonated upper bound.

This is a negative result under a rough approximation. It does not show that the hexamer can't release the drug.

## Limits worth knowing before you reuse anything

- The 30 ns run had not fully equilibrated. Backbone RMSD was still drifting by about +0.003 nm/ns in the last 5 ns window.
- Rescoring and PROPKA use one frame (frame 29, at 29.93 ns) and one pose per site. The receptor is rigid.
- The AutoDock4 charges are simple formal charges. The totals of -19.3 (pH 7.4) and +84.7 (full pH 5.0) are sums of those AutoDock charges, not GROMACS topology charges.
- The "realistic" pH 5.0 receptor was edited by hand, and only in chain C. Asp296 predicts a higher pKa in chains E (6.79) and F (6.34), and I did not model those.
- hot1 was picked from the pH 5.0 receptor, so a larger shift there is partly built in.
- No replicates, no error bars, no experiments. The pH 7.4 versus pH 5.0 MD comparison was set up but not run.

## Folder layout

```
legumin-doxorubicin-ph-docking/
├── README.md
├── LICENSE
├── CITATION.cff
├── notebook/
│   └── docking_pka_analysis.ipynb
├── md/
│   ├── mdp/              em, nvt, npt and production .mdp files
│   ├── topology/         topol.top and .itp files
│   └── analysis/         rmsd30.xvg, gyrate30.xvg
├── structures/
│   ├── legumin_hexamer_22-517.pdb
│   ├── frame29_29.93ns.pdb
│   ├── receptors/        rec_29_pH74.pdbqt, rec_29_pH50.pdbqt
│   └── ligand/           dox.smi, dox.pdbqt
├── docking/
│   ├── vina/             dock_*.pdbqt and .log, one set per site
│   └── autodock4/        .gpf, .dpf and .dlg for hot1, siteA, centre and pocket 6
├── pka_pockets/
│   ├── propka/           p74.pka
│   └── fpocket/          frame 29 info files at pH 7.4 and 5.0
└── tables/               Tables 1 to 5 from the preprint as .csv
```

The trajectories (`md_0_30ns_full.xtc`, `last5ns.xtc`) and checkpoints are too big for GitHub. They are on Zenodo.

## Setup in short

Protein: UniProt P02857, signal peptide (residues 1 to 21) removed, AlphaFold Server model 0. The model covers residues 22 to 517 of each of six chains, and residue numbers throughout follow UniProt numbering.

MD: GROMACS 2026.3, amber99sb-ildn, TIP3P, 310 K, rhombic dodecahedral box with 1.0 nm between protein and box edge, 50,962 waters, 215 Na+ and 179 Cl-. Frames were saved every 10 ps. Frames from 2,470 to 2,510 ps were not saved because a Colab session was interrupted. That gap does not affect any analysis here.

Frames for docking: six frames from the 25 to 30 ns window (25.00, 26.02, 27.04, 28.06, 29.08 and 29.93 ns). Frame 29 is the last one, at 29.93 ns.

Software versions:

- GROMACS 2026.3
- AutoDock Vina 1.2.0
- AutoDock 4.2.6 and AutoGrid 4.2.9
- PDB2PQR 3.8.0 (with PROPKA 3.5.1)
- PROPKA 3 run standalone: [version, from `pip show propka`]
- fpocket and Open Babel: [versions]

## Order the steps were run

1. Predict the hexamer on AlphaFold Server and remove the signal peptide.
2. Build the system, minimise, equilibrate and run 30 ns in GROMACS.
3. Cut the 25 to 30 ns window and extract six frames.
4. Blind-dock doxorubicin on each frame (Vina, 135 Å box, exhaustiveness 16). Take siteA from where the top poses converge.
5. Build the pH 7.4 and pH 5.0 receptors for frame 29 with PDB2PQR, and run fpocket and PROPKA on frame 29.
6. Dock to the named sites on frame 29 (Vina, seed 1, 9 modes).
7. Set formal charges, then rescore the best pose at hot1, siteA, the centre box and pocket 6 with AutoDock4.
8. Run the one-change-at-a-time controls at hot1.

All coordinates, including the docking box centres, are in the simulation's own coordinate frame.

## Running the notebook

The notebook was written in Google Colab and its paths point to a Google Drive folder called `Legumin Hexamer MD`. To rerun it, change those paths to wherever you put the files. Vina scores are the best mode of one search with seed 1, so expect small differences if you change the seed.

## Licence

Code: [MIT, to confirm]
Data and results: [CC BY 4.0, to confirm]
The AlphaFold Server model file is subject to the AlphaFold Server terms of use: [to confirm before uploading it].

## Citation

Awan, M. A. H. [Title of the preprint]. ChemRxiv, 2026. [DOI]

## Contact

Malik Ahmed Hassan Awan, Bahauddin Zakariya University, Multan, Pakistan. [email]

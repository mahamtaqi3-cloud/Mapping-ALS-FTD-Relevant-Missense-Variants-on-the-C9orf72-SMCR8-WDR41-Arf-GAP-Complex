# Structural Mapping of ALS/FTD-Relevant Missense Variants on the C9orf72–SMCR8–WDR41 Arf GAP Complex

A computational survey that maps missense variants in *C9orf72*, *SMCR8* and *WDR41* onto cryo-EM structures of the C9orf72–SMCR8–WDR41 (CSW) complex. The goal is to identify residues at assembly and GTPase-binding interfaces and to propose biochemical experiments to test them.

> **Status:** work in progress. Results sections below are filled in after the analysis is run.

---

## Background

*C9ORF72* is the most commonly mutated gene in amyotrophic lateral sclerosis and frontotemporal dementia (ALS/FTD), yet the cellular role of the C9orf72 protein is still being worked out. C9orf72 forms a stable complex with SMCR8 and WDR41. Cryo-EM structures show that C9orf72 and SMCR8 are both longin-DENN domain proteins, with WDR41 binding SMCR8, and the complex acts as a GTPase-activating protein (GAP) for Arf-family GTPases (with reported activity on some Rab GTPases as well). In ALS/FTD, repeat expansion reduces C9orf72 protein levels, so loss of complex function is thought to contribute to disease.

Most C9orf72-linked disease is caused by the repeat expansion rather than by missense variants. Missense variants are still useful as **probes of structure–function**: a variant at an assembly interface or at the GTPase-binding site points to residues that may matter for complex stability or for Arf/Rab regulation, and gives a testable hypothesis.

## Research question

Where do missense variants in C9orf72, SMCR8 and WDR41 fall on the CSW complex structure, and which of them may disrupt complex assembly or GTPase binding?

## Approach

The full pipeline runs in one Google Colab notebook (`csw_variant_mapping.ipynb`):

| Step | What it does | Source |
|---|---|---|
| 1 | Verify UniProt accessions (Q96LT7, Q8TEV9, Q9HAD4) | UniProt |
| 2 | Find PDB entries containing all three proteins | RCSB PDB |
| 3 | Download a structure; map chains to proteins and UniProt numbering | PDBe SIFTS |
| 4 | Collect missense variants (optional manual additions from ClinVar/literature) | EBI Proteins API |
| 5 | Add predicted pathogenicity scores | AlphaMissense via AlphaFold DB |
| 6 | Classify each variant as core, surface or interface (and name the interface partner) | Biopython (Shrake–Rupley SASA) |
| 7 | Rank candidates and plot summary | pandas / matplotlib |
| 8 | 3D view and ChimeraX script | py3Dmol, ChimeraX |
| 9 | Save and download results | |

### Structural classification

For each variant residue, solvent-accessible surface area (SASA) is computed for the isolated chain and for the full complex.

- **interface_X**: loses more than 10 Å² of accessible surface on complex formation (X = partner protein; `dimer` for a second copy; `other` for e.g. a bound GTPase)
- **core**: relative accessibility below 20% in the isolated chain
- **surface**: all remaining modelled residues
- **not_in_structure**: residue not modelled (often disordered loops)
- **check_numbering**: residue type at the mapped position does not match the variant (needs manual review)

These thresholds are conventional choices, not biological facts.

### Candidate ranking

Each variant gets one point for each of: rare in population data (frequency below 1e-4 or absent), located at an interface or in the core, and AlphaMissense score ≥ 0.564. The ranking is used only to choose a few variants to discuss. It is **not** a verdict on pathogenicity.

## How to run

1. Open [Google Colab](https://colab.research.google.com) and upload `csw_variant_mapping.ipynb` (File → Upload notebook).
2. Run the cells in order (Runtime → Run all, or step by step).
3. In Step 3, set `PDB_ID` to the structure you want (default: best resolution among entries containing all three proteins).
4. Optional: upload an `extra_variants.csv` (columns `gene,protein_change`, e.g. `SMCR8,R145C`) and set `EXTRA_CSV` in Step 4.
5. Download the output files at the end (Colab deletes them when the session ends).

## Outputs

| File | Contents |
|---|---|
| `csw_variants_annotated.csv` | All variants with structure location, SASA, partners, AlphaMissense, population frequency |
| `csw_top_candidates.csv` | Top-ranked variants |
| `variants_by_location.png` | Variants per gene by structural class |
| `map_variants.cxc` | ChimeraX script colouring variants on the structure |

## Results

*To be completed after running the analysis.*

- Structure(s) used: `PDB ID`, resolution, whether a GTPase is bound
- Number of missense variants analysed per gene
- Distribution across core / surface / interface classes
- Top candidate variants and structural rationale (3–5)

## Proposed follow-up experiments

For the top candidates:

1. Purify wild-type and variant CSW complexes and test assembly (e.g. size-exclusion chromatography or pull-downs).
2. Measure GAP activity on Arf1 and Rab8a/Rab11a (GTP hydrolysis assays) to test whether interface variants reduce activity.
3. Test thermal stability of variant complexes to check predicted destabilization.
4. If relevant to the lysosomal recruitment of the complex, test binding between variant WDR41 and its lysosomal partner(s) in cells.

## Limitations

- Variant effect predictors (AlphaMissense) are predictions, not measurements.
- Population variants are not automatically benign, and disease-associated variants are not automatically pathogenic.
- The variant list comes from an aggregated API and is not a complete ClinVar/gnomAD/literature list. The retrieval date should be recorded.
- Structures represent specific constructs and conformational states; disordered regions may be missing.
- Interface and core classifications depend on the chosen thresholds.
- Variants in the repeat-expansion region (non-coding) are outside the scope of this analysis.

## Repository structure

```
.
├── csw_variant_mapping.ipynb    # full analysis notebook (Colab)
├── README.md
├── data/                        # downloaded structure, variant tables
├── figures/                     # summary figure, ChimeraX script
└── docs/                        # write-up
```

## References

*Add after reading, including the structure papers for the PDB entry you use and the relevant publications from the Joiner Lab.*

## Tools and data sources

UniProt, RCSB PDB, PDBe SIFTS, EBI Proteins API, AlphaFold Database (AlphaMissense annotations), Biopython, pandas, matplotlib, py3Dmol, UCSF ChimeraX.



This project was inspired by the research of the Joiner Lab (Department of Chemistry, University of Cincinnati) on the structure and function of protein complexes in organelle homeostasis and neurodegeneration. It is an independent project and is not affiliated with the lab.

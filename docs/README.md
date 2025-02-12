## Introduction
This workflow is a bioinformatics pipeline designed for taxonomic classification of 16S rRNA gene sequences. It employs BLAST (Basic Local Alignment Search Tool) to compare query sequences against a reference database, allowing for species, genus, and family-level classification based on sequence identity thresholds.

This workflow ensures high-quality classification of Bacteria and Archaea by integrating stringent quality control measures, a taxonomy decision-making algorithm, and a curated habitat-specific reference database. The results can be used in microbiome analysis, environmental studies, and even pathogen identification, and further complemented following downstream applications such as QIIME2.

This particular workflow has been optimized for the Human Oral Microbiome, and its use is not recommended for samples derived from other habitats.

## Bioinfile Databases
BIOINFILE’s databases are high-quality, curated reference datasets designed for precise taxonomic classification of 16S rRNA gene sequences. These databases focus on various microbiomes and have undergone extensive quality control to ensure accuracy in species identification. Each database is optimized for a specific habitat or sample type, such as the Human Oral Microbiome (HOM) – A reference database curated for the characterization of microbial communities in the human oral cavity. It includes well-annotated 16S rRNA sequences from species relevant to oral health and disease.

### Quality Aspects of Bioinfile Databases

#### Sequence Quality
- Primary sequence integrity: All sequences are checked for errors such as ambiguities (N bases) and homopolymers.
- Length control: Short or incomplete sequences that lack informative regions are removed.
- BLAST anomaly detection: Sequences with unexpected similarities or mismatches are flagged for review.
- Hypervariable region coverage: The sequences are selected to ensure they contain the necessary variable regions (e.g., V1-V9) for taxonomic resolution.

#### Taxonomic Accuracy
- Updated taxonomy assignments: The taxonomy is verified using multiple sources, including NCBI Taxonomy, LPSN, SEQCODE, and scientific literature.
- Consistent naming: All sequences are labeled with standardized taxonomic names, avoiding obsolete or conflicting classifications.
- Manual curation: Where automated tools fail to resolve higher taxonomic levels, manual curation is applied.

#### Non-Redundancy
- Single representative per species: To avoid bias in classification, the database includes only one high-quality sequence per species.
- Elimination of duplicates: Ensures computational efficiency and prevents overrepresentation of common taxa.
- Supportive reference backbone: Additional reference sequences are included to provide a stable classification framework.

#### Calibration and Performance Evaluation
- Benchmarking against known samples: Each database is tested with real-world datasets to confirm classification accuracy.
- Resolution power assessment: Determines how well the database can classify partial or full-length 16S sequences.
- Consistency checks: Ensures that classifications are stable and reproducible across different runs.

#### Biomarker Inclusion
- Microbiome-specific biomarkers: Each database includes key species known to be associated with specific health conditions, environmental niches, or industrial applications.
- Core vs. Peripheral species: Databases are structured to contain core species that define a given microbiome, along with supporting taxa to improve classification depth.
- Scientific literature validation: The selection of biomarker species is based on extensive literature review to ensure biological relevance.
- Enhanced disease association studies: Enables more accurate microbial profiling for applications such as oral health, gynecological microbiomes, and pharmaceutical contamination monitoring.

## Decision Making
After a BLAST job has ended, the hitlist is deeply processed following a strict criterion to differentiate coherent matches, i.e., those producing a valid taxonomic classification. This approach is only possible thanks to a carefully curated reference database and the habitat specificity of the pipeline, ensuring classifications are both precise and biologically relevant. Reads undergo a series of filtering steps based on sequence identity and query coverage, followed by a hierarchical consistency check.

The taxonomic boundaries calculated in this process are based on established thresholds in 16S rRNA-based microbial identification. In particular, the 1.3% divergence threshold used to distinguish species, or the 94.5% genus-cutoff are derived from the taxonomic thresholds proposed in Yarza et al., 2014, which provide a well-supported framework for microbial classification. These defined margins help determine the highest possible taxonomic resolution for each read while preventing misclassification due to inherent limitations of the 16S rRNA gene. Depending on the consistency among high-confidence hits, reads are assigned to species, genus, or family levels, with ambiguous cases being marked as unmapped. This structured decision-making process ensures the robustness and reliability of microbial identification, maximizing accuracy while maintaining biological coherence.

### Classification Steps

#### Quality Check (before the BLAST run)
- pident < 75% or Query Coverage < 90% → INVALID, i.e. Not classified.

#### Taxonomic Classification
- pident ≥ 98.7% → Species-level classification if consistent; otherwise, assign genus or family based on consistency.
- 94.5% ≤ pident < 98.7% → Genus-level classification if consistent; otherwise, assign family.
- 80% ≤ pident < 94.5% → Family-level classification if consistent; otherwise, UNMAPPED.

#### Final Decision
- Mapped (Species, Genus, or Family) → If classification is consistent.
- Unmapped → If classification is inconsistent at all levels.

## Output files
- **statistics.txt**: Summary of read distribution across different categories, including total, invalid, unmapped, valid, mapped, and their classification at species, genus, and family levels.
- **invalid_reads.txt**: List of all invalid reads.
- **unmapped_reads.txt**: List of all unmapped reads.
- **reads.txt**: Table displaying the classification of all reads, including their assigned categories.

  | Read ID  | Category          | Classification |
  |----------|------------------|----------------|
  | Read_1   | mapped_species   | k__Bacteria;p__Firmicutes;c__Clostridia… |
  | Read_2   | invalid          | - |

- **features.txt**: Table showing taxonomies identified in each sample along with their absolute abundances. Each row represents a unique taxonomy. To avoid double counting, species-level classifications (e.g., *Escherichia coli*) are separated from genus-level (*Escherichia*) and family-level (*Enterobacteriaceae*) classifications.

  | Taxon ID   | Sample1 | Sample2 | Sample3 |
  |-----------|---------|---------|---------|
  | Feature_1 | 10      | 5       | 0       |
  | Feature_2 | 3       | 8       | 2       |

- **species.txt**: Similar to the *features.txt* table but includes only species-level classifications.
- **genus.txt**: Contains genus-level classifications, combining counts from species (e.g., *Escherichia coli*) and reads assigned only to the genus level (e.g., *Escherichia*).
- **family.txt**: Shows family-level classifications by summing species, genus, and family-only assignments. The total count in this table represents all mapped reads.

## References
- NCBI BLAST+: https://blast.ncbi.nlm.nih.gov/Blast.cgi
- BioInfile 16S Reference Database: https://www.bioinfile.com/databases 
- QIIME2 Taxonomy Classification: https://qiime2.org/
- Taxonomic thresholds: Yarza et al., 2014 (https://www.nature.com/articles/nrmicro3330)


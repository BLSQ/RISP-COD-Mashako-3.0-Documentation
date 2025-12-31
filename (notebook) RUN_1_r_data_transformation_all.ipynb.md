**Purpose:** This notebook acts as an **R-based orchestration layer**. It executes a sequence of R scripts that transform raw DHIS2 data into a "master" analytical table. This table serves as the primary data source for the [**Superset** dashboard](https://pev-rdc.bluesquare.org/#/tableau) and the subsequent report-generation stages ( [(notebook) RUN_3_py_generate_report_filtered.ipynb](<./(notebook) RUN_3_py_generate_report_filtered.ipynb.md>) ).
### Workflow Overview

The notebook sources and executes 6 R scripts in a specific order to build the final dataset. These are the main purposes of each script:
1. **Calculate Denominators** `00_calculate_denominator.r`: use health **pyramid** to create a table (`denominator_ZS.rds`) with the **denominator** for each ZS, as the number of AS\/CS per ZS. Denominators are needed later on for the calculation of the indicators. 
2. **Format “Cibles” data** `00_format_events_Cible_CS_ZS.r`: this data is extracted from DHIS2 in OH. Here we filter and link it to the pyramid so it can be used downstream, and also run some QC and replace missing fields with `0`s (placeholders) to prevent process from breaking. Vibles are needed to calculate the 💉 "**Indicator 5: Disponibilite de Vaccines**"
3. **Format events from Supervision CS** (`00_format_events_Sup_CS.r`) : processes raw supervision events at the Centre de Santé (CS) level, including calculating durations (`total_duration_minute`), enriching data (add levels and names from pyramid), and removing duplicated entries (see more on duplicated entries in [Context and Additional Info](<./Context and Additional Info.md>)).
4. **Format events from Supervision ZS**  (`00_format_events_SUP_ZS.r`) : same logic as `00_format_events_SUP_CS.r` but for Zone de Santé (ZS) level.
5. **Calculate indicators *numerators* at CS level** (`01_calculate_CS.r`) : Calculates the values necessary to later (next script) calculate the indicators, at the Centre de Santé level. Some values are formatted by simply renaming the column name from id (dataElement id) to a human-readable name, or calculated from multiple variables based on formulas (see this [spreadsheet](https://docs.google.com/spreadsheets/d/1XW3JFWwOEP2GzAKgLOjCCQOt0vGXPgzbKxxiIOY8K8w/edit?usp=sharing) for reference). These values can be seen as intermediate steps, which are preserved (exported in output table) so that they can be used for both calculating the indicators (next script) and displaying “raw” data in tables and plots.  
6. **Final Aggregation (`02_calculate_aggregate_ZS.r`):** Merges CS-level results with ZS-level data and denominators to create the final "Master Table."

---
### Inputs & Outputs

| **Type**         | **Path / Source**                      | **Description**                                    |
| ---------------- | -------------------------------------- | -------------------------------------------------- |
| **Inputs**       | `.../GENERIC/data/dhis2_metadata/`     | OrgUnit lists and health pyramid structures.       |
| **Inputs**       | `.../0_extract_data/data/`             | Raw supervision event CSVs (from Stage 0).         |
| **Intermediate** | `.../out/formatted_data/*.rds`         | Binary R data files for each processing step.      |
| **Final Output** | `data_calculated_aggregated_CS_ZS.rds` | The "Master Table" used for all plots and reports. |


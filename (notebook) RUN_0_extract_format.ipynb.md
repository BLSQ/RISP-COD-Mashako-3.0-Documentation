**Purpose:** This notebook acts as an **orchestrator** for the data extraction phase. It manages the execution of multiple sub-notebooks that pull supervision data (events) from DHIS2 and format it for subsequent analysis. It is specifically designed as a workaround for environments that typically execute only one notebook per pipeline step.

### 1. Requirements & Parameters

The notebook relies on the global `month_report` parameter (YYYYmm) passed from the main pipeline to calculate a three-month data window.
- **Logic:** It sets the `END_DATE` to the last day of the reporting month and the `START_DATE` to the beginning of the period two months prior (e.g., if the report is for October, it extracts data from August 1st to October 31st).
- **Target Levels:** The notebook processes data for two distinct organizational levels: **CS** (Centre de Santé) and **ZS** (Zone de Santé).

### 2. Workflow Overview

The notebook uses **Papermill** to trigger ~~three~~ *a single* sub-notebook in sequence, once for the CS level and once for the ZS level.
#### Step 1: Event Extraction (`01_extract_DHIS2_events_Supervision.ipynb`)

- **Primary Action:** Attempts to extract raw supervision event data from DHIS2 based on the calculated date range.
- **Fallback Mechanism:** to ensure the pipeline continues if the DHIS2 connection fails or returns no data, the notebook automatically switches to using the following data:
	- `~/PILOT/data/dhis2_data/supervision_CS/events_w2vb1Acxqkl.csv`  produced by [(pipeline) PILOT 1.1. Extract and Format CS](<./(pipeline) PILOT 1.1. Extract and Format CS.md>) and
	- `~/PILOT/data/dhis2_data/supervision_ZS/events_D3xy01Wf3un.csv` produced by [(pipeline) PILOT 1.2. Extract and Format ZS](<./(pipeline) PILOT 1.2. Extract and Format ZS.md>)

#### ~~Step 2: Metadata Extraction (`02_extract_DHIS2_optionSet_Supervision.ipynb`)~~
- ~~**Primary Action:** Pulls "Option Set" mappings from DHIS2 (the definitions for data field codes).~~
- ~~**Fallback Mechanism:** Similar to Step 1, if DHIS2 is unreachable, it copies pre-existing JSON mapping files from the "PILOT" archive.~~
#### ~~Step 3: Data Formatting (`03_format_events_Supervision.ipynb`)~~
- ~~**Primary Action:** Takes the raw events and metadata mappings from the previous steps to produce a cleaned, human-readable dataset.~~

---
### 3. Inputs & Outputs

| **Type**                | **Path / Source**                                                                                                                                                                                                                                                                                          | **Description**                                                                                               |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| **Input Parameter**     | `month_report`: Pipeline parameter provided by user via pipeline run interface                                                                                                                                                                                                                             | The YYYYMM integer used to define the extraction window.                                                      |
| **Primary Data Source** | DHIS2 Tracker API                                                                                                                                                                                                                                                                                          | Live supervision events and metadata.                                                                         |
| **Fallback Data**       | `.../PILOT/data/dhis2_data/...`<br>Specifically:<br>`~/PILOT/data/dhis2_data/supervision_CS/events_w2vb1Acxqkl.csv`<br>`~/PILOT/data/dhis2_data/supervision_ZS/events_D3xy01Wf3un.csv`                                                                                                                     | Local CSV/JSON archives used if DHIS2 is unavailable.                                                         |
| **Notebook Logs**       | `.../papermill_outputs/`                                                                                                                                                                                                                                                                                   | Executed versions of the 01, 02, and 03 sub-notebooks.                                                        |
| **Processed Data**      | `.../0_extract_data/data/dhis2_data/...`<br>Specifically: `~/AUTOMATED_REPORTS/Pipeline_Rapport_de_la_Zone/0_extract_data/data/dhis2_data/supervision_CS/events_w2vb1Acxqkl.csv`<br>`~/AUTOMATED_REPORTS/Pipeline_Rapport_de_la_Zone/0_extract_data/data/dhis2_data/supervision_ZS/events_D3xy01Wf3un.csv` | Cleaned CSV files ready for the transformation phase ( [(notebook) RUN_1_r_data_transformation_all.ipynb](<./(notebook) RUN_1_r_data_transformation_all.ipynb.md>) ) |

---

### 4. 🚀 Technical Notes & Future Implementations

- **Hardcoded Context:** The current extraction logic contains specific IDs of orgUnits (e.g., for Kwilu province only) hardcoded in the json config file (`~/AUTOMATED_REPORTS/Pipeline_Rapport_de_la_Zone/report_parameters.json`). These will need to be updated (or parameterized) when scaling the pipeline beyond the pilot province. Refer to [this Jira task](https://bluesquare.atlassian.net/browse/RISP2223-477).
- 💡**Consider** (see [Jira task](https://bluesquare.atlassian.net/browse/RISP2223-471)): change the **default behavior to use the data already extracted via [(pipeline) PILOT 1.1. Extract and Format CS](<./(pipeline) PILOT 1.1. Extract and Format CS.md>) and [PILOT: 1.2. Extract and Format ZS](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/pilot-1-2-extract-and-format-zs/), instead of extracting it anew at each pipeline execution** (via `01_extract_DHIS2_events_Supervision.ipynb`). Rationale: 
	- The “PILOT Extract and Format \*\*” pipelines are scheduled to **run daily anyways** (because they are part of the *data transfer to Acasus* process, see [Context and Additional Info](<./Context and Additional Info.md>))
	- The extraction takes a significant portion of the pipeline execution time (~15 min): ![](<./attachments/Pasted image 20251223174903.png>)


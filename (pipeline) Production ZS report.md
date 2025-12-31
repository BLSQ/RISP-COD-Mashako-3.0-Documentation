**Purpose:** Automates the generation of health zone (Zone de Santé) reports for a specific month. It handles data extraction from DHIS2, statistical transformation, visualization, and PDF/HTML report generation.
### 1. Requirements & Parameters

The pipeline requires one input parameter to run:
- **`month_report` (Integer):** The reporting period in `YYYYMM` format (e.g., `202510`). Must be provided by the user via pipeline run interface ![](<./attachments/Pasted image 20251223165439.png>)

### 2. Workflow Overview
The pipeline uses **Papermill** to execute a series of Jupyter notebooks. It is divided into a global processing phase and a loop that generates individual reports for each health zone.
Note that these are all **orchestration notebooks** which call the execution of multiple other scripts. 
#### Phase 1: Data Preparation
Extract and format data for the whole Country (or at least, all available data). Split into 2 steps:
1. **Extraction** via (notebook) [(notebook) RUN_0_extract_format.ipynb](<./(notebook) RUN_0_extract_format.ipynb.md>) (Python): Connects to the DHIS2 Tracker instance (Production) to pull raw data.
2. **Transformation** via [(notebook) RUN_1_r_data_transformation_all.ipynb](<./(notebook) RUN_1_r_data_transformation_all.ipynb.md>) (R): Cleans the raw data and calculates the necessary health indicators.
#### Phase 2: Report Generation (iterative)
This part only processes the data needed for the specific report. So instead of processing the whole available data, it filters it for the relevant periods and ZS.
For this, the pipeline iterates through a list of health zones defined in the `org_units` module. For each zone:
1. **Parameter Update:** It updates `report_parameters.json` with the current health zone name and period. This JSON file acts as a configuration bridge for the notebooks.
2. **Content Creation** via [(notebook) RUN_2_r_generate_report_content_filtered.ipynb](<./(notebook) RUN_2_r_generate_report_content_filtered.ipynb.md>) - (R): Generates the specific 📊plots and 📅tables for the current ZS.
3. **Templating** via [(notebook) RUN_3_py_generate_report_filtered.ipynb](<./(notebook) RUN_3_py_generate_report_filtered.ipynb.md>) (Python): Injects the generated content into an HTML template to produce the final report. Styling relies on CSS file. For more details on this see [Logic of Report assembly with Py, HTML and CSS](<./Logic of Report assembly with Py, HTML and CSS.md>).
#### Phase 3: Export
- **S3 Upload** via [(notebook) RUN_4_py_save_reports_s3.ipynb](<./(notebook) RUN_4_py_save_reports_s3.ipynb.md>) (Python): Moves all generated reports to an S3 bucket and updates the database with the new report URLs for dashboard access.
### 3. Inputs & Outputs

| **Type**                     | **Path / Source**                                                                      | **Description**                                                                                                                                                                                                                             | Location                                                                                                      |
| ---------------------------- | -------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| **OpenHEXA workspace**       | “COD Mashako 3.0 BLSQ”                                                                 | OH workspace that host the connection, notebook, extracted and formatted data and database                                                                                                                                                  | https://app.openhexa.org/workspaces/cod-mashako-3-0/                                                          |
| **OpenHEXA notebook folder** | `AUTOMATED_REPORTS/Pipeline_Rapport_de_la_Zone`                                        | The directory containing all source notebooks and templates.                                                                                                                                                                                | OpenHEXA workspace [Notebooks root directory](https://app.openhexa.org/workspaces/cod-mashako-3-0/notebooks/) |
| **Input Data**               | Name: “DHIS2 Tracker - PROD”<br>Identifier: “dhis2-tracker-prod”                       | Source of raw health data.                                                                                                                                                                                                                  | OpenHEXA workspace [Connection](https://app.openhexa.org/workspaces/cod-mashako-3-0/connections/)             |
| **Config**                   | `org_units.py`                                                                         | Python module containing the list of target health zones. <br><br>Note: current list was manually created, and contains only the 24 ZS of the Kwilu province. This will need to be updated to include more ZS at the later scale-up stages. | Stored in the pipeline (same location as the pipeline.py file)                                                |
| **Output Logs**              | `.../papermill_output/[month_report]/`                                                 | Executed copies of the notebooks (useful for debugging).                                                                                                                                                                                    |                                                                                                               |
| **Final Output**             | Amazon S3 Bucket<br>Name: “cod-bmgf-risp-public”<br>Identifier: “cod-bmgf-risp-public” | The final reports accessible via the project dashboard.                                                                                                                                                                                     | OH workspace [connection](https://app.openhexa.org/workspaces/cod-mashako-3-0/connections/)                   |

___
### 4. ⚠️ Technical Notes & 🚀 Future Implementations
<mark style="background: #D2B3FFA6;">ADD these to JIRA EPIC Nr.5</mark>
   
- **The "JSON Bridge":** Notebooks 2 and 3 do not take direct Papermill parameters for the health zone name. Instead, they read from `report_parameters.json`. If you change the report structure, ensure this file remains the "source of truth" for the current iteration. ==What does this do???==
- **Scalability & Runtime Optimization:** The current implementation is a pilot for the **Kwilu** province. Scaling to all 519 health zones in the country may require optimizing the loop (e.g., parallel execution) to reduce total runtime (currently, the pipeline takes >1h for only 24 ZS … ).
- **The `org_units` module:** the list of health zones in `org_units.py` is updated manually. Therefore change this if you need to include more ZS (i.e., during scaling up phase of the project)
	- 💡Consider: might be more robust to fetched this dynamically from a stable source, such as the DHIS2 instance. This could be achieved my modifying the existing pipeline [Health Pyramid DHIS2](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/health-pyramids-dhis2/) which already produces .**csv** tables with original and cleaned names for the relevant levels. From this, we could simply extract the list of values (strings) under the column `zone_de_sante`.
- **Error Handling:** If one ZS fails in the loop (failure in `run_notebook`), the pipeline stops entirely.
	- 💡Consider: implementing approach that skips problematic ZS’s and continue with the others (with warning).
- **Report Formats:** currently only the PDF format of the reports is saved to S3, however in the future we might want to also export the HTML version to make the reports available in the browser. This could be useful to maintain a UX more similar to the original [Acasus Tableau Dashboard](<./Acasus Tableau Dashboard.md>) (where the dashboard is viewed online)

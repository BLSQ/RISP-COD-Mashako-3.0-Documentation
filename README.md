> [!IMPORTANT]  
> Refer to <b>Jira</b> for additional info and for the most up-to-date information on the status of each process!
### Overview
This is the documentation for the whole process necessary for the production of the 3 reports: 1) Zone, 2) Antenne, and 3) National.

The whole thing happens in the OH ws "[COD Mashako 3.0 BLSQ](https://app.openhexa.org/workspaces/cod-mashako-3-0/)". Look here for a [Tour of the OpenHEXA Workspace](<./Tour of the OpenHEXA Workspace.md>).

Also, check this out for more [Context and Additional Info](<./Context and Additional Info.md>) ! 🧐 

Generally speaking, each report is generated via a pipeline:
1. "Rapport de la Zone" (Zone de Sante or ZS) ← [(pipeline) Production ZS report](<./(pipeline) Production ZS report.md>)
2. "Rapport de l'Antenne" (ANT) ← [(pipeline) Production Antenna report](<./(pipeline) Production Antenna report.md>)
3. "Rapport de bord National" ← 🚧 CURRENTLY EMPTY AS IT DOES NOT EXIST YET 🚧

If you click on the respective links above (👆) you can find a detailed explanation of how each pipeline works. As a quick and generic overview, for each pipeline, we have:
* a Py script (classic OH pipeline), which takes 
* a `parameter` as input (period as "YYYYMM"), and that calls the execution of 
* a .json (configuration) file defining the list of org units for which to create the reports
* 5 orchestration notebooks (some take the same `period` parameter as input), each of which calls one or more
* R or Py scripts, which are where the juicy things happen: data formatting, cleaning, filtering, aggregation and all calculations, creating of the content for the report, then assembling the reports (in an ♻️iterative process that loop over a list of org units, for a defined period as provided in the parameter), and finally loading the reports PDF files into an S3 bucket where they can be fetched via the web interface (based on user choice of report level, location/org unit and period). 
	* 🚨 IMPORTANT: This code was versioned in [this GitHub repo](https://github.com/BLSQ/RISP-COD-Mashako-3.0) but the current code as executed by the pipelines has diverged from the last commit ... ! [TBD how to maintain versioning](https://bluesquare.atlassian.net/browse/RISP2223-472).

And here is a visual overview of the whole process. This example applies specifically to the "Rapport de la Zone" (see names of the pipelines), however, **the same logic and structure applies to the other reports** as well:
![](<./attachments/Excalidraw-2025-12-24-1154_black_nobg.svg>)

### Data sources and input 
Raw data primarily comes from [DHIS2 EZD SNIS](https://ezd.snisrdc.com/dhis/dhis-web-login/) instance from which we extract a bunch of things like data and metadata coming from forms/surveys (events), as well as generic metadata (pyramid, shapes, org units lists) and other stuff (datasets). 
Additional data also comes form spreadsheets (but eventually this should also come from DHIS2), and from "helper" tables that I created provide input and values in a more dynamic way (avoid hard coding). 
For a more detailed list of data sources look at [Documents References & Formulas](<./Documents References & Formulas.md>) and at [Rapport de l'Antenne - Data Sources](<./Rapport de l'Antenne - Data Sources.md>) (although this latter is specific to the "Antenne" level, the **data sources are pretty much the same for the other reports**).

This means that there are also *other* [pipelines](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/?tab=pipelines) (*besides* the ones that generate the reports, which are called "**Production ... report**") that are part of the process but run somewhat separately: scheduled to run independently and at different frequency (i.e., pyramid extraction runs monthly, shapes extraction only once or on demand, events are extracted daily, ... ). 

### Output
The report generation pipeline(s) (see above) take the raw data and metadata and process it to create:
* Intermediate objects:
	* processed data as .CSV and .RDS files (tables) (`/out/formatted_data/`). This data is for the whole country (or better, uses all available data)
	* objects as .PNG images of 📊 plots (`.../out/plots/final/plot*.png`) and ready-to-display .CSV 📅 tables (`.../out/data_for_tables/table*.csv`), which are produced *after* filtering for the specific org unit and period.
* Final objects:
	* 📄 **Report as PDF file**s (`.../out/reports/PDF/`)
		* these files are then written to the S3 bucket to be available to the users via the [web interface](https://pev-rdc.bluesquare.org/#/reports)
	* Report as **HTML** file (`.../out/reports/HTML/`): generated as part of the process (reports are first assembled as HTML files, then rendered to PDF)
	* 🐘 **[Table](https://app.openhexa.org/workspaces/cod-mashako-3-0/databases/public_reports/) in the OH Database**: necessary for the web interface to fetch the correct report from the S3 bucket
	* ♾️ **Data as "master tables" for *Superset***. These tables contain data for the whole country (or, all available data), both raw and calculated/aggregated, depending on the logic implemented in Superset.









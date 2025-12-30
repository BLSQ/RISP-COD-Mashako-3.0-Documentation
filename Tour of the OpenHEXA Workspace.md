
Welcome to where the magic happens!
Let's have a quick look at what the relevant things are and where to find them ...
### 📁 Directories / Folders
For this part of the project, you are only interested in the content of the following folders:
* `~/AUTOMATED_REOPORTS`. Here, you are interested in the subfolders `~/Pipeline_Rapport_*/` which contain:
	* **code**: the main ETL code executed by the "core" pipelines, the ones that generate the reports as PDF files (namely: [(pipeline) Production ZS report](<./(pipeline) Production ZS report.md>), [(pipeline) Production Antenna report](<./(pipeline) Production Antenna report.md>))
	* **data**: raw and formatted
	* **"final" files**: 
		* the content of the report before it is assembled, namely plots (.png) and tables (.csv)
		* the actual assembled reports (.pdf and .html) files
	![](<./attachments/Pasted image 20251230161124.png>)
		You can **ignore** `~/Rapport_de_la_Zone`: that is were I first tested if my code (developed locally) was running smoothly in OH (it does). Then, Lea created the new folders which are actively maintained and necessary to run the current report generation pipelines.
* `~/GENERIC`: this is a bit of a stupid name. It made sense back then because I wanted to distinguish more "generic" processes from processes that are specific to the "Data Transfer to Acasus", which is stored in `~/PILOT/` and `~/PRE-TEST/`. In practice now it contains:
	* Data extracted from DHIS2, stored in `~/GENERIC/data/dhis2_*`
	* Data provided by Acasus, stored in `~/GENERIC/data/from_acasus/` . This is the file needed to add the "Antenna" level to the DHIS2 pyramid.
	![](<./attachments/Pasted image 20251230154446.png>)
* `~/PILOT/`: as mentioned above, this is part of the "Data Transfer to Acasus" process, where we take data collected via DHIS2 and reverse-engineer it to match the format of the Acasus data collection app. The important thing here is that **this folder stores both the code and the extracted data for the** `events` ! For more details and relevant implications for the workflow see [(notebook) RUN_0_extract_format.ipynb](<./(notebook) RUN_0_extract_format.ipynb.md>) (check the point marked with 💡)
* `~/MANUAL_LOAD_TO_DATABASE/`: as the name says, to manually (one-time, try out stuff operations) write tables to the OH Database of this ws. Currently used to write the "master" tables from Superset. This step should be incorporated in the main pipeline once it has become more clear what exactly we want to expose to Superset #addToJira .

🚮 The following folders can probably be deleted (see [this Jira task](https://bluesquare.atlassian.net/browse/RISP2223-473) for more details):
 * `~/PRE-TEST/
* `~/local_code_backup_copy`
* `~/old_working/` 
* `~/DHIS2_ANALYTICS/ 

The rest is probably relevant to someone else (Leyre, Lea, Stephane ... ) so ask around :) 

---
### ⚡ Pipelines
As mentioned above, there are pipelines that produce the automated reports, and pipelines that do *other things*. However, among these there are also processes that are relevant to the report generation because the extra the needed raw data.

Here is what you should pay attention to:
* [PILOT: 1.1. Extract and Format CS](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/pilot-1-1-extract-and-format-cs/)
* [PILOT: 1.2. Extract and Format ZS](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/pilot-1-2-extract-and-format-zs/)
* [OrgUnits DHIS2 - Sup CS](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/orgunits-dhis2-sup-cs/)
* [Health Pyramids DHIS2](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/health-pyramids-dhis2/)
* [DHIS2 Extract Shapes](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/dhis2-extract-shapes/)
* [DHIS2 Extract Cibles](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/dhis2-extract-cibles/)

![](<./attachments/Pasted image 20251230162118.png>)

To see how these contribute to the process, check this flow diagram process overview: ![](<./attachments/Excalidraw-2025-12-24-1154_black_nobg.svg>) 

---
### 🐘 Database tables
In this project we only use the Database to expose data to the S3 bucket and to Superset.
Stephane is in charge of Superset, so for any question refer to him.
Therefore, the only thing that you need to care about here for now is the table `public_reports` : this is used in the step [(notebook) RUN_4_py_save_reports_s3.ipynb](<./(notebook) RUN_4_py_save_reports_s3.ipynb.md>) which allows the users to access the reports via the [Web Interface](https://pev-rdc.bluesquare.org/#/reports).

![](<./attachments/Pasted image 20251230163330.png>)
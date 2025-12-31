This should be everything you need to answer all your questions like *where is that from?* or *what is that?* or *how did you do this?* … 

* **OpenHEXA workspace**: - [COD Mashako 3.0 BLSQ](https://app.openhexa.org/workspaces/cod-mashako-3-0/)

* [DHIS2 EZD SNIS web interface](https://ezd.snisrdc.com/dhis/dhis-web-login/): useful to navigate and manually check stuff - currently the PEV cut our access so this is what you see ... ![](<./attachments/Pasted image 20251224152142.png>)

* **Reports to be replicated (what we are trying to make)**: Acasus is still in charge of (co-)design (with the PEV) and developing the reports for Mashako 3.0. So we basically have to re-create what they made, but using different tools. Therefore, we should look at the current Tableau dashboard. The dashboard itself is exported as print screen-ed PDF, so the Acasus “dashboard” and the Acasus “report” are **effectively the same** thing. How can you see them?
	* [**Tableau dashboard**](https://eu-west-1a.online.tableau.com/#/site/axdata/views/DashboardCatalog-Mashako/DashboardCatalog?:iid=1) ← you can only see if they granted access to your BLSQ Gmail account! These are *not* credentials that can be just shared with 1password! 
	* **Exported Reports (PDFs)**: these we exported by me (Giulia) and saved in [this GDrive folder](https://drive.google.com/drive/folders/1gMGqjlqHiDVsz4AE1DNPFo6AC3hkhKvh?usp=sharing) for easy access. 
		🚧**Note**: the dashboard is still a WIP, so the content of the reports might have changed since my last export ... ! 

* **Formulas for the calculation of Indicators**: [spreadsheet](https://docs.google.com/spreadsheets/d/1XW3JFWwOEP2GzAKgLOjCCQOt0vGXPgzbKxxiIOY8K8w/edit?usp=sharing) developed with Thomas Warichet. This allows to identify the correct DHIS2 dataElements (`de` id's) for the calculations. 

* **Mapping of DHIS2 dataElements (ids) to report elements (labels and figures)**. 
	* Same sheet as above, but look at the specific ***tabs***: “[Mapping for Rapport de la Zone](https://docs.google.com/spreadsheets/d/1XW3JFWwOEP2GzAKgLOjCCQOt0vGXPgzbKxxiIOY8K8w/edit?usp=sharing)”, “**Mapping for Rapport de l Antenne**”, "**Mapping for Rapport National**", "**Coherence**" ![](<./attachments/Pasted image 20251229175140.png>)
	* 👉 Also, very IMPORTANT (!), refer to the following documents with the naming of each “visual” (plot and table) for correct reference and mapping. This is needed to understand both the processing in [(notebook) RUN_1_r_data_transformation_all.ipynb](<./(notebook) RUN_1_r_data_transformation_all.ipynb.md>) and [(notebook) RUN_3_py_generate_report_filtered.ipynb](<./(notebook) RUN_3_py_generate_report_filtered.ipynb.md>):
		* [Rapport de la Zone - Plots & Tables to reproduce](<./Rapport de la Zone - Plots & Tables to reproduce.md>)
		* [Rapport de l'Antenne - Plots & Tables to reproduce](<./Rapport de l'Antenne - Plots & Tables to reproduce.md>)
		* [Rapport de bord National - Plots & Tables to reproduce](<./Rapport de bord National - Plots & Tables to reproduce.md>)
		Also, look at [Context and Additional Info](<./Context and Additional Info.md>) for more explanations on the naming of objects inside the R code for data processing.

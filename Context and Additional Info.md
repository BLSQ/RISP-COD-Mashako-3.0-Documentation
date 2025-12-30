Stuff that is not obvious / cannot be extrapolated/figured out by just looking at the data:

* **Health Pyramid and *other* admin levels**: we use the pyramid as from EZD DHIS2 instance. Levels go from 0 to X. Importantly, for the reports Antenne and National we also use levels that do *not* exist in the pyramid. Namely:
	* **HUB** = unclear to me, but it seems to indicate the "entry points" to the country, or warehouses where the supplies are first stored before being distributed at the periferal storages.
	* **Province** = level 2
	* **Antenne** ← *not* a DHIS2 level
	* **ZS** = level 3
	* **AS** = level 4
	* **CS** = level 5
	
* **CS is equivalent to AS** (even if CS is level 5 and AS is level 4): 
	* Comes from the fact that, for each AS, there is *always* only 1 CS that is supervised
		* “Supervised” = expected to submit a form for the survey “**Supervision CS**” (<mark style="background: #FF5582A6;">ADD id</mark>)
	* so the two terms can be used interchangeably – and they are used as such. 

* **“ZS” and “Antenne” can be both *raw* and *aggregated* data, depending on the context (report)**. Here is an example: "*the antenna of Bandundu the map about Chaine de froid is entirely green despite all indicators below are null (and in red)*": the discrepancy comes from the fact that in the top plot the data comes from the survey at the ZS level, while in the bottom one the data comes from the survey at the CS (AS) level ... ! ![[Pasted image 20251229185631.png]] ![[Pasted image 20251229185738.png]]
* **Redundancy of data processing**: Each report has its own folder (e.g., `~/AUTOMATED_REPORTS/Pipeline_Rapport_de_la_Zone/`), each containing its specific code, and raw (when not extracted from DHIS2) and processed data. This was taken as a "safety" measure: the processes are not fully independent as they rely on sometimes overlapping data (i.e., data that comes from the same survey, but used across multiple reports). For efficiency, it would be nice to "re-use" the same processing across reports, however, given the complexity of the process and weird logic of the reports, it seems safer to keep each reports' data processing "siloed" in independent processes to avoid breaks from propagating across multiple pipelines. *

* **Meaning and reason for `~/PILOT/` folder**:
	* “Legacy“: extraction of events (DHIS2 Tracker data) was first set up for the data transfer to Acasus. The name “PILOT” refers to the stage of the project where data collection with the *new tool* (DHIS2 PROD instance instead of Acasus app) is only done in the pilot province of Kwilu. This is to differentiate the process from the previous stage (`~/PRE-TEST/`) which relied upon the DHIS2 STAGing instance. 
		* Now, this doesn't really matter to you, just know that the **Data Transfer to Acasus and the Report Generation processes share the extraction of the same `events` data!** For more details see [[(notebook) RUN_0_extract_format.ipynb]] and [[Rapport de l'Antenne - data sources]] for context.
		* Therefore, currently the latter still imports the relevant files from the same `~/PILOT/` output location. 
		* 💡 TO DO: eventually it will make more sense (for logical clarity) to me the extraction of events under the `~/GENERIC/` directory (where generic indicates that outputs are used for “generic”/”shared by multiple processes” purposes) #addToJira

* This project relies on DHIS2 **Tracker**, which works differently from the “classic” DHIS2. However, for brevity, across the documentation and the code, the term “DHIS2” is always used to indicate DHIS2 Tracker. To learn more about DHIS2 Tracker see [[DHIS2 Tracker Concepts]].

* Access to the DHIS2 instance (API): we only have one working access: Carlos (*on est tous Carlos* … 😅)
	* No need to know the credentials, just use the the connection stuff functions from the OpenHEXA sdk `con = workspace.dhis2_connection("dhis2-tracker-prod")`
	* ✂️ Even this **access was cut on multiple occasions by the PEV** → if pipelines start to fail with apparent no reason, first assume it has to do with the interrupted connection …

* **“Periods” are _not_ native to DHIS2 Tracker** (!): Unlike DHIS2, DHIS2 Tracker does not work with periods. These are introduced by us in the ETL data processing because the reports are conceived to be on a monthly basis. As such, periods, as 6-digit numerical values (“YYYYMM”) are extracted from raw events metadata (field/column `occurredAt`) in the following manner: `period = format(occurredAt, "%Y%m")`

* **Duplicated entries**: more than 1 submission per orgUnit per period, cause by people updating (modifying) already submitted forms. These are **removed** by keeping only the most recent entry. See example of code from `00_format_events_SUP_ZS.r`: 
```
events_unique <- events %>% 
                        group_by(period, orgUnit) %>% 
                        mutate(updatedAt_max = max(updatedAt)) %>% 
                        filter(updatedAt == updatedAt_max) %>% 
                        ungroup() %>% 
                        select(-updatedAt_max)
```

* 🖇️ **Mapping of produced objects (processed data) to report components (pages, plots, tables)**: this part is super tricky because the report contains many components that all sort of sound the same, sometimes displaying the same thing in slightly different ways (i.e., raw and aggregated data, or same values but in different plots), and sometimes with the same thing having different names across sections of the same report and/or between the survey (fields/dataelement name) and the report (label used in visuals or headers) (i.e., the indicator "Récupération des enfants" is then called "Taux d'abandon" in the later pages of the same report). Therefore, this is my strategy & how to navigate my code:
	* 📉 **Indicators**: I created numbering to refer to them in a more concise and unambiguous way. So typically in the code I refer to `Ind#{d}` or `ind{d}` and similar, instead or together with the indicator descriptive name. As such:
		* See “conversion” table ![[Pasted image 20251014104202.png]]
			* In the code, using the same numbering, I use:
			* `i{d}_` : to refer to “precursors” of the indicator. For example … `i2_passed` is a precursor of the indicator \#2 because to calculate the numerator of indicator \#2 we sum up the number of orgunits where `i2_passed == TRUE` .
			* `ind{d}_` :  to refer to the actual (already calculated) indicator value. Tyoically, this ready to be plotted. 
			* To refer to plots and tables (and their specific components), I set up the following "mapping" system to try to create more concise and unambiguous names. Specifically, I use these documents for reference:
				* [[Rapport de la Zone - Plots & Tables to reproduce]]
				* [[Rapport de l'Antenne - Plots & Tables to reproduce]]
				* [[Rapport de bord National - Plots & Tables to reproduce]]
	* 📊 **Plots**: plots are just embedded in the report *as they are*, and therefore we just need to know *which* object (filename and its location) needs to be placed *where* in the HTML template. To name plots in a logical manner, I simply number it in the order in which they appear in the (original Acasus) report and use the same nomenclature in the code. So, in `~/.../out/plots/final/` you'll find files like "plot1.png", plot2.png", etc.	  ```
	* 📅**Tables**: tables are more complex than plots to embed because they need to be read-in and pre-processed to be correctly rendered in the report. So, after the .csv tables are generated (by [[(notebook) RUN_2_r_generate_report_content_filtered.ipynb]]), this processing, which happens in the script "generate_report.py" (from the [[(notebook) RUN_3_py_generate_report_filtered.ipynb]] ) basically adds the necessary HTML tags so that the different elements of the table (headers, cells/values) are correctly placed and styled (based on CSS file). You can read more about this in [[Logic of Report assembly with Py, HTML and CSS]]. 
		Naming of objects also follows the same logic as the plots, but with more specification to indicate which *component* of a table. This is because in a given table, each column might require different formatting rules (i.e., text color based on numerical value, or replace a string text with a symbol). To make some sense of this, I tried to give somewhat logical names to the columns of the csv tables. For example:
		* `num_` or `nr_` : for "numerator" values
		* `den_` : "denominator"
		* `perc_` : "percentage"
		* … these are either standalone col or values needed to be "assembled" (e.g., a cell displaying `perc % (num / den)`)
	  *So why not simply using the "final" col names (what is actually displayed in the report)?* 🤔 To avoid headaches: special characters and col names with spaces need quotation, therefore I only renamed columns at the very end of the processing (just before exporting the table/df as csv files), for example: 
	  ```
	  data_table16 <- data %>% 
                arrange(aire_de_sante) %>%
                select(`Aire de Santé` = aire_de_sante,
                        `Nombre des agents` = nombre_agents,
                        `Nombre des agents formés au PEV Pratique` = nombre_agents_forme
                )
	    table16_path <- file.path(output_dir, "table16.csv")
		write_csv(data_table16, table16_path)        
	  ```

* 🚧 **Most calculations are still work in progress** ⚠️! Therefore, you will see mismatches in values between the Acasus report and "our" report. Although I said this like a million times, people from Acasus regularly come up with "critiques" to the report pointing out at such mismatches. If you are asked to investigate such "errors", always first check the code that performs the relevant calculations: namely, look at [[(notebook) RUN_1_r_data_transformation_all.ipynb]] and/or [[(notebook) RUN_2_r_generate_report_content_filtered.ipynb]] and read my comments to the code. I use emojis and clear "Note" or "Important" and similar to make it clear when something is unfinished/unclear or wip!

* **"Helper" tables**: to avoid hard-coding too much stuff, I use what I call helper tables to import values (input) in a way that could easily edited by someone else (not requiring coding skills). Specifically, I use this for:
	* `to_extract_ZS.csv` : list of the de id to keep. Specifically, in XXX (add which step) I filter the data (events) to keep only the data relevant to the processing. (I could simply keep everything, but I try to avoid mega heavy tables for performance). Namely, I only keep the dataelements (de id) that are either displayed as they are or are part of calculations (are part of formulas for the calculation of indicators). 
		* Misleading name: initially, I was planning on using this list to extract only the relevant dataelements (again, to keep things light and fast). Hence the `to_extract` part of the filename. Later, I dropped that approach and kept extracting everything and only later on filter the data. Consider streamlining this process once it will have become clear which dataelements are actually used (at the moment we are still figuring out) ... #addToJira 
	* `indicators_definitions.csv`: the actual text of the fist pages of the reports containing the definitions of the indicators. This is fixed text (remains exactly the same in each individual report or the same reporting level; aka does not change across orgunits and periods), so it could be coded in the .HTML file. However, since it's a lot of text, and that will need fixed and updates ( #addToJira  ), I import this text from a CSV in which I provide all the content with all the relevant formatting (see md or HTML tags). In this way, if anyone wants to update it, they can simpy work on a CSV file and replace the file in the correct location (`.../in/fixed_text/indicators_definitions.csv`)
Stuff that is not obvious / cannot be extrapolated/figured out by just looking at the data:
* **Health Pyramid and *other* admin levels**: we use the pyramid as from EZD DHIS2 instance. Levels go from 0 to X. Importantly, for the reports Antenne and National we also use levels that do *not* exist in the pyramid. Namely:
	* HUB ???
	* Province = level 2
	* Antenne ← not a DHIS2 level
	* ZS = level 3
	* AS = level 4
	* CS = level 5
* **CS is equivalent to AS** (even if CS is level 5 and AS is level 4): 
	* Comes from the fact that, for each AS, there is *always* only 1 CS that is supervised
		* “Supervised” = expected to submit a form for the survey “**Supervision CS**” (<mark style="background: #FF5582A6;">ADD id</mark>)
	* so the two terms can be used interchangeably – and they are used as such. 
* **“ZS” and “Antenne” can be both *raw* and *aggregated* data, depending on the context (report)**: <mark style="background: #FFF3A3A6;">TO ADD EXAMPLES OF WHEN IS RAW (SUP ZS) AND WHEN IS AGGREGATED (SUM OF SUP CS) IN WHICH REPORT</mark>. Use this convo with Lea as an example of the utter confusion that this creates https://bluesquare.slack.com/archives/D087W5SGSLQ/p1765465508577059
* Meaning and reason for `~/PILOT/` folder (OH ws):
	* “Historical“: extraction of events (DHIS2 Tracker data) was first set up for the data transfer to Acasus (<mark style="background: #FF5582A6;">create note and add link)</mark> . The name “PILOT” refers to the stage of the project where data collection with the *new tool* (DHIS2 PROD instance instead of Acasus app) is only done in the pilot province of Kwilu. This is to differentiate the process from the previous stage (`~/PRE-TEST/`) which relied upon the DHIS2 STAGing instance. <mark style="background: #FFF3A3A6;">RE-WRITE THIS TO BE MORE CLEAR</mark>!
	* Currently, the same DHIS2 events are also the input of the Report Generation process, so the latter still imports the relevant files from the same PILOT output location. 
		* TO DO: eventually it will make more sense (for logical clarity) to me the extraction of events under the `~/GENERIC/` directory (where generic indicates that outputs are used for “generic”/”shared by multiple processes” purposes) #addToJira
* This project relies on DHIS2 **Tracker**, which works differently from the “classic” DHIS2 (<mark style="background: #FFB86CA6;">ADD LINK TO SOMETHING USEFUL)</mark> . However, for brevity, across the documentation and the code, the term “DHIS2” is always used to indicate DHIS2 Tracker.
* Access to the DHIS2 instance (API): we only have one working access: Carlos (*on est tous Carlos* … 😅)
	* No need to know the credentials, just use the the connection stuff (<mark style="background: #FFB86CA6;">ADD EXAMPLE OF CODE …</mark> )
	* Even this **access was cut on multiple occasions by the PEV** → if pipelines start to fail with apparent no reason, first assume it has to do with the connection …
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
* (THIS COULD BE A DEDICATED NOTE! just mention here and add link to doc with more details) **Mapping of produced objects (processed data) to report components (pages, plots, tables)**: this part is super tricky because the report contains many components that all sort of sound the same, sometimes displaying the same thing in slightly different ways (i.e., raw and aggregated data, or same values but in different plots … ), and sometimes with the same thing having different names across sections of the same report (ADD EXAMPLE OF INDICATORS WITH DIFFERENT NAMES) and/or between the survey (fields) and the report (label used in visuals or headers) (ADD EXAMPLE … taux abandon vs …). Therefore … strategy & how to navigate my code:
	* Indicators: created numbering to refer to them in a more concise and unambiguous way. As such:
		* See “conversion” table (TO ADD, based on screenshot of KPI page with added numbers)
		* In the code, using the same numbering, I use:
			* `i{d}_` : to refer to “precursors” of the indicator. For example … `i2_passed` is a precursor of the indicator \#2 because to calculate the numerator of Ind2 we sum up the number of orgunits where `i2_passed == TRUE` .
			* `ind{d}_` :  to refer to the actual 9already calculated) indicator value – ready to be plotted. 
	* 📊 **Plots**: plots are just embedded in the report *as they are*, and therefore we just need to know *which* object (filename and its location) needs to be placed *where* in the HTML template. To name plots in a logical manner, I simply number it in the order in which they appear in the (original Acasus) report and use the same nomenclature in the code. Specifically, I use these documents for reference:
		* [[Rapport de la Zone - Plots to reproduce]]
		* [[Rapport de l'Antenne - Plots to reproduce]]
		* [[Rapport de bord National - Plots to reproduce]]
	* 📅**Tables**: tables are more complex than plots to embed because they need to be read-in and pre-processed to be correctly rendered in the report. This processing basically adds the necessary HTML tags so that the different elements of the table (headers, cells/values) are correctly placed and styled (based on CSS file). <mark style="background: #FFF3A3A6;">DOUBLE CHECK THAT THIS IS CORRECT AND WRITE MORE CLEARLY!</mark> … Naming of objects also follows the same logic as the plots, but with more specification to indicate which component of a table … :
		* `num` : …
		* `den` : …
		* … add others as I find them 
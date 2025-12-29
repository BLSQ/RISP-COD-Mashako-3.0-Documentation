### What is this documentation about?
In this documentation you should be able to find everything that you need to know continue the work (somewhat autonomously) on the automated report generation. 

So what does "my" code do?
Two main things:
1. 📄 **Produce/generate the Automated Reports**. These are 3 "independent" [^1] reports that display and summarize data at different levels: Zone (as Zone de Sante or ZS), Antenna, and National. For each report (level of reporting), the processing includes data extraction, transformation (data wrangling and calculations) and assembly of the report (PDF file) which are eventually loaded to an amazon S3 bucket, so that files can be fetched by the user interface thingy. 
2. ♾️ **Generate the "master" table for the Superset dashboard**. The dashboard and the report should display the same content (with different layout and interactivity) and therefore rely on the same data. Similarly to the reports, these tables are also produced as "independent" [^1] tables, one for each level of reporting. (Note: we currently don't have a "master" table for the National level report #addToJira )
	1. Note: the logic of filtering and calculation differs to a certain extent between Superset and the report generation processing, and therefore certain steps are "repeated": performed twice, in OH and in Superset, to keep data "light". One such example is the the joining of the shapes (geometries) to the data that is done in OH later in the processing and then again in Superset. Other processes are repeated for other reasons ... (see Jira?)


### Redundancy of data processing
Each report has its own folder (==ADD PATH and examples==) for "safety" (see footnote) ...





### Note from the author
This is where Giulia (me) left things after a year of work. Over time many things changed, mostly in our understanding of the data and the end product. We discovered a lot "as we go" and you will see traces of this in the way the process is structured. Namely, I would have structured things differently if I know then what I know now. Of note, many things are still unclear and expect things to change again to some extent in the future.





[^1]: **independent**: the processes are not fully independent as they rely on sometimes overlapping data (data that comes from the same survey, used in different levels reports). For efficiency, it would have been great to be able to "re-use" the same processing across reports, however, given the complexity of the process, it seems safer to keep each reports' data processing "siloed" in independent processes. For this reason you will see a dedicated folder for each  report generation pipeline: this folder contains the dedicated code, and processed data.

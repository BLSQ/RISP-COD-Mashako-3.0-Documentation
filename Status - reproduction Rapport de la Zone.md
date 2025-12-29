2025-10-02

Still **work in progress**, but so far **achieved** the following:
* Wrote code to **reproduce first 3 pages with plots**:
	1. KPI cards (Mashako score + the 9 indicators)
	2. Heatmap (evolution score)
	3. Map of CS on ZS (qualite supervision)
		* skipping the pages with just text as that can be done later
		* Other plots to be made later, they will be similar in structure to these
* Made demo report that assembles the plot + text and layout
	* Done: headers, ZS, and period
	* To be done: explanation text, add more logos, improve positioning

Problems with the data:
* **CS missing coordinates**. Coordinates extracted from DHIS2 pyramid. **Problematic** because:
	* **Cannot assess quality of location** (user location <500 m from CS) ←- needed for Indicator2
	* **Cannot plot**
		* less important: also AS missing geometry - cannot plot polygons
* **Still too many submissions**: > 1 events per AS per period (ADD DETAILS, MAKE EXTRACT TABLE WITH THINGS TO CORRECT)
* 

Working in:
* Adding “cibles” manually (as per email from XXXXXX) ← wait, not top priority (and eventually this data will be in DHIS2 anyways …)
* 
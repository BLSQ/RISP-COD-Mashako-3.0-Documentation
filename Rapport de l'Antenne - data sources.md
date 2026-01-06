
> [!CAUTION]
> Info might be outdated ... ! Please refer to Jira for the most up-to-date information!

This is supposed to be the most exhaustive list of data needed for the report to happen. Ideally all of this should comes from DHIS2, but we are not there yet. 

 * ✅📲 `events`
	* ✅ "Canevas de supervision des CS", id: "w2vb1Acxqkl" (CS is synonym of AS as there is only and always 1 CS supervised per AS)
		* ⚡ Extracted via this [pipeline](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/pilot-1-1-extract-and-format-cs/)
	* ✅ "Canevas de supervision des ZS", id: "D3xy01Wf3un" 
		* ⚡ Extracted via this [pipeline](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/pilot-1-2-extract-and-format-zs/)
		* 🤌🏼 are there more?? what about 🤌🏼Antenne and 🤌🏼Hub level???
 * ❌📲 also `events`? (probably but cannot see data yet … )
	* ❌ "Réunion de suivi national", id: "bnTCctOP3Hd" 
	* ❌ "Suivi de routine - ZS", id: "r1xMoDp9m1l" 
	* ❌ "Suivi de routine - Antenne", id: "lULft0JaH4b"  
		* ⚡ TO BE Extracted via pipeline(s) (data is *not* there ... )
		* See [Jira task](https://bluesquare.atlassian.net/browse/RISP2223-413) for details
* ✅🎯 `cibles` - `dataSets`: target values decided by the PEV (so, not collected with surveys) which needs to be extracted from `dataSets`:
	* ✅ "Supervision des CS et ZS - Cibles vaccinations", id: “cSr0sEazvAt”
		**Note**: for higher levels (Antenna, National) need to aggregate these values …
	* ✅ Extracted *once* **manually** (data is there), using this notebook: XXXXX
	* ❌⚡Pipeline temporarily set up [here](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/dhis2-extract-cibles/) , but needs improvements! See [Jira task](https://bluesquare.atlassian.net/browse/RISP2223-468) but for more **context** also check [this Jira task (RELEASED)](https://bluesquare.atlassian.net/browse/RISP2223-400) and [this other Jira task (RELEASED)](https://bluesquare.atlassian.net/browse/RISP2223-449), and [this other Jira task ("To Do")](https://bluesquare.atlassian.net/browse/RISP2223-451)
* ❌ 📚 “**Commission logistique**”: data shared via [3 spreadsheets](https://drive.google.com/drive/u/0/folders/1T5CO6E4pAVACMjALGkj0p2cE4gl-sSIC). Should be about “*disponibilité des vaccins et intrants*” (Hub, Ant) and “*suivi des approvisionnements du HUB vers les antennes*” … what about ZS … ?
	* See [Jira task](https://bluesquare.atlassian.net/browse/RISP2223-431) for details
* ✅🏛️ Health pyramid (DHIS2) 
	* Levels 1 (national) to 5 (CS). For back compatibility on case of changes to the pyramid, extraction is scheduled once per month and output saved corresponding folder (“YYYYMM“)
	* ⚡ Extracted and processed via this [pipeline](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/health-pyramids-dhis2/)
* ✅🟣 Mapping of Antenna to pyramid: _not_ a level in DHIS2! 
	* Province (2) > ***Antenna*** > ZS (3) > AS (4) > CS (5)
	* Comes from Acasus and joined to DHIS2 pyramid in OH
	* ⚡ Produced by this [pipeline](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/health-pyramids-dhis2/) (same that extracts pyramid)
* ✅📋List of `orgUnits` to keep: at ZS level (Sup ZS) only 1 CS per AS is expected to be supervised, therefore, all other CS should be filtered out from the pyramid, so that we can calculate the correct denominator for the indicators (i.e., nr of CS\=\=AS per ZS)
	* ⚡ Extracted via this [pipeline](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/orgunits-dhis2-sup-cs/)
* ✅🗺️ `shapes` : needed for map visuals
	* ⚡ Extracted via this [pipeline](https://app.openhexa.org/workspaces/cod-mashako-3-0/pipelines/dhis2-extract-shapes/)
	*  ❌🟣  ***Antenna*** level shapes to be obtained (from PEV or Acasus or 🛠️ made by aggregating ZS shapes)
	
* 🚮 `programIndicator` (pre-calculated and aggregated) which are needed for the indicators, but that I am re-calculating from scratch anyways because I also need to display the raw data in the report ... this data **can probably be entirely discarded** (!) as I am not using any of it in my ETL at the moment (see my comments in [this Jira task](https://bluesquare.atlassian.net/browse/RISP2223-473))

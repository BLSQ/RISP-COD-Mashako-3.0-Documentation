This time already separate between **plots** (ggplot) and **tables** (csv processing in Jinja) … 

Keep **numbering sequential** for ease of assembly (but do not put number first because that messes up R and python). Namely, after table1 comes plot2 (*not* plot1)
## table1
“Classement des antennes sur le mois”
Note:
* NEW from ZS
* Need to calculate Classement of previous month
* In “Écart à l’objectif”: drop symbol (keep only color coding)
Rules:
* “Classement” seems purely based on “Score Mashako”
* *<mark style="background: #FFB86CA6;">How to sort Antenne with exact same Score Mashako???</mark>
![](<./attachments/Pasted image 20251014095921.png>)

## plot2
“Aperçu de l'antenne”

Note:
* More indicators than in “Rapport de la Zone”: 13 + Mashako Score (in ZS was 9 + Mashako). Of these, in the “Indicators Mashako 3.0” sheet we have:
	* 9 match with definitions in the Report (“Report” = what Acasus produces atm)
	* 3 are **missing**: <mark style="background: #FF5582A6;">Supervision ZS, Supervision AS, Reunion ZS</mark>
	* 1 needs **double checking** for correct geographic scope: <mark style="background: #FFF3A3A6;">Livraison de vaccins ZS </mark>(sheets says AS but report says ZS)

![](<./attachments/Pasted image 20251014104202.png>)

## plot3
“Évolution du score Mashako de l'antenne”

Note:
* this was a plot in Rapport de la Zone, but makes more sense to make it a table instead
* Else all good, should be easy to replicate
![](<./attachments/Pasted image 20251014112259.png>)

## table4
“Aperçu des performances Mashako au niveau des zones”

Notes:
* Looks like they left out a bunch of ZS … (see at page 7 = table8 there are more ZS!)
* For the rest should be simply the indicators #3, #4, #6, #8, ~~#9, #10,~~ #11, #12, #13 (the ones that **aggregate at ZS** level, data from “**Sup CS**”)
	* TO BE CLARIFIED (leaving these out for now): the following indicators, *here* are aggregated at ZS level (from Sup CS) but in the summary of indicators ([Rapport de l'Antenne - Plots & Tables to reproduce](<#plot2>)) this is not displayed (instead, it displays the data at Antenna level, from Sup ZS) 
		* “<mark style="background: #FF5582A6;">Disponibilite des vaccins</mark>” (Ind #9):
		* “<mark style="background: #FF5582A6;">Satisfaction des commandes</mark>” (Ind #10): same as above …
![](<./attachments/Pasted image 20251105121959.png>)

## plot5
“Carte des supervisions formative de qualité des zones de santé” - **KPI cards**

Notes:
* Same logic as qualite supervision of previous report (should be straightforward)
![](<./attachments/Pasted image 20251014114117.png>)

## plot6
“Carte des supervisions formative de qualité des zones de santé” - **Map**

Notes:
* Same logic as qualite supervision of previous report (should be straightforward)
* Some <mark style="background: #FFB86CA6;">shapes for ZS are probably missing</mark> (noticed this when I did the Rapport de la Zone - need to do mini analysis for this level) → cannot be plotted
![](<./attachments/Pasted image 20251014114345.png>)

## table7
“Supervisions formative de qualité des zones de santé” - **Antenne** level (aggregated)

Notes:
* should be same logic as before: need to calculate values for each col based on conditions
* <mark style="background: #FF5582A6;">How to calculate choerence??</mark>
![](<./attachments/Pasted image 20251014114654.png>)

## table8
“Supervisions formative de qualité des zones de santé” - **ZS** level (raw)

Notes:
* is this the correct nr of ZS (are all displayed)? 
* <mark style="background: #FF5582A6;">Coherence missing</mark> (as above … )
![](<./attachments/Pasted image 20251014114722.png>)

## table9
“Supervisions formative de qualité des aires de santé” - **Antenne** (aggregated)

Notes:
* Probably need data from “**Sup CS**” (same as “Rapport de la Zone”, but aggregate at Antenne instead of ZS)
* **Ind #4**: “**Supervisions formatives de qualité (AS)**”
* Again, Sup Qualite’ relies on “Coherence” which we don’t have (skip until we figure that out, as done in the previous report)
![](<./attachments/Pasted image 20251014115255.png>)
## table10
“Supervisions formative de qualité des aires de santé” - ZS (raw/not aggregated)

Notes:
* Probably need data from “**Sup CS**” (same as Rapport de la Zone, but aggregate at higher level)
* Again, Sup Qualite’ relies on “Coherence” which we don’t have (skip until we figure that out, as done in the previous report)
![](<./attachments/Pasted image 20251014115315.png>)
## plot11
“Carte des réunions fonctionelles des zones de santé” - KPI card

Notes: again, can recycle code from before
![](<./attachments/Pasted image 20251014115725.png>)

## plot12
“Carte des réunions fonctionelles des zones de santé” - Map

Notes:
* Same logic as qualite supervision of previous report (should be straightforward)
* Some <mark style="background: #FFB86CA6;">shapes for ZS are probably missing</mark> (noticed this when I did the Rapport de la Zone - need to do mini analysis for this level) → cannot be plotted
![](<./attachments/Pasted image 20251014115855.png>)

## 🦘 table13
“"Réunions fonctionelles des zones de santé” - **Antenne** level (aggregated)

🚨 SKIPPING UNTIL WE GET DATA FOR “Suivi de routine … ”

Notes: 
* Not sure which `de` to use here, <mark style="background: #BBFABBA6;">see spreadsheet (details below)</mark>
![](<./attachments/Pasted image 20251014125832.png>)

## 🦘 table14
“"Réunions fonctionelles des zones de santé” - **ZS** level (not aggregated)

🚨 SKIPPING UNTIL WE GET DATA FOR “Suivi de routine … ”

Notes: 
* Not sure which `de` to use here, <mark style="background: #BBFABBA6;">see spreadsheet:</mark>
	* ***Participation des participants concernés*** = “At least 1 event during the period for which answer to IHyGzZ5nTPA includes at least: "mcz","at","is_pev" and "mcz_ai" 
		(The question is a multiple choice question. I assume that the value in DHIS2 looks something like 'abc,def,ghi' where "abc" is one of the available options)
	* ***Partage du compte rendu de réunion avec points d’actions*** = “At least 1 event during the period for which: `D3CcyQSlUC2 = 1 & d2:hasValue(GblEue73vxQ) & d2:hasValue(RmfyCAPNYEG)` “
![](<./attachments/Pasted image 20251014130022.png>)

## plot15
“Fonctionnalité de la chaîne de froid des zones de santé” - **KPI cards**
![](<./attachments/Pasted image 20251014135914.png>)

## plot16
“Fonctionnalité de la chaîne de froid des zones de santé” - Map
🚨 Note: I’ll use the **same** **legend** as the **previous** maps for **consistency** … meaning: red/yellow/green for non-sup/sup but non func/sup & func …
![](<./attachments/Pasted image 20251014135929.png>)

## table17
“Fonctionnalité de la chaîne de froid des aires de santé” - **Antenne** (aggregated)
![](<./attachments/Pasted image 20251014140050.png>)

## table18
“Fonctionnalité de la chaîne de froid des aires de santé” - **ZS** (aggregated) 
![](<./attachments/Pasted image 20251014140102.png>)

## table19
“Disponibilité des vaccins des zones de santé” -  **Antenna** 

Notes: 
* fr<mark style="background: #FFF3A3A6;">om which form???</mark>
* Rule for **color coding** <mark style="background: #FFB86CA6;">depends on another field/reference value</mark>? Which one?
![](<./attachments/Pasted image 20251014140246.png>)

## table20
“Disponibilité des vaccins des zones de santé” -  **ZS** 

Notes: 
* fr<mark style="background: #FFF3A3A6;">om which form???</mark>
* Rule for **color coding** <mark style="background: #FFB86CA6;">depends on another field/reference value</mark>? Which one?
![](<./attachments/Pasted image 20251014140350.png>)

## table21
“Taux de satisfaction des commandes”

Notes:
* rules for first 2 cols: see spreadsheet by Thomas
![](<./attachments/Pasted image 20251014140510.png>)

## plot22 - plot23 - plot24
“Réalisation des séances de vaccination” - AS aggregated at ZS
👉 Note: data from CS (AS) level, aggregated by ZS
<mark style="background: #FFF3A3A6;">TO BE FINISHED (yet to incorporate in the Report!)</mark>
![](<./attachments/Pasted image 20251014140632.png>)

## table25
“Réalisation des séances de vaccination” -  Antenne (aggregated)
![](<./attachments/Pasted image 20251014142750.png>)
## table26
“Réalisation des séances de vaccination” -  ZS (aggregated from CS or raw ZS?)
![](<./attachments/Pasted image 20251014142801.png>)

## plot27
“Carte des récuperation des enfants par zone de santé” - KPI card
![](<./attachments/Pasted image 20251014142901.png>)
## plot28
“Carte des récuperation des enfants par zone de santé” - Map
![](<./attachments/Pasted image 20251014142915.png>)

## table29 - table30
“Taux d'abandon au niveau des aires de santé”

Note:
* “**Variation depuis le mois précédent (pp)**”: to be calculated from previous month (this was not in “Rapport de la Zone”)
![](<./attachments/Pasted image 20251014144805.png>)

## plot31 - plot32
“Carte des formation des infirmier titulaire au PEV au niveau des aires de santé”

![](<./attachments/Pasted image 20251014145634.png>)

## table33 - table34
“Formation des infirmier titulaire au PEV au niveau des aires de santé”
![](<./attachments/Pasted image 20251014145731.png>)


## Structure (based on Fernando’s idea of docu)
This stuff is copy pasted *as is* from his DMs:

> so from you what I expected is to have a guide , if you want a prototype of that document we said per project
> that you can do **to be used by anyone to start from where you left it**
> and then IF you have any time left to do what's more tricky for someone from outside
> I saw your document that's by sources but for me it's hard to grasp it like that
> I think there are **two documents** that could help onboarding
> one is a **visual** image like this: ![[Pasted image 20251223114006.png]]
> (Where the pipeline tells the place where it's the pipeline)
> the other would be a document per pipeline that produces the report (the ones that create the file more than the extraction ones that I assume are easier to infer)
> That would have a structure where for the places that's the most obvious ones you put three sections:
>  ![[Pasted image 20251223114219.png]]
> Finally modify the code (if it's not done already) so that it's structured with sections easily recognizable with sections of the documents (Sections A.2) or general parts ( like library loading, functions)
> For those parts that are missing write the smaller headers (Section B.3) and leave one single commented lines that says we should use source Y but it's missing. Probably better to use graph Y. This in the code notebooks
> Then you make or reagroup one epic per report
> and you move there the full tasks+stories that represents each of the missing sections ( ideally in the comment lines above you add the ID of the corresponding JIRA TASK)
> You add another epic with the pipelines missing and one tasks per pipeline : of those you link them to the section tickets that are missing
> And a 5th epic with things that could be improved or documented or place to be used for anything else missing
> ![[Pasted image 20251223114735.png]]
> And finally at last document is: how to operate in your workflow + R for someone who doesn't do often. Because Leyre isn't used to and so I guess they will welcome it.

> And spend from now to end of the year doing this and if you finish earlier then you can implement things yourself.

## Structure (based on my understanding of Fernando)
Write a technical documentation or "guide" that can be used by anyone to start working (pick up) from where I left.

Produce 3 documents:

1. **Diagram of pipeline input, output and useful context.** More specifically:
	* one diagram per pipeline (i.e., Zone, Antenna, National) 
	* specify data sources needed
	- specify **where** to find the pipeline ("*the place where it's the pipeline*")
	- Note: not clear here if he mean just the pipelines that produce the report (run py + html + css) or also the ETL ... I would do this for **all pipelines**  ... !

2. **Written documentation for each of the above pipelines**
	(although he says "*the ones that create the file more than the extraction ones that I assume are easier to infer*" but I'd document everything to be honest otherwise no one can work on the ETL ... !
	- (each of) this document should have the following structure (3 sections):
		- "Screenshot of the dashboard with the relevant part framed"
		- "Screenshot of the code that reproduces this"
			↳ (if needed) modify the code to map the sections of the script (or code snippets) to the respective sections of the documentation
			↳ for "missing parts" (cannot do because data is missing) write something somewhere (no idea what "For those parts that are missing write the smaller headers (Section B.3) and leave one single commented lines that says we should use source Y but it's missing. Probably better to use graph Y. This in the code notebooks" means) BUT ANYWAYS MORE CODE ALREADY CONTAINS ALL THIS INFO AND CONTEXT
		- "Text clarifying non obvious choices or reasons to be done this way"

3. **User guide on how to operate in my workflow** + **R for python users** (quick guide on what to pay attention to / where R differs from Python)

**Organize JIRA** as follows:
- Have **5 EPICS** (3 already exist because I made them):
	1. **Generate Rapport de la Zone** (this is already quite complete)
	2. **Generate Rapport de l'Antenne** (these I didn't have time to write the tasks, I just did the work)
	3. **Generate Rapport de bord National**
	4. **Missing Pipelines**: list all missing pipeline, each as a task, and link these tasks to ... whatever makes sense because I don't understand what "of those you link them to the section tickets that are missing" means ...
	5. **Others**:  things that could be improved or documented or place to be used for anything else missing
		(note: I already have an epic for this for Rapport de la Zone, but I should "make one story per section")


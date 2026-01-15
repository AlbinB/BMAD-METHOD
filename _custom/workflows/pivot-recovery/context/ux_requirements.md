BMad Method is an AI-driven agile development framework with 21 specialized agents, 50+ guided workflows, and scale-adaptive intelligence that adjusts from bug fixes to enterprise systems.

3 Core Moduels 
Module: BMad Method (BMM), Purpose: Core agile development with 34 workflows across 4 phases 
Module: BMad Builder (BMB), Purpose: Create custom agents and domain-specific modules
Module: Creative Intelligence Suite (CIS), innovation, brainstorming, and problem-solving

BMad Method (BMM) has 3 tracks: 
Quick Flow - Bug fixes, small features
BMAD method - Products and Platforms 
Enterprise - Compliance Heavy Systems 


Modules have 1 or more phases.
Phases are groupings of workflows.
Some workflows are optional, while the other ones are required. 
You will not be able to run a workflow downstream if the pre-required workflow has not been executed and the output of that workflow has not been generated. 
Workflows are groupings of multiple steps. 
A particular workflow is always handled by just one agent. However, at any point throughout the workflow, you can initiate a party / bring in multiple agents into the conversation. 
A workflow consists of:
1. a workflow file (markdown), 
2 template file (markdown),
3. Multiple steps inside that workflow (markdown)
4. There's always an output of that workflow 

References: 
docs/reference/glossary
docs/explanation/core-concepts
docs/explanation/architecture/four-phases.md

The goal of the front-end user interface is to create a mental model aligned with this architecture. 

I believe that our interface needs to show:

In the sidebar: 
<Sidebar Should Be Collapsible> 

Core Modules 
	Creative Intelligence Suite (CIS)
		Agents
	BMad Method (BMM)
		Agents
	BMad Builder (BMB),
		Agents
Custom Modules 
	Custom 1
		Agents
	Custom 2
		Agents

<Clicking on Module will load the module in Canvas> 

Canvas should show: 
Graphical Representation of the Entire Module: 
Phases, 
workflows in each phase, 
distinction between Optional and Required Workflows, 
Which Agent is handling which workflow 

the rest, of the requirements we will need to port over from _bmad-output/planning-artifacts/ux-design-specification.md however, we will need to scrutinize what's in there, and if it was possibly based on the wrong assumptions from the get-go. We don't want to carry over the mistakes. 
# **SDL Development Lifecycle**
You are welcome to participate in the evolution of SDL. Because of high quality expectation of SDL community, every contribution has to pass certain processing before it becomes an SDL evolutionary step.<br>
To make introduction of new ideas into SDL transparent and manageable, we outline in this document major stages of development lifecycle.<br>
We would like to emphasize that every contribution has to conform to agreed upon [definition of done (DoD)][DOD-LINK].

## **Scope**
SDL is a large product with multiple repositories. Some of them contains source codes for features and tests others mainly dedicated to documentation and some R&D experiments. The ones which do not strictly dedicated to documentation or R&D form set of product parts like SDL Core, ATF, ATF Scripts, HMI etc. Later in documentation those repositories are mentioned in generic manner: "product parts".

## **Lifecycle processes**
SDL lifecycle is based on three major processes:
- [SDL Evolution][SDL-EP-LINK]
- [SDL Contribution][SDL-CONT-LINK]
- [SDL Release][SDL-REL-LINK]

Next picture emphasize processes and their outcome in SDL.<br>
![Lifecycle processes](assets/lifecycle.png "Lifecycle processes")<br>

Each one of them might be executed in parallel or sequentially. However for each proposal there is a streamlined development process, outlined on following picture.<br>
![Development streamline](assets/development-streamline.png "Development streamline")<br>

## **Roles**
SDL lifecycle considers interactions of following groups:

- Stakeholders (primary responsible role SDL Project Manager)
- [Change Control Board (CCB)][CCB-LINK] (primary responsible role CCB Group Manager)
- Maintainers (primary responsible role is **Task Assignee**)
- Community (primary responsible role for proposal review is **Proposal Author**)

### Stakeholders
Primary owners and other individuals, groups or organizations which has certain concerns and might influence on SDL development.<br>
Group responsibilities:<br>
1. Assign responsible for proposal processing<br>
2. Assign responsible for tasks realization (*Maintainers*)<br>
3. Approve potential contributors<br>
4. Define priorities/business value of approved proposals<br>
5. Delegate, by need, assignment responsibility (stated in point 1. and 2. of this list) to [CCB][CCB-LINK]<br>

### Change Control Board
[CCB][CCB-LINK] is group of technical specialists gathered and approved by Stakeholders.
Their primary responsibility is to control state of the product.
Detailed description on group duties available in [here][CCB-LINK].

### Maintainers
Approved by Stakeholders potential contributors.
Their primary responsibility is providing delivery according SDL Contribution or SDL Release process.
For each task contributor must be assigned by Stakeholder or CCB.
Relations between *Stakeholders* and *Maintainers* are not part of this description.

### Community
Consumers of new SDL releases.
Generators of ideas and proposals for improvement in SDL project.
People which delivery most of the innovations in SDL through SDL Evolution process.

## **Processes**
This chapter aligns important, for product quality, activities in a production chain.
All processes are built around GitHub and therefore presented as set of GitHub issues and wiki pages.

### [SDL Evolution][SDL-EP-LINK]
Change management is important part of production chain.<br>
Brief description of evolution scenario:<br>
1. *Community* should propose improvements in the scope of [SDL Evolution][SDL-EP-LINK] process<br>
2. *Stakeholders* and *[CCB][CCB-LINK]* define responsible for proposal processing<br>
3. [CCB][CCB-LINK] helps in clarification of specifications and impact<br>
4. *Statement of work* is delineated by stakeholders for **Accepted** after [review][SDL-EP-LINK] proposals<br>

_Statement of work_ for proposal includes set of GitHub issues created in repositories of affected *parts of product* and might include request for release.<br>

> **NOTE:** [SDL Evolution][SDL-EP-LINK] process stipulates possible communication channels, through which *Community* can delivery initial requests or answer questions.

**Outcome (for accepted proposals only):** 
- Issue in [SDL Evolution repository][SDL-EP-LINK] for approved proposal
- Set of issues with requirements and technical tasks in respective repositories of product parts
- And/or set of defect issues in respective repositories of product parts

### [SDL Contribution][SDL-CONT-LINK]
Implementation of the proposal is the cornerstone of the SDL development.
To make the development of SDL predictable and stable *Maintainers* should follow common rules in contribution.
SDL development is based on two principles:
- [A successful Git branching model][GitFlowModel]
- ["Fork & Pull" model][ForkAndPull]

> **Suggestion:** Use [GitFlow][GitFlowTools] for streamlined development.

**Outcome:**
- Stated in technical task deliverable in **develop** branches of affected *product parts*

### [SDL Release][SDL-REL-LINK]
Frequent releases are basis for stable software development.
In SDL releases are requested by Stakeholders through [CCB][CCB-LINK].

**Outcome:**
- Stable release for *Community*

## **Artifacts**
In the confines of OpenSDL lifecycle generic GitHub issues receive following meanings:<br>
- Requirements
- Tasks
- Defects

Next picture shows relations between different arifacts in SDL.<br>
![Artifacts relations](assets/proposal-requirement-tasks-relations-github.png "Artifacts relations")<br>
All of these artifacts have have specific namings and description template.<br>

**How to maintain artifacts**<br>
[GitHub][GitHub-LINK] services capabilities stipulate a lean approach to software development. 
Therefore each created artifact should not provide long descriptions and use as less as possible parameters.<br>

_Crucial elements for model based on GitHub issues are **links**._<br>
Each and every change in existing model automatically triggers necessity to maintain links. Pay attention to the facts that all links between SDL artifacts are bilateral. This implies necessity to update also linked issues.<br>

_To make this process easier, relation must be kept as simple as possible._<br>
Best relationship model: *Proposal* (1 to 1) *Requirement* (1 to 1) *Technical task*.<br>
Maximum allowed complexity: *Proposal* (1 to 1) *Requirement and sub-requirements* (n to 1) *Technical task and sub-tasks*.<br>

> **NOTE:** Always pay attention to provided in issue links and update them by need.

### Requirements
These issues are high level description of expected outcome.
More or less they answer first basic question: **What has to be done?**
They form basis of acceptance criteria for future contribution.
Requirements issue can be created by *Stakeholders* or *[CCB][CCB-LINK]*.
Maximum two level of requirements are allowed.

**How to create requirement issue**<br>
*Select naming (title).<br>*
Title template: *R\<number\>:\<summary\>*<br>
Title must be sequentially numbered.<br>

*Writing of the description.<br>*
Description template:<br>
> **Relations**<br>
> Proposal: *\<Link to proposal issue\>*<br>
> Parent: *\<Link to parent requirement issue or empty\>*<br>
> 
> **Priority**<br>
> Business value: *\<0..100\>*
> 
> **Description**<br>
> *\<High level description of what is required to be done and acceptance criteria or link to the document in repository.\>*
> Expected delivery (in order of execution):<br>
>  [ ] Link to technical task 1<br>
>  [ ] Link to technical task 2<br>
>  [ ] Link to technical task 3<br>
>  ...<br>

*Establish links.<br>*
- To related proposal issue (should be one)
- To parent requirement issue (only one, if applicable)

### Technical tasks
These issues are high level description of technical task.
Basically they should answer second basic question: **How requirements have to be implemented?**
They form basis of acceptance criteria for future contribution.
Technical tasks issue can be created by *Stakeholders* or *[CCB][CCB-LINK]*.
Maximum two level of technical tasks are allowed.

**How to create technical issue**<br>
*Select naming (title).<br>*
Title template: *T\<number\>:\<summary\>*<br>
Title must be sequentially numbered.<br>

*Writing of the description.<br>*
Description template:<br>
> **Relations**<br>
> Requirements: *\<Link to requirement issue\>*, *\<Link to requirement issue\>*...<br>
> Parent: *\<Link to parent technical tasks issue or empty\>*<br>
> 
> **Priority**<br>
> Severity: *\<High|Medium|Low\>*<br>
> 
> **Description**<br>
> *\<High level description of how requirements have to be implemented.\>*<br>
> Expected delivery (read [DoD][DOD-LINK] for details):<br>
>  [ ] Source code updates<br>
>  [ ] Code comments<br>
>  [ ] UTs add/update<br>
>  [ ] Integration tests add/update<br>
>  [ ] SDD updates<br>
>  [ ] SAD updates<br>
>  [ ] Guidelines update 1 (*\<Link to repository with guidelines to be updated.\>*)<br>
>  [ ] Guidelines update 2 (*\<Link to repository with guidelines to be updated.\>*)<br>
>  ...<br>

*Establish following links.<br>*
- To related requirement issues (one or many)
- To parent technical task issue (only one, if applicable)

### Defects
Normally defects should be posted by [CCB][CCB-LINK] basing on the discussion initiated with *Community* in [SDL Evolution][SDL-EP-LINK] process. However members of the *Community* are also welcome to create defects in respective *product parts* repository. In both cases defects creation should follow same principles.<br>

Two main rules for defect creation:
1. Check if defect is already present.
2. One defect per (requirement / test case / environment).

**How to create defect**<br>
*Select naming (title).<br>*
Title template: *D\<number\>:\<summary\>*<br>
Title must be sequentially numbered.<br>

*Writing of the description.<br>*
Description template:<br>
> **Relations**<br>
> Requirement: *\<Link to requirement which is failed\>*<br>
> Test script: *\<Link to test script with test case which fails or empty\>*<br>
> 
> **Priority**<br>
> Severity: *\<Hotfix|Blocker|Critical|Major|Normal|Minor|Trivial\>*<br>
> 
> **Description**<br>
> 
> Found in release: *\<High|Medium|Low\>*<br>
> *\<Detailed description, link to description or test case ID *(must be identifiable in test script)*.\>*<br>
> *\<Detailed description of expected result.\>*<br>
> 
> Test environment:<br>
> - HMI version:
> - ATF version:
> - OS:
> - Transport:
> - Mobile device:
> - Mobile OS:
> - Mobile App version:
> - Mobile App type (media/non-media/navi/any):
> - Virtual Machine using (yes/no):
> 
> Expected delivery (read [DoD][DOD-LINK] for details):<br>
>  [ ] Source code updates<br>
>  [ ] Code comments<br>
>  [ ] UTs add/update<br>
>  [ ] Integration tests add/update<br>
>  [ ] SDD updates<br>
>  [ ] SAD updates<br>
>  [ ] Guidelines update 1 (*\<Link to repository with guidelines to be updated.\>*)<br>
>  [ ] Guidelines update 2 (*\<Link to repository with guidelines to be updated.\>*)<br>
>  ...

*Establish links.<br>*
- To related requirement issues (should be only one)
- To integration test script with test case (only one, if applicable)

## **Useful links**
[SDL Evolution][SDL-EP-LINK]<br>
[Definition Of Done][DOD-LINK]<br>
[Change Control Board][CCB-LINK]<br>
[A successful Git branching model][GitFlowModel]<br>
[Fork & Pull model][ForkAndPull]<br>
[GitFlow][GitFlowTools]<br>
[GitFlow Cheatsheet][GFCheatsheet]<br>

[SDL-EP-LINK]: https://github.com/smartdevicelink/sdl_evolution/blob/master/process.md "SDL Evolution"
[DOD-LINK]: DefinitionOfDone.md "Definition Of Done"
[CCB-LINK]: ChangeControlBoard.md "Change Control Board"
[SDL-CONT-LINK]: SDLContribution.md "SDL Contribution"
[SDL-REL-LINK]: SDLRelease.md "SDL Release"
[GitFlowModel]: http://nvie.com/posts/a-successful-git-branching-model/ "A successful Git branching model"
[ForkAndPull]: https://help.github.com/articles/fork-a-repo/ "Fork & Pull model"
[GitFlowTools]: https://github.com/nvie/gitflow "GitFlow"
[GFCheatsheet]: http://danielkummer.github.io/git-flow-cheatsheet/ "GitFlow Cheatsheet"
[GitHub-LINK]: https://github.com/ "GitHub"

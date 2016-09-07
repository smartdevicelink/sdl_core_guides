# SDL Development Lifecycle

You are welcome to participate in the evolution of SDL. Because of high quality expectation of SDL community, every contribution has to pass certain processing before it becomes an SDL evolutionary step.

To make introduction of new ideas into SDL transparent and manageable, we outline in this document major stages of development lifecycle.

We would like to emphasize that every contribution has to conform to agreed upon definition of done (DoD).

## 1. Roles

SDL lifecycle considers interactions of following actors:

- Stakeholders  (define priorities/business value of spcifications)
- Change Control Board [CCB] (clarify high level specifications and approve readiness of the delivery for *Stakeholders*)
- Maintainers  (implement change requests and fixing defect)
- Community  (propose improvements in the scope of [SDL Evolution Process][SDL-EP])

## 2. Processes

This chapter alines important, for product quality, activities in a production chain.
All processes are built around GitHub and therefore presented as set of GitHub issues and wiki pages.

### 2.1. Request for changes

Change managemenet is important part of production chain.<br>
All improvements have to be proposed in the scope of [SDL Evolution Process][SDL-EP].<br>
Roles which are involved in this process: Stakeholders and CCB.
Usually Stakeholders define responcible for proposal processing, and CCB helps in clarifcation of specifications and impact.

### 2.2. Detalization of request

For proposals which recieved status **Accepted** after [review][SDL-EP] respective *statement of work* will be deliniated.
Statemenet of work for proposal includes set of GitHub issues created in repositories of affected parts of product.
In the confines of OpenSDL lifecycle generic GitHub issues recieve certain meaning like:
- Requirements
- Tasks
- Defects

#### **Requirements**
These issues are high level description of expected outcome.
They form basis of acceptance criteria for future contribution.
Requirements issue can be created by Stakeholders or CCB and have specific namings and template.
Maximum two level of requirements are allowed.



**How to create requirement issue**
1. Select naming (title)
Title tempalte: "R<number>:<summary>"
2. Writing of the description
Description template:
>
>
>

[SDL-EP]: https://github.com/smartdevicelink/sdl_evolution/blob/master/process.md

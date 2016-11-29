# Issue processing workflow, release forming and versioning proposal for SDL

Current document is extension for [SDL Contribution][SDLContribution] process.

## Issue processing workflow
GitHub feature [Projects][GitHubProjects] has to be used in order to simplify issue processing and managing. For every new feature a separate [Projects][GitHubProjects] has to be created, which has to accumulate all issues (WBS) related to the stated feature. Each project must have naming, description, which conforms defined in this document template, and set of predefined columns. <br>
<br>Following picture demonstrates workflow for issue processing in projects. <br>
![Issue workflow](https://github.com/mghiumiusliu/sdl_core_guides/blob/lifecycle/docs/Lifecycle/assets/linear-workflow-for-github-issue.png)

Issue readiness is split on 3 major parts: <br>
1. Ready in development branch <br>
2. Integrated in **develop** branch <br>
3. Integrated in **master** within a release <br>

### Project naming and description
**Project naming** must consist of two parts: release number in square brackets and feature name after space:<br>
_[release number] feature name_ (for ex.: [4.3.0] Extended policy support)

**Project description** must conform following template:
> # Milestone: [\<milestone name\>](https://github.com/smartdevicelink/sdl_core/milestone/TBD)
> Development branch name: **feature/TBD** <br>
> Affected repositories: [sdl_core](https://github.com/smartdevicelink/sdl_core/tree/feature/TBD), [sdl_atf_test_scripts](https://github.com/smartdevicelink/sdl_atf_test_scripts/tree/feature/TBD), [sdl_core_guides](https://github.com/smartdevicelink/sdl_core_guides/tree/feature/TBD), [sdl_hmi_integration_guidelines](https://github.com/smartdevicelink/sdl_hmi_integration_guidelines/tree/feature/TBD) <br>

### Set of columns
Columns in the project defines state of the issue in workflow. <br>
**Open**: set of issues which accepted for feature implementation. _Assignee_: Contributor lead <br>
**In Progress**: issues are being developed by assignee. _Assignee_: Sub-team taking care about this issue (developers, testers, etc) <br>
**Ready**: issues completed in feature branch; all tasks from task list are completed or not required.  _Assignee_: Must be left untouched <br>
**Integrated**: issues implementation integrated into **develop** branch. _Assignee_: Must be left untouched <br>
**Suspended**: issue can be passed to suspended stated during implementation or integration. _Assignee_: Must be left untouched <br>

### Type of work packages and quality gates
There must be separated packages for implementation of features and for release of set of milestones. <br>
Each feature efforts include implementation on dedicated branch and integration of results in **develop**. <br>
Readiness of features is verified on dedicated branch, on **develop** and on **master** after release. <br>
<br>
Quality assurance is done by CI system for feature development and execution of set of published manual tests (User Acceptance Tests) for release.

## Backlog
One special type of project is defined: **[Backlog]**. <br>
It contains 3 columns: <br>
**Open**: accepted proposals and defects <br>
**Under clarification**: issue is being prioritized <br>
**Ready for assignment**: prioritized issues ready to be placed in project <br>
**Rejected**: not accepted proposals and defects <br>

## Milestones and release forming
[Milestones][GitHubMilestone] in SDL organization groups features intended to be created in parallel to a certain date. <br>
After certain milestone a new **release** can be published. <br>
Following is schema of inclusion of issues in milestones and releases. <br>
![Issue inclusion schema](https://github.com/mghiumiusliu/sdl_core_guides/blob/lifecycle/docs/Lifecycle/assets/issue-inclusion-schema.png)

As shown on schema release can include one or more milestones, which includes one or more features, which includes one or more issues.

## Versioning
SDL organization uses 4 digit version numbering: **X.Y.Z.B** <br>
**X**: Major release number; must be changed only in case then user scenarios or APIs are changed. <br>
**Y**: Minor release number; must be changed then set of milestone are reached with features which d not severely change SDL. <br>
**Z**: Maintenance release number; hotfixes only. <br>
**B**: Build number; used by CI system. <br>

**sdl_atf** repository represents a sub-product must also have 4 digit version number, however might not be the same as sdl_core. <br>

[SDLContribution]:https://github.com/mghiumiusliu/sdl_core_guides/blob/lifecycle/docs/Lifecycle/SDLContribution.md "SDL Contribution"
[GitHubProjects]:https://help.github.com/articles/tracking-the-progress-of-your-work-with-projects/ "GitHub Projects"
[GitHubMilestone]:https://help.github.com/articles/about-milestones/ "GitHub Milestones"

# **SDL Release**
Successful iterative development require frequent release of stable product versions. Therefore SDL release process is independent from [SDL Contribution][SDL-CONT-LINK] process, however can be planned as subsequent step of it, if needed.

## **Scope**
In SDL a new release can be requested at any time and might be result of important feature implementation or set of sever hotfixes. SDL release include GitHub branch creation, development (branch stabilization), continuous integration (CI) and continuous deployment (CD) activities.

## **Release process**
At the moment of the release request not all features might be implemented. Therefor [CCB][CCB-LINK] first has to organize feature implementation according [SDL Contribution][SDL-CONT-LINK] process and only after prepare release branch.<br>
Following is the usual scenario for SDL release:<br>
**1.** *Stakeholders* request release which must include certain list of implemented requirements, technical tasks or defects<br>
**2.** *Stakeholders* might request to increase test coverage and make additional testing (also manual)<br>
**3.** *Stakeholders* must discuss with *Maintainers* scope of work and due dates, or delegate this activity to [CCB][CCB-LINK]<br>
**4.** [CCB][CCB-LINK] must prepare respective branches: feature branches and release branch (when all features will be implemented)<br>
**5.** Send implementation request to selected *Maintainers* specifying list of requirements, technical tasks or defects which have to be released<br>
**6.** *Maintainers* provide set of pull requests with features and stabilization fixes<br>
**7.** [CCB][CCB-LINK] review, approve them as long as automated CI system<br>
**8.** Approved release is passed to automated CD system which should publish release on GitHub and on other demo and test stands<br>

## **Useful links**
[Change Control Board][CCB-LINK]<br>
[SDL Contribution][SDL-CONT-LINK]<br>

[CCB-LINK]: ChangeControlBoard.md "Change Control Board"
[SDL-CONT-LINK]: SDLContribution.md "SDL Contribution"

## 1. Introduction

### 1.1. Purpose and Scope of the SAD

This document defines the high-level software architecture for the ***Automated Test Framework (ATF)*** system. It describes the structure and the main components of the system, the project basis and dependencies. The goal of the document is to describe, in sufficient detail, the software components, their responsibilities, behavior, and interfaces. This document provides support for Luxoft, Ford, open-source developers and others to learn system design, limitations, stakeholders, and ways of extension and further development.

### 1.2. Definitions and Abbreviations

Abbreviations used in this document please find in the table below.

| **Abbreviation** | **Expansion**                    |
|------------------|----------------------------------|
| SDL              | SmartDeviceLink                  |

Definitions used in this document are in the table below.

| **Definition** | **Description**                   |
|----------------|-----------------------------------|
| Concern        | A functional or non-functional requirement. |
| Model          | A particular diagram or description constructed following the method defined in a viewpoint. These provide the specific description of the system, which can include identifiable subsystems and elements. |
| Stakeholder    | An individual, group or organization that has at least one concern relating to the system. |

For further definitions and abbreviations, please follow [SDL SAD][Software Architecture Document]

### 1.3. Document Roadmap

The SW architecture of system is considered from different viewpoints:

| **Viewpoint**         | **Viewpoint Description** |
|-----------------------|---------------------------|
| Component             | Functional type of view which describes the system's runtime functional elements and their responsibilities. |
| Component Interaction | Functional type of view which describes interactions of the system's functional elements. Component Interaction view uses component-level sequence or collaboration diagrams to show how specific components will interact. The purpose is to validate structural design via exploration of the software dynamics. |
| Use Case              | Use Case View captures system functionality as it is seen by users. System behavior, that is what functionality it must provide, is documented in a use case model. |
| User Interface        | Functional type of view which describes interfaces of the system's functional elements. |
| Data                  | Describes the way that the system stores, manipulates, manages, and distributes information. The ultimate purpose of virtually any computer system is to manipulate information in some form, and this viewpoint develops a complete but high-level view of static data structure and information flow. The objective of this analysis is to answer the questions around data content, structure, ownership, quality, consistency update latency, references, volumes, aging, retention, and migration. |
| Process State         | Concurrency type of view. Process State View is used to model standard process dynamics that are independent of the loaded components. These dynamics may, for example, be part of a component management infrastructure that loads and controls components in the process. For process dynamics, it is often useful to think in terms of a standard set of states such as initializing, operating, and shutting down |
| Process               | Concurrency type of view. Process View describes processes and process inter-communication mechanisms independent of physical hardware deployment |
| Development           | Describes the architecture that supports the software development Process. This view addresses the specific concerns of the software developers and testers, namely code structure and dependencies, build and configuration management of deliverables, design constraints and patterns, and naming standards, etc. The importance of this view depends on the complexity of the system being built, whether it is configuring and scripting off-the-shelf software, writing a system from scratch, or something between these extremes. |
| Deployment            | Describes the environment into which the system will be deployed and the dependencies that the system has on elements of it. This view captures the hardware environment that your system needs (primarily the processing nodes, network interconnections, and disk storage facilities required), the technical environment requirements for each element, and the mapping of the software elements to the runtime environment that will execute them. |
| Operational           | Describes how the system will be operated, administered, and supported when it is running in its production environment. The aim is to identify system-wide strategies for addressing the operational concerns of the system's stakeholders and to identify solutions that address these |
| Logical               | Logical view focuses on the functional needs of the system, emphasizing on the services that the system provides to the users. It is a set of conceptual models. |

For more information about Viewpoints refer to Architectural Blueprints The “4+1” View Model of Software Architecture:
- <http://www.cs.ubc.ca/~gregor/teaching/papers/4+1view-architecture.pdf>

For detailed UML diagrams notation description please refer to :
- <http://www.uml-diagrams.org/>
- <https://sourcemaking.com/uml>

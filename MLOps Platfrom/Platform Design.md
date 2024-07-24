# Plattform Design

Gliederung: 
	- [x] Intro 
	- [x] Reference Architecture
		- [x] Hardware Layer
		- [x] Infrastructure Layer
		- [x] Plattform Layer
	- [x] User-Zone
	- [ ] Interactions	

**TODO**: 
	- [ ] Komponenten beim ersten Auftreten kursiv schreiben?
	- [ ] alle Komponenten immer groß schreiben?

## Intro: 

Based on the identified general ML platform and MLOps capabilities, we propose a reference architecture for a holistic ML platform. This platform is designed as a three-layer architecture that implements both types of capabilities to support teams during the entire lifecycle of ML models, including experimentation, deployment, monitoring, and retraining. The architecture alone cannot capture all MLOps capabilities, as some require the interaction of several components. Therefore, we first introduce the three-layer architecture of the ML Platform followed by interaction paradigms to fully realize all MLOps capabilities. 

## Three-Layer Architecture

The architecture from bottom to top consists of three layers: Hardware, Infrastructure, and Platform. Each lower layer abstracts the details from the higher layer and addresses its specific task, realizing some of the previously identified capabilities. The final architecture is visualized in Figure X. In the following sections, each layer is described in detail, examining its specific task and how it realizes the previously identified capabilities. 

### Hardware Layer
This layer is located at the lowest level of the layer model and provides the physical resources to the higher layers. For the ML platform, this layer includes CPU, RAM, ML accelerators, storage, and network devices (CB-1, CB-2, CB-3), thereby building the foundation for the ML platform.  

### Infrastructure Layer
This layer builds the bridge between the hardware and platform layers by abstracting the utilization of the physical resources and providing a standardized interface to the platform layer for efficient workload deployment. Therefore, the infrastructure layer must support the previously extracted capabilities (CB-3, CB-4, CB-5):

(In the reference architecture, each capability is realized either directly with a component or requires the introduction of multiple components. The following section derives these components from the capabilities and briefly describes them in the ML platform infrastructure layer context.) 

The infrastructure layer achieves resource pooling (CB-4) by logically combining the hardware resources' computing, storage, and network capabilities in a *Cluster*. Each physical server builds a *Node* in the established Cluster. Moreover, *Virtualization* is used to realize the encapsulation of runtime environments (CB-5). Virtualization allows the placement of several workloads on a single node while ensuring that these workloads are separated from each other and have clear resource constraints. The multi-tenancy support offered by virtualization makes it necessary to introduce a *Resource Scheduler* to optimize workload placement and maximum throughput of workloads. This scheduler assigns each workload to the most suitable node in the cluster based on the workload's resource requirements and the node's current capacity. A *Monitoring Component* continuously tracks each node's resource capacity to achieve this scheduling. Furthermore, as virtualization makes the workloads independent from the underlying hardware, a *Cluster State Watcher* is introduced to reallocate workloads to a different node if their current assigned node becomes unavailable, thus guaranteeing high availability.

To realize the network capability (CB-3), and as the previously introduced components rely on communication with each other, a *Network Manager* creates and manages a virtual network within the cluster. Moreover, the network manager connects the virtual network with outside networks and makes workloads addressable. This allows workloads to communicate with each other and be accessible from outside the cluster.  

Lastly, we introduce a single *Management Endpoint* that abstracts the complexity of the underlying infrastructure. This endpoints allows to manage and deploy workloads but also to configure and monitor the underling cluster. 

 

### Platform Layer 
Structure:
- Intro
- describe each component:
	- what features / what is the component capable to do
	- relationship to MLOps capabilities
	- unique aspect to the component
	- interaction with other components in order to realize their associated capabilities


- include model training and serving infrastructure

The platform layer forms the core of the MLOps platform. It abstracts the underlying complexity of the hardware and infrastructure layer, realizes all MLOps capabilities from CB-6 to CB-17, and thus supports teams during the entire ML lifecycle.

We derived n components based on the MLOps capabilities, which are summarized along with their corresponding capabilities in Table X.

The derived components are categorized into four groups based on their primary use: artifact management, automation, user management, and development and deployment zones.

#### Artifact Storage

The primary use case of the components Source Code Management (SCM), Data Store, Metadata Store, Model Registry and Image Registry is to store some kind of artifact and make it accessible.

##### SCM

A SCM is required to store, version (CB-15), and collaborate (CB-11) on the source code of an ML project. Furthermore, an SCM allows sharing of the versioned source code of the ML project with an independent third party, simplifying the recreation of the results and enhancing overall reproducibility (CB-17). Lastly, the SCM enables Continuous Integration (CI), as CI processes build upon the code stored in the SCM.  

##### Model Registry
  
Similar to the SCM, the model registry is needed to store, version (CB-15), and share trained ML models (CB-11, CB-17). Additionally, CD (CB-7) and Continuous Deployment (CB-8) utilize the model registry to store and retrieve trained models during the deployment process.

##### Data Store

A data store is essential to store and serve different versions of data artifacts, such as raw datasets or extracted features (CB-15). These functionalities are also required by CM (CB-10) and CT (CB-9). CM, on the one hand, requires two dataset versions to detect data transitions like data drift, while CT, on the other hand, needs to detect data changes to re-execute the ML pipeline on the newest data. Further, the data store should support sharing data artifacts with colleagues (CB-11) and third parties to foster reproducibility (CB-17). Therefore, the data store should be explorable.


##### Image Registry
To completely comply with the versioning capability (CB-15) and increase reproducibility (CB-17) and collaboration (CB-11), an image registry is introduced. The images from which a runtime environment is instantiated can be stored, versioned, and shared via the image registry. This ensures that tasks executed on the platform are reproducible, as each task must be contained within a runtime environment, and through the versioning, the runtime environment can be recreated at any time. Finally, sharing a versioned image increases reproducibility and collaboration since a third party can work in the same runtime environment.
 
##### Metadata Store

The metadata store mainly results from the metadata capture capability (CB-16). Hence, it must support capturing the defined metadata categories: model, dependency, and pipeline metadata. To realize the dependency metadata category, which is responsible for establishing a full model lineage, the metadata store must interact with the SCM, Data Store, Model Registry, and Image Registry. This interaction can occur directly with each component or through a chained approach. In the chained approach, the Metadata Store links to the first component, which then links to one of the remaining components. For example, the metadata store could link its captured metadata only to the responsible code version at the SCM. The source code then links to the used image and dataset and the resulting model, which also enhances reproducibility (CB-17).

Additionally, the metadata store is required to improve collaboration (CB-11) and enable CM (CB-10). The metadata store reduces knowledge silos about model dependency, pipeline, and general model metadata by providing this information at a central location. CM uses the stored information about the model and the pipeline in the Metadata Store to compare the deployed model with its previously captured parameters during training to detect changes like higher inference latency or reduced model performance. 

Artifact Manangement

- SCM CB-11, CB-15, CB-17
- Model Registry CB-7, CB-8, CB-11, CB-15, CB-17
- Data Store CB-9, CB-10, CB-11, CB-15, CB-17
- Metadata Store CB-10, CB-11, CB-16, CB-17
- Image Registry CB-11, CB-15, CB-17


#### Automation
This group's essential task is to increase the overall level of automation (CB-14) through CI, CD, Monitoring, and Image Builder components. 

##### CI
The CI component is primarily needed to perform integration tests (CB-6). Beyond that, it also encourages collaboration (CB-14) as team members more frequently merge code changes into a shared repository. Consequently, each team member receives feedback about the project's current status through the test results.  

- Perform integration tests (CB-6) 
- Collaboration (CB-11)
	- every team member receives feedback
- increases automation (CB-14)

##### CD
A continuous delivery component is introduced to realize CD (CB-7) and Continuous Deployment (CB-8). This component performs and orchestrates the deployment process, and is essential for achieving a high automation level (CB-14).

- orchestrate deployment process (CB-7, CB-8)
- increases automation CB-14

##### Monitoring System
The capability of continuous monitoring (CB-10) requires the implementation of a Monitoring System. This system gathers data such as predictions and incoming data distribution from the deployed model and compares them to the data stored in the Metadata Store. In case the Monitoring System detects an anomaly like data drift or model degradation, it can automatically re-trigger the deployed training pipeline (CB-14).  

- Continuous Monitoring (CM-10)
- Automation (CB-14)
	- enabler to trigger deployed pipeline

##### Image Builder 
Every task on the platform must be executed inside a runtime environment, which is instantiated from an image. Therefore, to instantiate a runtime environment, the image must be created automatically (CB-14). The Image Builder handles this automation by creating the final image from a manifest and storing it for consumption and versioning in the Image Registry. Additionally, to comply with Versioning (CB-15) and enhance Reproducibility (CB-17), the manifest describing the image should be tracked in the SCM. 

#### User Management
In an ML project, several people work together and use the components of the ML platform. Therefore, to allow a user to own their private data but also share it and thereby decrease isolated knowledge (CB-11), an identity provider is needed. The Identity Provider authenticates each user and assigns them a unique ID. This ID can then be used by different components to authorize the user and allow them to share specific resources with others.


#### Zone Management
CD (CB-7) and Continuous Deployment (CB-9) rely on deploying the final artifact, the ML pipeline or ML model, to a dedicated production environment. Additionally, a dedicated development environment is required to allow testing of the ML pipeline and provide developers with computation environments on demand for development (CB-5+). 

In order to realize both the production and development environment, a Zone Manager is introduced. The Zone Manager creates, administers, and deletes zones. A zone encapsulates several computation environments, separating them from one another, and guarantees and restricts the total resource usage of the computation environments within the zone. For instance, a zone with a resource limit of 100 GB RAM usage restricts the total resource consumption of all execution environments in the zone to be below 100 GB for the RAM usage. Overall, two types of zones can be created: Development Zones and Deployment Zones.

Development Zones: These zones are characterized by two properties. Firstly, a development zone is bound to a single developer. Secondly, the zone is resource limited, so that a single developer cannot accidentally consume all available resources. Consequently, a developer can create as many computation environments as required until the zone’s resource limit is reached. 

Deployment Zones: In contrast to the Development Zones, the Deployment Zones are bound to a ML project and have both a resource guarantees and limits. Thus, the production computation environments achieve high availability through the resource guarantees, but also resource limits to avoid starving other environments.  



#### Development Zone

- developer has a dedicated zone with resource assertions in which they can:
	- test deploy a ML pipeline
	- create runtime environments for development
	- utilize all components of the platform and use provided hardware 
 

#### Deployment Zone
In order to deploy the ML pipeline and serve the final trained model, a dedicated Deployment Zone is required by the CD process (CB-7, CB-8). While the Deployment Zone offers the same capabilities as the rest of the platform, it ensures that a defined minimum number of resources are available to guarantee the availability of the deployment for the runtime environments executed within this zone.  


| Principles | Components |
|:--|:--|
| CI / CD | CI, CD, Continuous Deployment, Automation |
| Monitoring System | CM, CT, Automation |
| ML-Pipeline Orchestrator | ML Pipeline Orchestration |
| Identity Provider | Collaboration |
| Metadata Store | Collaboration, Metadata Capture, Reproducibility |
| SCM | Collaboration, Versioning, Reproducibility, CI / CD |
| Model Registry | Collaboration, Versioning, Reproducibility, CI / CD, CM|
| Data Store | Collaboration, Versioning, Reproducibility, CI/CD, CT |
| Image Registry | Automation, Versioning|



- link artifacts for reproducibility -> add this to interaction paradigms


## User Zone

- development
- staging environment -> test deploy models, other activities


- not directly derived from the principles, but from the overall goal to create a holistic ML platform, we introduced a development zone. The development zone offers platform users, an environment to experiment and test ML models using the underling and the offered components. 

TODO: 
	- include workflow agnostic -> later 
	- can be integrated at every point in the development -> later




# Ideen
Cloud-Native: CNCF

- build and run scalable applications -> required for ML learning
- relies on:
	- Containers
	- Service Mesh
	- Microservices
	- Immutable infrastructure 
	- declarative API
- results in:
	- loosely coupled systems
	- resilient 
	- manageable 
	- observable
	- robust automation
	- frequently high-impact changes



Infrastructure Layer:
- 

Platform: 

derive components from the general ML requirements and identified MLOps capabilities and combine them into a single platform

General ML Requirements: 
	- orchestration component: manages the components on the platform
	- scheduler: move workloads to machines
	- environment runtime: encloses and manages the lifecycle of an application


Stellt: 
	- Resource Provisioning (CPU, GPU, Storage, Network)
	- Execution Environments (Components, Deployed Model, Deployed ML Pipeline) & Development Environments
	- Components




To be independent from any development workflow we offer interaction paradigms to realize the MLOps capabilities more completely and let them integrate more in existing development paradigms 
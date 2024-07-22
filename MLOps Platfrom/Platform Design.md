# Plattform Design

Gliederung: 
	- [x] Intro 
	- [ ] Reference Architecture
		- [x] Hardware Layer
		- [x] Infrastructure Layer
		- [ ] Plattform Layer
	- [ ] User-Zone
	- [ ] Interactions	

## Intro: 

Based on the identified general ML platform and MLOps capabilities, we propose a reference architecture for a holistic ML platform. This platform is designed as a Platform-as-a-Service (PaaS) solution implementing MLOps capabilities to support the entire lifecycle of ML models, including experimentation, deployment, monitoring, and retraining. The architecture of the PaaS is not able to capture alone all MLOps capabilities, as some require the interaction of several components. Therefore, we first introduce the reference architecture followed by the interaction paradigms to fully realize all MLOps capabilities.  

TODO: 
	- include workflow agnostic
	- can be integrated at every point in the development 

## Reference Architecture

3 layer Idee, welt nicht neu erfinden, auch bei dem PaaS auf etwas aufsetzten, was die Orchestrierung, Scheduling, Routing auf der Platform übernimmt, wie zum Beispiel ein Container Orchestration Framework

- Platform wird als PaaS angesehen, hat dennoch Anforderungen an die zugrunde liegenden Layer
- drei aufeinander aufbauende Layer unterteilt (Hardware, Infrastructure, Platform)
- Haupt Interaction der Entwicklung und Deployments findet in Layer 3 statt -> bietet dem User die Möglichkeit ein Model zu entwickeln und deployen durch MLOps Capabilites
- zugrunde liegende Layer sind enabler für den Application  Layer und kümmern sich um die Bereitstellung und Verwaltung der notwendigen Ressourcen und machen es gegenüber den End-User transparent
- Buttom-Up Erklärung der Platform
- kurze Erklärung der jeweiligen Layer 
- die Komponenten die pro Layer existieren um die Capabilities umzusetzten
- pro Layer lassen sich nicht alle Capailities mit einer einzelnen Komponente umsetzten -> Einführung von „Interactions“, die eine Capability umsetzt, die mehrere Schritte benötigt oder von anderen Komponenten abhängig ist
- interactions sollen es auch workflow agnostic machen 

### Hardware Layer
- contains the hardware 
- CPU, ML Accelerators (Computation Infrastructure)
- Storage (Storage)
- Network (Network)
- RAM (Computation Infrastructure)

This layer is located at the lowest level of the layer model and provides the physical resources to the higher layers. For the ML platform, this layer includes CPU, ML accelerators, storage and network devices (CB-1, CB-2, CB-3) and thereby building the foundation for the ML platform.  

### Infrastructure Layer
**TODO**: 
	- Komponenten beim ersten Auftreten kursiv schreiben?
	- alle Komponenten immer groß schreiben?

This layer builds the bridge between the hardware and platform layers by abstracting the utilization of the physical resources and providing a standardized interface to the platform layer for efficient workload deployment. Therefore, the infrastructure layer must support the previously extracted capabilities (CB-3, CB-4, CB-5):

In the reference architecture, each capability is realized either directly with a component or requires the introduction of multiple components. The following section derives these components from the capabilities and briefly describes them in the ML platform infrastructure layer context. 

The infrastructure layer achieves resource pooling (CB-4) by logically combining the hardware resources' computing, storage, and network capabilities in a cluster. Each physical server builds a node in the established cluster. Moreover, virtualization is used to realize the encapsulation of runtime environments (CB-5). Virtualization allows the placement of several workloads on a single node while ensuring that these workloads are separated from each other and have clear resource constraints. The multi-tenancy support offered by virtualization makes it necessary to introduce a resource scheduler to optimize workload placement and maximum throughput of workloads. This scheduler assigns each workload to the most suitable node in the cluster based on the workload's resource requirements and the node's current capacity. A monitoring component continuously tracks each node's resource capacity to achieve this scheduling. Furthermore, as virtualization makes the workloads independent from the underlying hardware, a cluster state watcher is introduced to reallocate workloads to a different node if their current assigned node becomes unavailable, thus guaranteeing high availability.

To realize the network capability (CB-3), and as the previously introduced components rely on communication with each other, a network manager creates and manages a virtual network within the cluster. Moreover, the network manager connects the virtual network with outside networks and makes workloads addressable. This allows workloads to communicate with each other and be accessible from outside the cluster.  

Lastly, we introduce a single management endpoint that abstracts the complexity of the underlying infrastructure. This endpoints allows to manage and deploy workloads but also to configure and monitor the underling cluster. 


These required functionalities can be realized through various components and technologies, such as container orchestration systems and virtualization platforms. 



### Platform Layer 
Structure:
- Intro
- describe each component:
	- what features / what is the component capable to do
	- relationship to MLOps capabilities
	- unique aspect to the component
	- interaction with other components in order to realize their associated capabilities


- include model training and serving infrastructure
- maybe better to speak about realizing a principle?

The platform layer forms the heart of the MLOps platform. It hides the underlying complexity of the hardware and infrastructure layer, realizes all MLOps capabilities from CB-6 to CB-17, and thus supports teams during the entire ML lifecycle.

Based on the MLOps capabilities CB-6 to CB-17 we derived ten components. The components and the principles from which they were deduced are listed in Table x and are also displayed in the reference architecture in Figure x. 

The derived components are grouped into the three groups Artifact Management, Automation, and User Management based on their primary use.

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

Additionally, the metadata store is required to improve collaboration (CB-11) and enable CM (CB-10). The metadata store reduces knowledge silos about model dependency, pipeline, and general model metadata by providing this information at a central location. CM uses the stored information about the model and the pipeline in the Metadata Store to compare the deployed model with its captured parameters about the same model to detect changes like higher inference latency or reduced model performance. 




Artifact Manangement

- SCM CB-11, CB-15, CB-17
- Model Registry CB-7, CB-8, CB-11, CB-15, CB-17
- Data Store CB-9, CB-10, CB-11, CB-15, CB-17
- Metadata Store CB-10, CB-11, CB-16, CB-17
- Image Registry CB-11, CB-15, CB-17


Automation
- CI
- CD / Continuous Deployment
- Monitoring System
- Image Builder

User Management
- Identity Provider

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


In the following we shortly explain the task of each component  and their interaction with other components to realize the components associated capabilities. 

- link artifacts for reproducibility -> add this to interaction paradigms

Componente | Principles

CPU | Computation Infrastructure

ML Accelerator | Computation Infrastructure
ML Accelerator Interface | Computation Infrastructure

Storage | Computation Infrastructure
Storage Interface | Computation Infrastructure

Network | Computation Infrastructure
Network Configuration | Configuration Infrastructure

Virtualization | Encapsulation of Computation Environments

- CI | CI, Automation
- CD | CD, Automation
- Continuous Deployment | Continuous Deployment, Automation
- Continuous Monitoring | Continuous Monitoring, Automation
- Monitoring System | Continuous Monitoring
- Continuous Training | Continuous Training, Automation

- Identity Provider | Collaboration

- Pipeline Orchestrator | ML Pipeline Orchestration
- Container Builder | Automation

- Data Store | Versioning
- Feature Store | Versioning
- SCM | Versioining
- Container Registry | Versioning

- Metadata Store | Metadata Capture

- Client | Collaboration
- 


## User Zone

- development
- staging environment -> test deploy models, other activities


- not directly derived from the principles, but from the overall goal to create a holistic ML platform, we introduced a development zone. The development zone offers platform users, an environment to experiment and test ML models using the underling and the offered components. 





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
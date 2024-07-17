# Plattform Design

Gliederung: 
	- [x] Intro 
	- [ ] Reference Architecture
		- [ ] Hardware Layer
		- [ ] Infrastructure Layer
		- [ ] Plattform Layer
	- [ ] Interactions	

## Intro: 

Based on the identified general ML platform and MLOps capabilities, we propose a reference architecture for a holistic ML platform. This platform is designed as a Platform-as-a-Service (PaaS) solution implementing MLOps capabilities to support the entire lifecycle of ML models, including experimentation, deployment, monitoring, and retraining. The architecture of the PaaS is not able to capture alone all MLOps capabilities, as some require the interaction of several components. Therefore, we first introduce the architecture followed by the interaction paradigms  to fully realize all MLOps capabilities.  

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

## Hardware Layer
- contains the hardware 
- CPU, ML Accelerators (Computation Infrastructure)
- Storage (Storage)
- Network (Network)
- RAM (Computation Infrastructure)

This layer is located at the lowest level of the layer model and provides the physical resources to the higher layers. For the ML platform, this layer includes CPU, ML accelerators, storage and network devices (CB-1, CB-2, CB-3) and thereby building the foundation for the ML platform.  

## Infrastructure Layer
TODO: mische ich hier mein Vorgehen? Sollten diese capabilities nicht vorher schon erklärt werde? 

This layer builds the bridge between the hardware and platform layers by abstracting the utilization of the physical resources and providing a standardized interface to the platform layer for efficient workload deployment. Therefore, the infrastructure 
layer must support the following key functionalities:

1. Resource Management (CB-4) 
	- Resource pooling
	- Resource scheduling
	- Resource relocation
2. Multi-tenancy support (CB-5)
3. Network creation and management (CB-3)
4. Management endpoint

In the reference architecture each key functionality is realized with one or more components, as visualized in Figure x. In the following these functionalities are briefly described and the required components are derived.

The infrastructure layer utilizes resource pooling to create a unified view of the hardware resources by logically combining their computing, storage, and network capabilities in a cluster. Each hardware server is considered a node in the established cluster. Moreover, virtualization is used to enable the placement of several workloads on a single node, along with ensuring that the workloads are separated from each other and have clear resource constraints. This multi-tenancy support creates the demand for the resource scheduling feature to optimize for maximum throughput of workloads, thereby eliminating waiting times for workload execution. A scheduler assigns each workload to the most suitable node in the cluster, based on the workload's resource requirements and the node's current capacity. To achieve this scheduling, each node's resource capacity is continuously monitored. Furthermore, workloads are automatically reallocated to a new node if their current node becomes unavailable to guarantee high availability.    

Cluster, Nodes, Scheduler, Virtualization, Resource Monitor, 

In addition to these resource management functions, the infrastructure layer should support network creation and management. This requires establishing and maintaining a network between independent nodes and making workloads executed on a node reachable and addressable within the cluster. Beyond that, it must enable communication with resources outside of the cluster to make overall communication with external and internal resources possible.

Network component

Lastly, the infrastructure layer should offer a single management endpoint that abstracts the complexity of the underlying infrastructure while allowing for the deployment, configuration and management of workloads.

These required functionalities can be realized through various components and technologies, such as container orchestration systems and virtualization platforms. 

   


 

Network:
	- create a network in the cluster to allow communication between nodes and between deployments on the nodes 
	- make deployments addressable  
Multi-tenancy support:
	- isolate the deployments 
	- restrict the resource usage of a deployment

## Platform Layer 
- orchestration 
- scheduling




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


## Mapping Components Principles

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


















To be independent from any development workflow we offer interaction paradigms to realize the MLOps capabilities more completely and let them integrate more in existing development paradigms 
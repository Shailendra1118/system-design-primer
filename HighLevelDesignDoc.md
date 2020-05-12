# Functional Requirements Overview
1. List High level uses cases which are significant from technical design perspective


# Non-Functional Requirements
1. Quantify the throughput and latency requirements. 
2. Identify the key fault tolerance and resilience scenarios.
3. Identify the key security requirements.
4. Identify the data management requirements (volume, growth, archiving, deletion, privacy)

# Design Summary
1. Overview of the key design problem and how they are approached and solved. 
2. Highlight the choice of framework, key patterns used.

# Dependencies
1. Back-end components, services.
2. List Internal Services, Platform Components, and Infrastructure Components

# Consumers
1. Front-End componenents, services.
2. List Initial Internal Services, Applications


# Components(Main design)
HLD:
1. Diagram showing the different components  and its connections 
2. Responsibilities of each component from non-functional and key functional point.
3. How does this component perform those responsibilities.

LLD:
1. Interfaces to these components (API, Commands, Events, Streams, Exceptions)
2. Components are organized (layered, ports and adapters, hexagonal, pipelines-{sequential},{fork-join}, {parallel}, DAG)
3. Compile time/Run time dependencies between components
4. Deployment structure (In-process libraries, Out-Process containers)

# Data Model and management
LLD:
1. Logical data model - Entities and relationships -----> Physical data model (based on Storage Engin (MySQL, MongoDB)
2. Key write and read queries (load, optimization for low latency)
3. Identify indexes
4. Data scalability (Sharding ? Replication? ) Replication strategy? Sharding key?
5. Data volumne growth over time, Increase in transactions and customers
5. Key transactions - How transaction boundry is minimized?
6. Security of data (encryption?) In rest, In Transit.
7. Data purging strategy? Lifecycle of data (hot -> code -> archived -> deletion)


# Security
1. Authentication
2. Authorization, roles required for each end points
3. Priciple of least priviledge.
4. How credentials and secrets are stored, maintained and roteted.


# Performance and Scalibility
1. How to achieve latency and throughput requirement. (Caching, replication, optimized data and storage model)
2. In case of high load - how system would behave?
Details:
Will be there increased latency?
Or Request drops?
Or scale elastically?
3. How system communicate with external depedencies (Sync-blocking? Aysnc-Non-blocking?)
4. Resource bottlenecks (memory, CPU, Disk I/O, network I/O) - And It can be scaled?

# Reliability and Fault-tolerance
1. How system will reponsed to Unavailable and Slow downstream depedencies?
Details:
Will there be timeouts?
Or Retry?
Or Circuit breakers?
Or fallbacks?
Or Idempotancy of request?
2. Key failure scenarios and how to handle them? What are consequences if not handled?
3. How load gets distributed amoung active nodes?
4. How to deal with nodes crashes?



# Configurability and Extensibilty
1. Identify the key functional logic which will change over time, deployment.
2. Define how that logic can be encapsulated and plugged-in for future extensibility perspective
3. Can the existing logic be parameterized to solve a much general problem?
4. Define plugin interfaces and core code logic which resolves the plugin and delegates to plugin to achieve extensibility

# Observability
1. Define how the health point is to be implemented realistically.
2. Define the health metric which will be collected and reported.
3. Define key application, key business and performance metrics. (Queue performance, response codes, key transactions etc)
4. How does the system enable diagnostic in a distributed deployment models with dependencies.


# List Architecture Decision Records(ADR)
http://thinkrelevance.com/blog/2011/11/15/documenting-architecture-decisions








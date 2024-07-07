# Grafysi integration
The Grafysi integration system provides functionalities to extract medical records 
and physiology signal data directly from hospital information system or 
from a preprocessed dataset such as MIMIC-III. The extracted data can then be 
processed, partitioned and loaded into Neo4j or Wdb cluster, according 
to data type, in form of Grafysi structured data models for advanced analytics.

Following are grounding features of the system:

1. Ensure health data privacy and security by strictly excluding any sensitive
data from transferring to the outside world (e.g. outside hospital information system)
and provide options to add intermediate pipelines (in form of plugins) to further
filter and process subtle aspects before loading to Grafysi model.
2. ETL types: unlimited-size batch ETL and real-time ETL.
3. Support data partitioning for large-scale graph analytics.
4. Allow hybrid Grafysi data instances hosting or even self-hosted Grafysi data instances for 
ultimate security with the least operational cost by allowing some self-hosted components being managed
by remote controllers.

Below is components diagram of the Grafysi integration system:


```plantuml
@startuml
frame "Hospital IS" {
  database "Medical records"
  database "Physiology signals"
  component "Integration client"
  [Medical records]<--[Integration client]: fetch
  [Physiology signals]<--[Integration client]: fetch
}

frame "Integration management" {
  component "Dataset manager"
  component "Integration controller"
  database "Dataset metadata"
  database "ETL metadata"
  [Dataset manager]--[Dataset metadata]
  [Integration controller]--[ETL metadata]
  [Dataset manager]<.[Integration controller]
}

frame "ETL middle pipe" {
  component "Kafka buffer"
  component "ETL handler"
  [Kafka buffer]->[ETL handler]
}

frame "Neo4j cluster" {
  component "Graph writer"
  component "Neo4j instance"
  [Graph writer]->[Neo4j instance]
}

frame "Wdb cluster" {
  component "Wdb writer"
  component "Wdb instance"
  [Wdb writer]->[Wdb instance]
}

frame "Resource management" {
  component "Resource manager"
  database "Resource metadata"
  [Resource manager]-[Resource metadata]
}

frame "Resource allocation" {
  component "Resource allocator"
}

frame "Authentication system" {
  component "Auth manager"
  database "Auth data"
  [Auth manager]-[Auth data]
}

[Integration client].>[Auth manager]: authenticate
[Integration client]..>[Dataset manager]: register_ds
[Integration client]..>[Integration controller]: init_etl
[Integration client]-r->[Kafka buffer]: push
[Integration client]..>[Resource manager]: reg_resource

[Integration controller]<.>[ETL handler]: init/log_etl_pipe
[Integration controller].>[Resource manager]: fetch_res_info

[ETL handler]-->[Graph writer]
[ETL handler]-->[Wdb writer]

[Resource manager]..>[Resource allocator]

[Resource allocator]..>[ETL middle pipe]: ensure_res
[Resource allocator]..>[Wdb cluster]: ensure_res
[Resource allocator]..>[Neo4j cluster]: ensure_res
@enduml
```
In next sections, we will analyse the system architecture around the 4 grounding features.




































































































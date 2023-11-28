# WEb3auth
web3auth infrastructure
Web3Auth Wallet Management Infrastructure 


Web3Auth's Wallet Infrastructure is designed to make managing 

Architecture of a Node
t of n Distributed Model for Security and Uptime​

These nodes operate in a t of n security model, a user's factor (share) is further split into sub-shares and secured individually by each of these nodes. Threshold(t) number of nodes are able to sign signatures for users.

In order to compromise this setup, one must compromise a total t of n setups. Similarly in order for the services to go down, n-t setups must go down (i.e. 2 nodes in a 3/5 setup). This is on a technical level very unlikley.
Regionally available services​

Signatures and login services are often expected to be low-latency sub 1.5s interactions from the user. To achieve that level of latency, each node operates clusters of instances in regions across the world. This includes operations in US-east, west, Singapore, South America, Africa, Europe east and west and ultimately is flexible.
Horizonally scalable for billions​

In each node, in each regional cluster, there runs an orchestration layer that operates multiple services. Services are spun up and down in different regions to cater to the load necessary for global applications.
These clusters is orchistracted via a master coordinator that communicates with different nodes to understand the load that they should be receiving and coordinate on a distributed level.



here are my guessing about your  Web3auth underlying infrastructure

1/  AWS solution 

+-----------------------------------------------------------+
|                      Web3Auth Node                        |
|  Regionally Distributed (EKS on AWS Fargate/ECS)          |
+-----------------------------------------------------------+
|                                                           |
|  +--------------------+         +----------------------+  |
|  |    Coordinator     |         | External Load       |  |
|  |                    |         | Balancer/Router     |  |
|  | - AWS Step         |         | (AWS Global         |  |
|  |   Functions        |         | Accelerator &       |  |
|  | - AWS Lambda       | <-----> | ELB/ALB)            |  |
|  | -  AWS           |         +----------------------+  |
|  |   DynamoDB         |                                   |
|  |   (for coordination|                                   |
|  |   state)           |                                   |
|  +--------------------+                                   |
|  |                    |                                   |
|  | -  AWS Aurora    |                                   |
|  |   Global Database  |                                   |
|  |   (Master GlobalDB)|                                   |
|  +--------------------+                                   |
|                                                           |
|  +------------------------------------+                   |
|  |         Services Cluster           |                   |
|  |                                    |                   |
|  | - Key Generation Service           |                   |
|  |   (AWS Lambda + KMS)               |                   |
|  |                                    |                   |
|  | - Session Service                  |                   |
|  |   (AWS Lambda + DynamoDB)          |                   |
|  |                                    |                   |
|  | - TSS Service                      |                   |
|  |   (AWS Lambda + KMS)               |                   |
|  |                                    |                   |
|  | - Service D                        |                   |
|  |   (AWS Lambda/ECS Containers)      |                   |
|  |                                    |                   |
|  | - Service F                        |                   |
|  |   (AWS Lambda/ECS Containers)      |                   |
|  +------------------------------------+                   |
|                                                           |
+-----------------------------------------------------------+



Explanation of AWS services used:

AWS Fargate/ECS: To run the containerized applications without having to manage servers or clusters. It integrates with EKS (Elastic Kubernetes Service) for orchestration.

AWS Lambda: For running the stateless components such as Key Generation Service, Session Service, and TSS Service. These are triggered based on events and can scale automatically.
AWS Step Functions: To coordinate the workflows between various services, especially for the Coordinator service which manages cross-node operations.


 AWS Aurora Global Database: To serve as the Master GlobalDB, offering multi-region deployments, cross-region replication, and high-performance database capabilities.

AWS Global Accelerator & Elastic Load Balancing (ELB/ALB): For distributing incoming application traffic across multiple targets in different Availability Zones, improving the global application availability and performance.


 AWS DynamoDB: For managing the state within the Coordinator service, providing a fast and flexible NoSQL database service.

( optional or not needed since you are using multi sharding key method  )AWS Key Management Service (KMS): To handle cryptographic keys required for Key Generation Service and TSS Service, ensuring secure key management.
Each of these AWS services has been chosen to match the function and requirements of the corresponding component in the Web3Auth node architecture, aiming to maximize performance, scalability, and resiliency.



2 / GCP architecture woth Web3authnode 


+------------------------------------------------------------------+
|                           Web3Auth Node                          |
|   Regionally Distributed (GKE with Autopilot/Cloud Run)          |
+------------------------------------------------------------------+
|                                                                  |
|  +-----------------------+         +---------------------------+ |
|  |     Coordinator       |         | External Load             | |
|  |                       |         | Balancer/Router           | |
|  | - Cloud Functions     |         | (Cloud Load Balancing &   | |
|  |   (for cross-node     |         |  Cloud CDN)               | |
|  |   coordination        | <-----> |                           | |
|  |   logic)              |         +---------------------------+ |
|  |                       |                                        |
|  | - Firestore/Datastore |                                        |
|  |   (for coordination   |                                        |
|  |   state)              |                                        |
|  +-----------------------+                                        |
|  |                       |                                        |
|  | - Cloud Spanner       |                                        |
|  |   (Master GlobalDB)   |                                        |
|  +-----------------------+                                        |
|                                                                  |
|  +-----------------------------------------+                     |
|  |             Services Cluster            |                     |
|  |                                         |                     |
|  | - Key Generation Service                |                     |
|  |   (Cloud Functions + Cloud KMS)         |                     |
|  |                                         |                     |
|  | - Session Service                       |                     |
|  |   (Cloud Functions + Firestore/Datastore)|                    |
|  |                                         |                     |
|  | - TSS Service                           |                     |
|  |   (Cloud Functions + Cloud KMS)         |                     |
|  |                                         |                     |
|  | - Service D                             |                     |
|  |   (Cloud Functions/Cloud Run)           |                     |
|  |                                         |                     |
|  | - Service F                             |                     |
|  |   (Cloud Functions/Cloud Run)           |                     |
|  +-----------------------------------------+                     |
|                                                                  |
+------------------------------------------------------------------+

Explanation of GCP services used:

Google Kubernetes Engine (GKE) with Autopilot or Cloud Run: For running the containerized applications with fully managed serverless offerings, providing automated scaling, networking, and security settings.
Cloud Functions: For running the stateless components and coordinator logic. They are event-driven and scale automatically with the number of requests.

Cloud Spanner: A fully managed, mission-critical, relational database service that offers transactional consistency at global scale, schemaless storage, and automatic, synchronous replication for high availability.

Cloud Load Balancing & Cloud CDN: To distribute incoming application traffic across multiple instances globally and to cache content closer to users, respectively.

Firestore/Datastore: For managing the state within the Coordinator service and other services needing a NoSQL database.

Cloud KMS ( optional or not needed since you are using multi sharding key method  ): For managing cryptographic keys required for services like Key Generation Service and TSS Service.

This GCP setup mirrors the functionality described in the AWS setup, providing a scalable, secure, and resilient environment suitable for handling the complexities of blockchain-based authentication systems with Web3Auth.




Cover letter 
 

Hello World changer  ! 

How i can serve you service your customers to serve this World better ?  

That s the question we should all ask ourselves ....
 
who Am i in a few words  words ?  a passionate Techie , trouble shooter and creative mind :  Devops engineer, SRE cloud architect ,future  Blockchain dev and  machine learning Engineer . a great  Learner , trainer , friendly team mate fun to work with. 

ps i m  French and vietnamese citzen , currently based in vietnam but might be moving back to France in 2024 

what can i do : architect , migrate , optimize your Apps , your workflow , your infra , your operations cost,  the services to your customers   
  

stacks :
coding :  python javascript , bash , powershell
SVN : github gitlab 
CICD pipelines :  jenkinbs gitla action gitlab ci 
IAC : aws Cloud formation , terraform , ansible , puppet
Containers orchestrations : docker swarm ,  kubernetes 
AWS : ecs eks fargate ,
DB :  SQL  , no SQL  mondo db , S3

Devops Blockchain skills : create and optimise infra and devops workflow for Smart contract, Dapps on  blockchain networks.  
data engineering : kinesis data streaning , firehose , kafka , ETL AWS Glue, aws EMR ,  AWS data lake model , data warehouse redshift ,   
Machine Learning  modeling : AWS Sagemaker , aws rekognition comprehend,  GCP auto ML vertex Studio , Azure ML  



Education and Certifications

- AWS GCP AZure solutions Architect Professional Level
- Azure Solutions Architect  Expert Level
- GCP infrastructure enginneer  
- Blockchain Dev ( currently training to become blockchain dev ) 
- AWS machine learning specialization
- AWS Cloud Practitioner**
-- Certified Expert troubleshooting engineer  https://www.kepner-tregoe.com/aboutkt/ 
- Network Architect:** CCNA, CCNP, CCND
- Telecom Degree:** BTS Telecom, AFPA TELECOM, Paris, 1995-1996
- BD:** El Camino College-Compton Center, Compton, 1986-1990


**Dzoan Nguyen Tran**
*Expert Network & AWS AZure  SRE DevOps Architect

https://github.com/Sherlock2019?tab=repositories

📧 [dzoannguyentran@gmail.com]
📞 +84 908618075 based in  Vietnam until begining 2024 back to Paris France  
🌍 Nationalities: French & Vietnamese

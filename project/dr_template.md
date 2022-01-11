# Infrastructure

## AWS Zones

Main Zone: ``us-east-2``
DR Zone: ``us-west-1``


## Servers and Clusters

### Table 1.1 Summary
| Asset         | Purpose                                                                         | Size        | Qty                                | DR                                                       |
|---------------|---------------------------------------------------------------------------------|-------------|------------------------------------|----------------------------------------------------------|
| EC2 Instances | Application Servers in the main region us-east-2                                | t3.micro    | 3                                  | Deployed in us-east-2, Replicated in DR Region us-east-1 |
| EKS Cluster   | Kubernetes cluster used for Application monitoring in the main region us-east-2 | t3.medium   | 1- control plane, 2 - Worker Nodes | Deployed in us-east-2, Replicated in DR Region us-east-1 |
| VPC           | Virtual Private Cloud for subnets in AZs of the region                          |             |                                    | Deployed in us-east-2                                    |
| Load Balancer | A load Balancer to application traffic to multiple EC2 instances in AZs         |             | 1                                  | Deployed in us-east-2                                    |
| RDS Database  | DataBase in main region us-east-2 with geo replication to DR Region us-west-1   | db.t2.small | 1 cluster, 2 DB instances          | Deployed in us-east-2, Replicated in DR Region us-east-1 |
| EC2 Instances | Application Servers in the DR region us-west-1                                  | t3.micro    | 3                                  | Part of DR plan                                          |
| EKS Cluster   | Kubernetes cluster used for Application monitoring in the DRregion us-west-1    | t3.medium   | 1- control plane, 2 - Worker Nodes | Part of DR plan                                          |
| VPC           | Virtual Private Cloud for subnets in AZs of the region                          |             |                                    | Part of DR plan                                          |
| Load Balancer | A load Balancer to application traffic to multiple EC2 instances in AZs         |             | 1                                  | Part of DR plan                                          |
| RDS Database  | DataBase in DR region us-east-2 with geo replication from main Region us-east-2 | db.t2.small | 1 cluster, 2 DB instances          | Part of DR plan                                          |


### Descriptions
More detailed descriptions of each asset identified above.

EC2 Instances: Each EC2 Instance is a Unix Server hosting our flask Application. Each region has 3 EC2 instances, this increases the availability of the app to users.

EKS Cluster: This k8s Cluster has prometheus stack installed on it. Using prometheus we will get some idea of our flask app performance. The cluster contains two Worker Nodes to make it highly available.

VPC: Amazon VPC is the networking layer for Amazon EC2, it enables us to launch AWS resources into a virtual network that you've defined. Every region has a default VPC created for us by AWS.

Load Balancer: Elastic load balancer is used to distribute load aong our 3 application servers in each region. it increases availability and performance of our application during peak traffic time.

RDS Database: Two clusters, one in the main region us-east-2, and one in the DR region us-west-1 which is a replica for the main database cluster. Both have retention policy of five days, and each cluster consist of two nodes, a writer and reader node. Each node is deployed in a different AZ in the region. It helps in fast recovery in the event of a disaster but a bit costly as we have 2 clusters runniing.

## DR Plan
### Pre-Steps:
List steps you would perform to setup the infrastructure in the other region. It doesn't have to be super detailed, but high-level should suffice.

- Ensure the infrastructure is set up and working in the Primary Site i.e., us-east-2 in our case
- Ensure the infrastructure is set up and working in the DR site i.e., us-west-1 in our case.
- Perform Periodic fail-over to our DR Infra and fail-backs to our Primary Infra, this hellps us in identifying Configuration drift and fix it.


## Steps:
You won't actually perform these steps, but write out what you would do to "fail-over" your application and database cluster to the other region. Think about all the pieces that were setup and how you would use those in the other region

- Create a cloud load balancer and point DNS to the load balancer. This way you can have multiple instances behind 1 IP in a region. During a failover scenario, you would fail over the single DNS entry at your DNS provider to point to the DR site. This is much more intelligent than pointing to a single instance of a web server.

- Have a replicated database and perform a failover on the database. This is automated in our case here.

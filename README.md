## Stage 02 - First steps with amazon ECS

After finishing stage 02 the two mysql database containers are running an amazon AWS, while the rest of the setup still runs locally. The containers are configured via an ECS (EC2 container service) task definition that automatically starts an instance when configuring a cluster and service for the task definition within the ECS console in AWS. The container image used for the databases is the standard mysql image from Docker-Hub (wich is available in ECS).

The link to the database containers in the cloud is configured via the config-server - the databases are not part of docker-compose in this stage.

### Stage 01 - Creating Auto-Scaled and Loadbalanced App in Amazon Web Services 

#### Goal
Deploying an app on two EC2 instances which are load balanced.

#### Setup

1. Amazon AWS account is required

#### Tasks

TODO

### Stage 02 - First steps with Amazon ECS

#### Goal
Instead of running the mysql databases (customerdb and accountingdb) in local docker-containers you run docker containers in the AWS cloud using amazon ECS (EC2 Container Service).

#### Setup

1. Amazon AWS account is required.

#### Tasks

1. Create a Task Definition within the amazon ECS environment that defines the mysql containers.
2. Define a cluster and service that use the task definition.
3. Get the IP address of the instance where your containers run.
4. Configure your local setup to use the containerized mysql databases from the cloud instance.

Note: ECS already uses the Docker-Hub registry, so you don't need to upload any containers to the AWS container registry in this step! The "mysql" container you pull locally from Docker-Hub also works within the Task-Definition of ECS.

Note: This setup goes against the idea of docker-compose because the mysql containers are not managed by docker-compose. You will have to configure the databases on the config-server rather then through docker-compose. You can remove the database container entries from docker-compose for this stage because you don't need them.


### Stage 03 - Docker-Hub (container repository)

#### Goal
Instead of creating the containers locally you push them to Docker-Hub so that they are publicly available. For the local setup the containers are then pulled from the Docker-Hub repository.

#### Setup

1. Docker-Hub account is required.

#### Tasks

1. Decide which containers you can just reuse from Docker-Hub because they are standard containers and which containers are "self built" so you have to push to repositories in your Docker-Hub account. 
2. Create a repository for each self-built container that will be pushed.
3. Push the self build containers to their respective repositories on docker-hub.
4. Adapt the docker-compose configuration so you use the docker-hub containers and not the local Dockerfiles.

Note: The self built containers are those containers that have a "context" defined in docker-compose. They are built and run using Dockerfiles.

### Stage 03.A - Using the Amazon ECR (EC2 Container Registry)

#### Goal
You understand how the AWS container registry works as an alternative to Docker-Hub. Your are able to upload docker containers to an AWS container repository.  

#### Setup

1. Amazon AWS account is required.
2. The [AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/installing.html) is required on your local machine.
3. Optionally you can [create an IAM user](http://docs.aws.amazon.com/AmazonECR/latest/userguide/get-set-up-for-amazon-ecr.html), if you operate within your own AWS account. If you create an IAM user you will have to [configure the CLI for that IAM user](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html).

#### Tasks

The ECR is an alternative to Docker-Hub. You can upload your container-images there so they are available in the AWS cloud.

1. Follow the instructions from the [ECR documentation on how to upload docker container images](http://docs.aws.amazon.com/AmazonECR/latest/userguide/ECR_GetStarted.html) and upload one of your container-images to the ECR.
2. Alter the task definition and use the container you uploaded to the amazon ECR instead of the Docker-Hub container-image.


### Stage 04 - Amazon ECS-CLI compose

#### Goal
You automatically generate a task-definition on amazon ECS out of your local docker-compose setup using the CLI. You use this task-definition to run containers in a new ECS cluster and service without any further adaption.

#### Setup

1. It is required that all the containers that the application consist of are available in the cloud (Docker-Hub or ECR).
2. The [ECS CLI](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_CLI_installation.html) is required on your local machine.
3. You will have to [configure the ECS CLI](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_CLI_Configuration.html) similar as to configuring the AWS CLI; for that purpose you might want to [create an IAM user](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) if you are operating within your private AWS account.

#### Tasks

1. Adapt the docker-compose file so it is compatible to the [ecl-cli compose features](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/cmd-ecs-cli-compose.html) (only certain options are available).
2. Generate a task definition through [ecl-cli compose](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/cmd-ecs-cli-compose.html).
3. Add the generated task-definition (in your AWS account) to a new service within a new cluster and run the cluster.
4. Hope that the magic works :)

Note: Most likely it will not work just like that: Think about what could be the problem - if you can't solve it you can take a look at the [hints for stage 10](https://github.com/senacor/MicroservicesAndCloud-Tutorials/tree/master/hints/stage-10).

### Stage 04.A - Architecture Considerations

#### Goal
You reach a stage where you can discuss the architecture of your application running in the cloud. You understand the basic principles of load balancing at an application level, database clusters/replication techniques and deployment strategies for microservices.  

#### Tasks

1. Read the [AWS introduction to elastic load balancing](http://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/how-elastic-load-balancing-works.html) and [service load balancing](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-load-balancing.html) and reflect on it.
2. Think about instances and containers - Which services will have to scale-up? Which services don't have to scale-up? What would be a good setup when it comes to scalability and fault-tolerance?
3. Read the [mysql introduction to data replication](https://dev.mysql.com/doc/refman/5.7/en/replication.html) and reflect on it.
4. Think about your data - How can you keep the data persistent between service instances when doing load balancing at application level?
5. Read about [microservices architectures](http://microservices.io/patterns/microservices.html) and dive into deployment patterns like [Single Service Instance per Host](http://microservices.io/patterns/deployment/single-service-per-host.html) and [Multiple Service Instances per Host](http://microservices.io/patterns/deployment/multiple-services-per-host.html) - reflect on them. 
6. Think about our current microservices architecture and out deployment setup - What could be done different and what would be the advantages and disadvantages? How would you apply load balancing?


### Stage 05 - MySQL database with amazon RDS

#### Goal
Instead of providing your own database container you manage your database through amazon RDS (Relational Database Service). 

#### Tasks

1. Think about the security of your application - what should be accessible and how? Read into amazon [VPC (Virtual Private Cloud)](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-vpc.html).
2. Launch MySQL instances managed by RDS for the databases of your services.
3. Make sure that your databases are not publicly accessible.
4. Remove the databases from the ECS task definition (by altering the docker-compose configuration and generating a new task definition).
5. Configure the database connection for your services through the config server.
6. Create an ECS cluster and service and configure it to use the databases.

### Stage 06 - Load Balancing on amazon AWS - Single-Service per Host

#### Goal
You adapt your setup to a ["single service instance per host"](http://microservices.io/patterns/deployment/single-service-per-host.html) setup. You add an elastic load balancer to that on AWS. You understand application level load balancing and discuss the advantages and disadvantages of this setup.

#### Tasks
 
1. Read into [Elastic Load Balancing](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-load-balancing.html) on amazon AWS.
2. Change your deployment to a ["single service instance per host"](http://microservices.io/patterns/deployment/single-service-per-host.html) setup. The databases should still be managed by RDS.
3. Create and configure an [Application Load Balancer](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html) in your EC2 console (using the default VPC).
4. Startup the config and registry service only once (without load balancing). The customer and accounting service should be load-balanced.


### Stage 07 - Load Balancing and Route 53

#### Goal


#### Tasks

<!---### Stage 13 - Cloud Storage with amazon SimpleDB

#### Goal
Instead of running a database in a docker container you should utilize the simple storage service (S3) of amazon AWS.

#### Tasks

For utilizing SimpleDB: https://aws.amazon.com/articles/Amazon-S3/7417221025670024--->
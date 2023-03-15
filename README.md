# RDS Aurora Postgres Deployment

This README file will explain the technology stack used to deploy each component requested in this assignment. 


I have selected to deploy a master-slave PosrgreSQL scenario, using Terraform (IaC). 

I have used Terraform to deploy an PostgreSQL cluster on Amazon Web Services (AWS), specifically using the RDS service. 

##### Requirements 
Note: Deployments were made using macOS 

- AWS Account -> https://aws.amazon.com/console/ (You can sign up following this URL link)
- Terraform installation is required (https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
- Terraform version -> Terraform v0.14.0
- Have used `tfenv` to switch to v0.14.0 as my local machine was using a different version for the code I wrote. 
- If needed: to install `tfenv`, `homebrew` should be installed first -> `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
- Install `tfenv` -> `brew install tfenv`

##### Pre-Deployment Tasks

- `cd ` into the `rds` directory of the project 
- set the db password in your terminal-> `export TF_VAR_db_password="<password>"`
- Initialise Terraform after confirming that you are running the required version -> `terraform init`
- If asked to upgrade for new modules run `terraform init -upgrade`

**WARNING** -> Passwords and usernames of the DB should be encrypted in a real-life scenario. 

##### File Structure
##### main.tf

`main.tf` -> Consists of the main configuration that Terraform is reading to deploy a primary and a secondary cluster in RDS. This file also contains that AWS credentials of my personal account. **Please NOTE** that adding AWS credentials for terraform to pull into, is not standard practice and it would not be my choice in a real-life/production scenario. These credentials can either be kept locally and get Terraform to pull from there into order to access the AWS account (possible using a tf wrapper bash configuration). The credentials have only been added in the `main.tf` file due to time sensitivity and more efficiency for the purpose of this assignment. 

The `main.tf` constists of VPC and Security Groups for PostgreSQL. These can also be added in a separate file structure in this project and can be applied with Terraform after the DB cluster is deployed. Instead, I have decided to deploy them in the same file, for the same justification mentioned above. 

##### variables.tf

In this file, I have variabilized:
- AWS region for where the deployment is going to take place 
- The database password, which will be added as a variable module in the `main.tf` file. Please note that the password will be asked to be prompted when terraform executes its apply 

##### outputs.tf
Values for: 
- RDS hostname
- RDS instance port
- RDS username  

##### versions.tf
- Where the aws provider version is specified. 

##### Post-Deployment Tasks (Setting up postgres)

- Install `psql` cli tool with homebrew -> `brew install postgresql`
- Go to the AWS Console to note down the Primary Instance's endpoint. We only need to configure the Primary node, as the secondary cluster replicates as the reader as shown in the screenshot below: 

Add screenshots here 

- Once you have the endpoint run the following commands to acces the cluster and create a new user for the developers and a new datavases with priviliges: 

Login as postgres db first -> `psql --host nameofthecluster.cweq0xk7slch.us-east-1.rds.amazonaws.com --port=5432 --username wrkbl --dbname=postgres `

```
postgres=> create user wrkbldev with encrypted password 'dev';
CREATE ROLE
postgres=> create database devapps
postgres-> createuser wrkbldev with encrypted password 'dev';^C
postgres=> create database devapps;
CREATE DATABASE
postgres=> grant all privileges on database devapps to wrkbldev;
GRANT
```
- Once finished provide the developer with the endpoints for both master & slave with the credentials. 


## License

[MIT](https://choosealicense.com/licenses/mit/)

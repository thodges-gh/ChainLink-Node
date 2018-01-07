# ChainLink on AWS guide

Configuring the ChainLink node through this guide on AWS involves setting up each piece of the node as its own instance: the Ethereum node, the database, and the ChainLink core software. Splitting each role does not require AWS, but AWS makes the database management easier with Amazon RDS. Otherwise, there is no difference if you were to use another VPS provider.

For these instructions, I give examples for names when you would encounter an input text field, but they may be changed to your liking.

## Ethereum node

* Click Launch Instance from the EC2 Dashboard
* Choose the Ubuntu image
* t2.micro
* Security Group (make sure 172.31.X.X is valid for your private IPs, change if necessary)
  * Add rule: Custom TCP Rule
  * Port: 8545
  * Source: Custom, 172.31.0.0/16
* Review and Launch
* Launch
* Create a new key pair: gethnode
* Download Key Pair
* Launch Instances
* Connect to the instance
* Follow Geth Node installation instructions

## Database instance

A significant advantage of using AWS for ChainLink is that very little configuration is necessary in order to configure PostgreSQL. Instead of having to modify routes and setting up roles, we can simply create a basic instance without a database and initialize it from the ChainLink core software.

* Go to Security Groups
* Create Security Group
  * Security group name: DB Connection
  * Description: Used to connect to database
  * VPC: Leave as default
  * Add rule under Inbound
    * Custom TCP Rule
    * Port: 5432
    * Source: Custom, 172.31.0.0/16
    * Description: Postgres port
* Select RDS from the dashboard
* Launch a DB instance
* Choose PostgreSQL (Check to only enable options for free tier)
* Leave everything under Instance specifications as their default
* Enter required info for the Settings:
  * DB instance identifier: `CL_DB_INSTANCE`
  * Master username: `CL_DB_USER`
  * Master password: `CL_DB_PASSWORD`
* Choose "No" for public accessibility
* Select existing VPC security group
  * Choose the DB Connection (VPC) security group
* Don't give a database name and leave default port
* Everything else default
* Launch DB Instance

## ChainLink Node

Currently (before the main network has launched), this can be ran with Amazon's free-tier instance. However, as external adapters are added (not covered in this guide), resources are expected to increase.

* Click Launch Instance from the EC2 Dashboard
* Choose the first image (Amazon Linux AMI)
* t2.micro
* Review and Launch
* Launch
* Create a new key pair: chainlinknode
* Download Key Pair
* Launch Instances
* Connect to the instance
* Follow ChainLink node installation instructions
  * The DATABASE_URL line will need to be in the following format:
  * `DATABASE_URL=postgresql://CL_DB_USER:CL_DB_PASSWORD@full.path.to.endpoint:5432/nayru_development?encoding=utf8&pool=5&timeout=5000`

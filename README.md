# Public Cloud Migration Case

## Table of Contents

- [Introduction](#Introduction)
  - [Scenario](#Scenario)
  - [Architecture](#Architecture)
- [Requirements](#Requirements)
- [Outputs](#Outputs)
- [Solutions](#Solutions)
  - [Diagram](#Diagram)
  - [Documentation](#Documentation)
  - [IaC](#IaC)
- [Contributor](#contributor)


# Introduction

This repo is a migration case study from on-premise to Amazon Web Services. You will see a set of solutions about the Business Requirements have been shaped by client.

# Scenario

The customer, you are working with, asks you to create a pilot for one of the company's applications in public
cloud. This is their first attempt to getting to know with non-on-prem environments so your responsibility is not only
delivering the solution but to present and highlight core concepts and benefits of the designed architecture. You can
choose any of major public cloud platforms; AWS, Azure or GCP, for the project.

# Current Architecture

<div class="sl-block is-focused" data-block-type="image" style="width: 806.4px; height: 265.853px; left: 73.6px; top: 399.073px; min-width: 1px; min-height: 1px;" data-origin-id="cdcaf465f82a9278da2789c9fe471672"><div class="sl-block-content" style="z-index: 12;"><img data-natural-width="1277" data-natural-height="421" style="" data-lazy-loaded="" src="https://s3.amazonaws.com/media-p.slid.es/uploads/913733/images/7027811/Screenshot_2020-02-04_at_14.35.00.png"></div></div>

Notejam is a web application which allows user to sign up/in/out and create/view/edit/delete notes. Notes can be
grouped in pads. Application is designed to be easy to build and run locally. Customer does recognize this may not be
the optimal architecture to meet the business requirements (see below). You are welcome to recommend (and
implement if time permits) changes to make it work better on target platform.

Notejam is currently build as monolith containing built-in webserver and SQLite database. Your task is to redesign the
architecture so it can meet business requirements on chosen public cloud platform.

Customer has provided you the GIT repo https://github.com/nordcloud/notejam with;

1. Documentation how to build and run application locally
2. Application source code
3. Unit tests for testing the application

NOTE: There are multiple implementations of Notejam application in different languages and frameworks. Pick the one
you are most comfortable with. All versions have identical functionality.

# Requirements

1. The Application must serve variable amount of traffic. Most users are active during business hours. During big events and conferences the traffic could be 4 times more than typical.
2. The Customer takes guarantee to preserve your notes up to 3 years and recover it if needed.
3. The Customer ensures continuity in service in case of datacenter failures.
The Service must be capable of being migrated to any regions supported by the cloud provider in case of emergency.
4. The Customer is planning to have more than 100 developers to work in this project who want to roll out multiple deployments a day without interruption / downtime.
5. The Customer wants to provision separated environments to support their development process for development, testing, production in the near future.
6. The Customer wants to see relevant metrics and logs from the infrastructure for quality assurance and security
purposes.

# Outputs

The Customer wants you to present your solution and demonstrate the benefits supported by proper documentation.
provide all the source code produced in the creation of the infrastructure in an organized way.


# Solutions

The solutions is going to be provided in three different categories;

1. Architectural Diagram 
2. Documentation
3. IaC Template on AWS CloudFormation

# Diagram 

This diagram has been designed as a solution to each of business problems below.

<div class="sl-block is-focused" data-block-type="image" style="width: 943.143px; height: 664.631px; left: 8.4285px; top: 17.6845px;" data-origin-id="27905459806bf7519733b319fa9ef0d3"><div class="sl-block-content" style="z-index: 10;"><img src="https://s3.amazonaws.com/media-p.slid.es/uploads/913733/images/7045662/twoTierWebApp.jpg" style="" data-natural-width="1639" data-natural-height="1155"></div></div>

# Documentation

The current architecture can be described as a classic monolith application lying behind a firewall, which denies all requests by default from internet but allows only specific type of requests such as HTTP, HTTPS or SSH via different ports after the configurations has been applied. At the first glance, it is also clear to identify this architecture as a two-tier web application, which is consist of a web-server and database-server. 

When I analyze the business requirements and think about the available architecture, a number of changes would be implemented to make architecture high available, scalable and secure. It can be said that these requirements would be done with a migration the current architecture to Amazon Web Services.

In the next section, I will investigate the business problems one by one, and provide the solution I designed on the architectural diagram on section 1.

- VPC Architecture:

As you see on (#Diagram), To create a secure, available and scalable architecture, the application deployed to three different avability zones. Each of web and database servers are installed in different subnets. The connection among subnets were provided VPC Router. In order to download software packages and versions from internet to private subnets NAT GW and Egress-only Internet Gateway attached to the VPC. For public subnets, an Internet Gatewat attached to the VPC and Security Groups (Firewalls) allowed the preffered requests from Internet.

<div class="sl-block is-focused" data-block-type="image" style="width: 806.4px; height: 348.255px; left: 76.8px; top: 175.873px;" data-origin-id="136cc8894d93f066c0d8f059888041e9"><div class="sl-block-content" style="z-index: 10;"><img src="https://s3.amazonaws.com/media-p.slid.es/uploads/913733/images/7045665/vpc.jpg" style="" data-natural-width="998" data-natural-height="431"></div></div>

Requirement 1. The Application must serve variable amount of traffic. Most users are active during business hours. During big events and conferences the traffic could be 4 times more than typical. 

- Solution 1. The solution to meet this requirement is to choose a type of EC2 Instances on the web-server part of architecture that can carry more traffic than the application need at the beginning stage of migration in order to prevent possible server outrage or down. After testing the available CPU usage on the EC2 Instances, the IT team would gradually decrase the type of instance from larger ones to relatively smaller ones. By that way, it would be possible to create a cost-optimised solution. But, this solutions should be the best practise to be able to meet the demand of getting 4 times more traffice during events and conferences. The best solution for this requirement is to use an Auto-Scaling Group with Scheduled EC2 Instances and a well-designed Auto Termination Policy, which could be configured before big events to be able to serve to huge amount of traffic during the events, and then, with the help of lifetime termination policy the auto-scaling group can terminate the instance has been launced before event and create cost-optimised architecture. 

Requirement 2. The Customer takes guarantee to preserve your notes up to 3 years and recover it if needed.

- Solution 2. As I investigate the available database on the current architecture, I can see SQLite database system and see SQL queries inside application's sourcecode. But, On AWS, there is no managed RDS (Relational Database System such SQLite). Due to that reason, the app's database needs to be migrated to the closet compitable database engine, which is MySQL. For the migration, DMS (Database Migration Service) and Schema Converting Tool is provided by AWS could be used. The aim should be using a managed database service from AWS due to its superior backup, scability, avability and etc... properties or features. In order to store, the notes for three years and recover them when it is needed could be designed by copying or backing up the EBS volumes' Snaphosts attached to AWS RDS. These EBS Snaphots can be stored in an S3 Bucket with a LifeCycle Management Policy and it would be cross-replicated to guarantee the fault tolerancy in another region. There are two specific type of S3 services for infrequent access and archiving purposes. These services are quite cheap the store data and prevent the data loss. These services are named as S3 Glacier and S3 Glacier Deep Archive. The only difference between these services are the retrival time when yopu need to recover or reach the data you stored in. 

Requirement 3. The Customer ensures continuity in service in case of datacenter failures. The Service must be capable of being migrated to any regions supported by the cloud provider in case of emergency.

- Solution 3. As we might know, data-center failures are called as incidents could be steams from literally anything such as; natural disasters, armed-attacks, political restrictions, infrastructure problems, human errors and etc... In every data center, these errors might be occured in anytime. Due to high importance on the IT systems, the systems are being intended to be up and running without any downtime. This concept is called as Avability in computer science. There are many methods to provide high-available (HA) solutions to the customers especially in public cloud. One of the well-known method is to deploy the web- and database- servers in multiple avability zones. Avability zones are physically seperated facilities that includes a set of hardwares and datacenters. Every avability zones are physically located at the different geological areas to prevent possible errors causing from the area. Noone knows when the data center goes down and the avabilitly zones, too. Because of that, The best practise is to deploy the webservers to three different public subnet each subnet is located on a different avability zone and lying down behind an Application Load Balancer with Failover Routing Policy. In the same way, the database servers should be deployed across three avability zones.

- There are many SAAS Solutions for cross-region replication in the case of disaster. It would be a wise choice get EBS Volume Snapshots and copy them to another region and create a CloudWatch Alarms integrated with Route 53 and ELB. Finally, the SAAS solutions agent is start to provision resource in another region based on the EBS Volume Snaphots system backuped. Please have a look to the image below.

<div class="sl-block is-focused" data-block-type="image" style="width: 806.4px; height: 517.171px; left: 76.8px; top: 91.4145px;" data-origin-id="8635cd803c8f68c5473ee824fa0a1722"><div class="sl-block-content" style="z-index: 10;"><img src="https://s3.amazonaws.com/media-p.slid.es/uploads/913733/images/7041400/crossRegionReplication.png" style="" data-natural-width="1500" data-natural-height="962"></div></div>

Requirement 4. The Customer is planning to have more than 100 developers to work in this project who want to roll out multiple deployments a day without interruption / downtime.

- Solution 4. I have found this as the most difficult business requirement, and spent many time about how to provide a realistic solution for deployments and possible rollbacks. As a former software developer, I have experienced that deployements are always the most sensitive part of the operations. It is quite open to see wide range range of problems after completing the deployment. Even some problems or errors cannot be observed by the whole team and could be seen after many deployments or relasing newer versions. The first of this requirement is deployment but i think the harder part is rolling out the available deployments and starting the previous version of app just after the failed deployment. As you know, rollbacks might bring unpredictable downtime to the system. Every part of business could be affected by that serious action with downtime, data loss and operation failure. As a solutions architect and former developer I think that this requirement can be solved by only if you know the right amount of the application size, deployment time, roll-out time, possible errors, testing, and many more parameters... But, I have an idea and I drawed my CI/CD pipeline idea with Blue/Green Canary deployment on the section 7 or below. 

<div class="sl-block is-focused" data-block-type="image" style="width: 352px; height: 302px; left: 304px; top: 199px;" data-origin-id="46f61a42e758503c28f168898161ce31"><div class="sl-block-content" style="z-index: 10;"><img src="https://s3.amazonaws.com/media-p.slid.es/uploads/913733/images/7037257/cicd.jpg" style="" data-natural-width="352" data-natural-height="302"></div></div>

Requirement 5. The Customer wants to provision separated environments to support their development process for development, testing, production in the near future.

- Solution 5. "Infrastructure as code (IaC) is the process of managing and provisioning computer data centers through machine-readable definition files, rather than physical hardware configuration or interactive configuration tools" Wikipedia says. The developers can easily create IaC with AWS CloudFormation. AWS CloudFormation allows you to provision resource without creating them one by one. IaC is quite useful to provison the resource in case of Disaster and launching DEV, PROD and STAGING environmnets. The best way is to use CloudFormation for the requirement.

# IaC

Infrastructure as Code Template has been committed to this Github repo. Please have a look to the CloudFormation Template.

# Contributor

Ömer Mindivanli - Senior Solutions Architect / Capgemini

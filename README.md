# LevelUp! Lab for Deploying HA Website
## Lab Overview And High Level Design

Let's start with the High Level Design.
![Architecture](./images/architecture.jpg)
In this project, we will implement a highly available architecture to host WordPress for a production workload with minimal management responsibilities required from you.

We will use AWS Elastic Beanstalk and Amazon Relational Database Service (RDS). Once you upload the WordPress files, Elastic Beanstalk automatically handles the deployment, from capacity provisioning, load balancing and auto-scaling to application health monitoring. Amazon RDS provides cost-efficient and resizable capacity while managing time-consuming database administration tasks.
## Prerequisites

An AWS Account: You will need an AWS account to begin provisioning resources to host WordPress. Sign up for AWS.
![Account](./images/pic1.png)

Skill level: Prior experience with WordPress is required.

AWS Experience: Intermediate familiarity with AWS and its services is recommended.

### 1. Launch a DB instance in Amazon RDS

Usually, we use the EBS environment to configure the backend databases like MYSQL/POSTGRESQL but here we are going launch to the RDS instance completely independent from the EBS environment. By doing, instances will not be terminated or monitored by EBS.

### Note: Running a DB instance external to Elastic Beanstalk decouples the database from the lifecycle of your environment. This lets you connect to the same database from multiple environments, swap out one database for another, or perform a blue/green deployment without affecting your database.

#### Note: You can either use your default VPC or create your VPC environment and subnets to use.
Steps for RDS instance:

Open RDS console and select database -> choose Standard create and select MYSQL database engine. (Remember we are using WordPress and by default it uses MySQL engine)
![RDS1](./images/pic2.png)
![RDS1](./images/pic3.png)
2. Choose Multi-AZ deployment for HA

3. Under Additional configuration, for Initial database name, type WPdatabase (choose a desired a name). Review your settings for one last time and select create database.
4. After database creation which will take a few mins, go to the security group to grant permission to your Elastic beanstalk environment.
5. Go to the console again and select Databases and choose your database.
6. Under Connectivity, note the Subnets, Security Group(SGs) and the endpoint which you will need later for the EBS environment.
7. Under security, see the SG group associated with the DB instance, choose edit to add a rule, and choose the SG group associated with the EBS environment that contains the auto scaling group. (do this step after you create the EBS environment)

### Download WordPress and launch an EBS env
Download the latest version of WordPress to your local drive and use the zip file to upload it to the EBS environment.

Note: I will directly upload the zip file using the default configuration from the EBS environment. You can download and extract the WordPress file to your EBS environment using AWS CLI.

1. In the EBS console, select environments and select web server environment.

2. Type your application name and upload your downloaded WordPress zip from your local drive. (make sure you select PHP under the platform option)

The next step is we need to add a security group of our DB instance to our running environment. This will reprovision all instances in our environment with an additional security group attached.

1. In the navigation pane, choose the Environment you created and select the configuration option. Choose your instance to edit.

2. Under EC2 security group, choose the security group to attach to the instances in addition to the instance security group that Elastic Beanstalk creates.

3. Choose Apply

You can also do some additional configurations to our RDS instance from EBS environment, like changing RDS_HOSTNAME, RDS_USERNAME or RDS_DB_NAME. Note: I am planning to cover these in the upcoming vlogs.

### Install WordPress
1. Open your Environment from your EBS console and choose the environment URL to open in your browser.
![RDS1](./images/pic4.png)
2. You will be redirected to WordPress installation wizard to configure your site.

3. You will be prompted to configure the database details manually.
Note: This can be automated by editing the wp-config.php file, which we have already uploaded earlier. We can make the source code directly read from your environment’s database connection information. So, step 3 can be avoided by doing so.
4. Enter the database name (RDS database name configured earlier), username, password, and the Database host. The URL of the database endpoint can be received from the RDS console -> Databases -> Connectivity & Security -> Endpoint URL.
![RDS1](./images/pic5.png)


### Configure your Auto Scaling group

Our final step is configuring our environment’s Auto Scaling group with a higher minimum instance count. Always run at least two instances to prevent the web servers in your environment from being a single point of failure. This also allows you to deploy changes without taking your site out of service.

1. Open your EBS console and select your running environment.

2. In the Capacity configuration category, choose Edit.

In the Auto Scaling group section, set Min instances to 2.
Choose APPLY
Finally, we have created a HA production-ready website using WordPress and AWS services.

Room For Growth
I am happy with how this project went. I invested nearly 12 hours in learning and doing hands-on labs to get a clear idea about the topics. Other few things can be configured, such as:

· If you need a high-performance database, consider using Amazon Aurora. Aurora is a MYSQL-compatible one and offers other commercial database features at low cost.

· You can perform blue/green deployment options of the EBS environment for connecting with another database. This will help you to complete the process without affecting the performance of the existing database.

· Finally, if you plan to publish the website, buy a custom domain or use Route 53 for registering and DNS management of your domain. You can use CloudFront for global distribution and Route 53 to create an alias record that points to your CloudFront distribution.

Hope everyone had a great time doing this project and thank you all for taking your time reading this post!









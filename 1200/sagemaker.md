+++
title = "Data Lake Automation CFN"
weight = 1210
+++

### Steps

- Setup Network Configuration	
- Create an IAM role to use with Lake Formation
- Create Glue JDBC connection for RDS
- Lake Formation – Add Administrator and start workflows using Blueprints.
- Explore the Underlying Components of a Blueprint	
- Explore workflow results in Athena	
- [Optional] Grant fine grain access controls to Data Lake user
- [Optional] Verify data permissions using Athena
 
### Setup Network Configuration
![](../1200/images/2.png)

To begin with, we need to perform network configuration. We will open ports specific to RDS PostgreSQL DB, define inbound rules and create S3 endpoint to access data in S3 bucket (created as part of DMS Lab) 

1.	Navigate to RDS dashboard and click on the instance. Select “Connectivity and Security” and look for security group. Click on the security group and and you will be navigated to VPC Dashboard. 

Note: Make sure your RDS instance is available. If the Status says “Storage Full”, increase the DB storage capacity and apply changes immediately to the DB instance and proceed further.
![](../1200/images/3.png)
 

2.	Security group associated with RDS instance will be automatically selected. Select Inbound Rules and Click Edit Rules.
![](../1200/images/4.png) 

3.	Create Inbound Rules as shown below:
    - a.	All TCP with self-reference of security group as its own source.
    - b.	PostgreSQL on 5432 from 0.0.0.0/0
    - c.	Click on Save Rules
![](../1200/images/5.png)
 
4.	Create S3 VPC endpoint. Navigate to VPC Dashboard and select Endpoint from left section. Click on “Create Endpoint”.
![](../1200/images/6.png) 
![](../1200/images/7.png) 
5.	In the next window, follow these steps:
    - a.	Service Category: AWS Services
    - b.	Service name: com.amazonaws.us-east-1.s3
    - c.	VPC: same as VPC for RDS instance
    - d.	Route table: in accordance to subnets
    - e.	Policy: Full Access
    - f.	Click on Create Endpoint
![](../1200/images/8.png)
![](../1200/images/9.png)

Endpoint creation is successful.

### Create an IAM role to use with Lake Formation:
Documentation Reference: Link

Create the IAM Role by following below steps:

1.	On the IAM console, In the navigation pane, choose Roles, and then choose Create role.
![](../1200/images/10.png)
 
2.	On the Create role page, choose AWS service, and choose Glue. Then choose Next:Permissions. 
![](../1200/images/11.png)

3.	Search for and select the AWSGlueServiceRole managed policy. Optionally, provide tags. 
![](../1200/images/12.png)
4.	Then complete the wizard, naming the role LakeFormationWorkflowRole, and choose Create role.
![](../1200/images/13.png)
5.	Back on the Roles page, search for and choose LakeFormationWorkflowRole.
6.	On the role Summary page, under the Permissions tab, choose Add inline policy, and add the following policy in JSON editor. 
![](../1200/images/14.png)
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Lakeformation",
            "Effect": "Allow",
            "Action": [
                 "lakeformation:GetDataAccess",
                 "lakeformation:GrantPermissions"
             ],
            "Resource": "*"
        }
    ]
}
```

7.	Name the policy DatalakeDataAccess and click on Create Policy
![](../1200/images/15.png)
8.	Add another inline policy that allows the role to pass itself by granting the PassRole permission. Name the policy DatalakePassRole.
Important: In the following policy, replace account-id with your AWS account number. Account ID can be found in Account Summary page.
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PassRolePermissions",
            "Effect": "Allow",
            "Action": ["iam:PassRole"],
            "Resource": [
                "arn:aws:iam::account-id:role/LakeFormationWorkflowRole"
            ]
        }
    ]
}
```
9.	Back to policy page, add two more AWS service level permissions: AmazonS3FullAccess and AWSGlueConsoleFullAccess
10.	On the Summary page, verify that there are five policies attached to the role.
![](../1200/images/16.png) 

### Create Glue JDBC connection for RDS 

1.	On the AWS Glue menu, select Connections. 
![](../1200/images/17.png)
2.	Click Add Connection. 
3.	Enter the connection name. This name should be descriptive and easily recognized (e.g ," glue-rds-connection").
4.	Choose **RDS** for connection type and **PostgreSQL** for Database Engine 
5.	Optionally, enter the description. This should also be descriptive and easily recognized and Click **Next**.
![](../1200/images/18.png)

6.	Choose dmslabinstance as Instance and enter password –“master123” and Click Next and Click Finish.
![](../1200/images/19.png)

7.	**glue-rds-connection** was created successfully. To test it, select the connection, and choose **Test connection**. 
![](../1200/images/20.png)
 
8.	To Test connection, choose the IAM role created in the previous step and then click on **Test Connection**.
![](../1200/images/21.png) 

### Lake Formation – Add Administrator and start workflows using Blueprints. 

Navigate to the AWS Lake Formation service.
![](../1200/images/22.png)
 
1.	If you are logging into the lake formation console for the first time then you must add administrators first in order to do that follow Steps 2 and 3. Else skip to Step 4.
2.	Click Add administrators
![](../1200/images/23.png)

3.	Add your <login user> as the Lake Formation Administrator and Click Save
![](../1200/images/24.png)
 
4.	Navigate to Databases on left pane. Select “ticketdata” and click on “Actions”, select “Grant” to grant permissions.
![](../1200/images/25.png)
 
5.	Under “IAM Users and Roles”, select Lake Formation role that you created – “LakeFormationWorkflowRole” and for user, select your username. Grant “super” permissions for Database permissions and Grantable permissions.
![](../1200/images/26.png)

6.	On the left pane navigate to Blueprints click Use blueprints.
![](../1200/images/27.png)
a.	For Blueprint Type, select Database snapshot
b.	Under Import Source
i.	For Database Connection choose the DB connection created in the glue. [Ex: “glue-rds-connection”]
ii.	For Source Data Path enter “sportstickets/dms_sample/player”.
![](../1200/images/28.png) 

c.	Under Import Target
i.	For Target Database, choose existing “ticketdata”.
ii.	For Target storage location choose the S3 bucket - For Bucket name, type the name of the s3 bucket that you created earlier in student lab. 
iii.	Add a folder at the end of the bucket url path.
NOTE: The value is similar to the following string, <StackName>-dmslabs3bucket
-<RandomString> for example “dmslab-student-dmslabs3bucket-q29vwhf8n41w/lakeformation”
iv.	For Data Format choose Parquet
![](../1200/images/29.png)

d.	For Import Frequency, Select Run On Demand
e.	For Import Options;
i.	Give a suitable Workflow Name
ii.	For the IAM role choose the LakeFormationWorkflowRole created previously
iii.	For Table prefix type “lakeformation_”
![](../1200/images/30.png) 

7.	Leave other options as default, Choose Create, and wait for the console to report that the workflow was successfully created. 
8.	Once the blueprint gets created, click on Start it Now? 
[There may be a delay of 5-10s delay in the blueprint showing up. You may have to hit refresh. Select the blueprint and choose Start in Actions drop down]

9.	Once the workflow starts executing, you will see the status changes from running  discovering  
![](../1200/images/31.png)  

### Explore the Underlying Components of a Blueprint
 
The Lake Formation blueprint creates a Glue Workflow under the hood which contains Glue ETL jobs – both python shell and pyspark; Glue crawlers and triggers. It will take somewhere between 15-20 mins to finish execution. In the meantime, let us drill down to see what it creates for us;

1.	On the Lake Formation console, in the navigation pane, choose Blueprints
2.	In the Workflow section, click on the Workflow name. This will direct you to the Workflow run page. Click on the Run Id.  
![](../1200/images/32.png) 
3.	On the Glue Console page, click on Get Started and head to ETL Workflows in the navigation pane.
4.	Here you can see the graphical representation of the Glue workflow built by Lake Formation blueprint. Highlighting and clicking on individual components will display the details of those components (name, description, job run id, start time, execution time)
5.	To understand what all Glue Jobs got created as a part of this workflow, in the navigation pane, click on Jobs.
10.	Every job comes with history, details, script and metrics tab. Review each of these tabs for any of the python shell or pyspark jobs.
![](../1200/images/33.png) 
 
### Explore workflow results in Athena

1.	Navigate to the Glue console
 
2.	Navigate to Databases on the left panel and select ticketdata
3.	Click on “Tables in ticketdata” and this table will be pre fixed by “lakeformation_”
Example: lakeformation__sportstickets_dms_sample_player
![](../1200/images/34.png)  

4.	And Click Action -> View Data
![](../1200/images/35.png)  

5.	This will now take you to Athena console, where you can preview the table contents, as show below;
![](../1200/images/36.png) 


Congratulation!!! You have completed lake formation lab. To explore more fine grain data lake security feature, continue to next section.

### [Optional] Grant fine grain access controls to Data Lake user

Before we start the querying the data, let us create an IAM User datalake_user and grant column level access on the table created by the Lake formation workflow above, to datalake_user. 

1.	Login as admin user to your account. Navigate to IAM Console and click on Add User.
![](../1200/images/38.png) 
 
2.	Create a user named datalake_user and give it a password: master123. 
![](../1200/images/39.png)  

3.	Next click on Permissions and keep navigating to the next steps until reached the end. Review the details and click on “Create User”.
![](../1200/images/40.png) 
 

4.	Navigate to the Lake Formation console, in the navigation pane, under Permissions, choose Data permissions.
![](../1200/images/41.png)
![](../1200/images/42.png)  
 
5.	Choose Grant, and in the Grant permissions dialog box, do the following: 
a.	For IAM user and roles, choose datalake_user. 
b.	For Database, choose ticketdata
c.	The Table list populates. 
d.	For Table, choose the table shown. 
e.	For Columns, select Include Columns and choose id, first_name
f.	For Table permissions, choose Select. 
6.	Choose Grant. 
![](../1200/images/43.png) 
 
### [Optional] Verify data permissions using Athena

Using Athena, let us now explore the data set as the datalake_user. 
1.	Sign in to AWS account with account ID and username as the datalake_user user – datalake_user (username: datalake_user)
2.	Navigate to Amazon Athena console. 
3.	Before you run your first Athena query, you need to set up a query result location in Amazon S3. On the right side of the Athena console, click on Settings and type in the S3 bucket location. The entry should look something like this: s3:// athenaqueryresults-<Accountnumber>/ 
4.	Next, ensure database ticketdata database is selected. 
5.	Now run a Select query on the table within the ticketdata database. There should be only one table in there, which was created by Lake Formation workflow.
6.	You will see that the **datalake_user** can only see the columns **id**, **first_name** in the select query result. The datalake_user cannot see last_name, sports_team_id, **full_name** columns in the table. This is because, datalake_admin gave datalake_user permissions to only select from table for the **id** and **first_name** columns. 

![](../1200/images/44.png) 

#### Congratulations on finishing this lab
+++
title = "AWS Cloud Development Kit"
weight = 40
+++

**"Setup AWS Cloud Development Kit"**

AWS Cloud Development Kit is a software development framework for defining cloud infrastructure in code and provisioning it through AWS CloudFormation. 

All CDK developers need to install **Node.js(>= 10.3.0)**, even those working in languages other than TypeScript or JavaScript. The AWS CDK Toolkit (cdk command-line tool) and the AWS Construct Library are developed in TypeScript and run on Node.js. The bindings for other supported languages use this backend and toolset.

You must provide your credentials and an AWS Region to use the AWS CDK CLI, as described in Specifying Your Credentials and Region.

### Install Node JS

Node JS(Java script) runtime will allow you to build scalable network applications. installing node will include npm.
NPM (Node Package Manager) for node performs two main things: first and foremost, it is an online repository for the publishing of open-source Node.js projects; second, it is a command-line utility for interacting with said repository that aids in package installation, version management, and dependency management.

**Why node js for API enabling the SAP Applications Workshop?**

AWS provides you the git-hub repository package to configure resources needed for this lab in AWS environment. You will download the git-hub package to update the parameters and build resources using simple node commands as mentioned in the LAB instructions
- Creates SAP ABAP developer edition environment using your own SAP media
- If you already have an SAP ABAP developer edition you can skip the step one and input your SAP environment details for AWS CDK to use the SAP hostname for integration the web application and Alexa app
- Creates a web application and highly performant API integrations to test the business context across the systems

Download the Node.js source code or a pre-built installer [see the Node JS installation steps here](https://nodejs.org/en/download/)

### Installation Steps 

1.Based on your preferred OS and your local system configuration (32 or 64 bit) download the pre-built installer binary files and install the package

![Node](../100-introduction/images/c9before.png "Node")

2.Double click on the downloaded to start the installation. Click the run button on the first screen to begin the installation

3.Double click on the downloaded to start the installation. Click the run button on the first screen to begin the installation

4.Accept the license agreement and click on the Next button

5.Choose the location where Node.js needs to be installed and then click on the Next button

6.Once node package is installed run the command node-v and npm --version to check the sucessful installation.

![Node](../100-introduction/images/c9after.png "Node")

## AWS CDK installation steps

1. For AWS CDK installation steps [see installing step AWS CDK documentation](https://docs.aws.amazon.com/cdk/latest/guide/getting_started.html)


**Install the AWS CDK using the following command from your local terminial**
```
npm install -g aws-cdk
```

**Run the following command to verify correct installation and print the version number of the AWS CDK**
```
cdk --version
```

**If you get an error message that your language framework is out of date, use one of the following commands to update the components that the AWS CDK needs to support the language**
```
TypeScript: npx npm-check-updates -u
Python: pip install --upgrade aws-cdk.core
Java: mvn versions:use-latest-versions
C(#): nuget update
```
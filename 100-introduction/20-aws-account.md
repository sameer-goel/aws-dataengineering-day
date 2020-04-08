+++
title = "AWS Account and IAM User"
weight = 20
+++

In order to actually do something with AWS services, you need an AWS account. Furthermore, you need an IAM user for this account that you can use to log into the AWS Management Console, so that you can provision and configure you resources.

[AWS Identity and Access Managment (IAM)](https://aws.amazon.com/iam/) enables you to manage access to AWS services and resources securely. Using IAM, you can create and manage AWS users and groups, and use permissions to allow and deny their access to AWS resources.

#### BYOA - bring your own account

The regular case is that you have to bring your own AWS account, configured with an IAM user with administrator privileges. If possible, this should be a vanilla account.

{{% notice warning %}}
If you bring your own account, either personal or a company account, make sure you understand the implications and policy of provisioning resources into this account.
{{% /notice %}}

If you don't have an AWS account yet, and you want to repeat this workshop for yourself later, you can [create a free account here](https://portal.aws.amazon.com/billing/signup) or ask your company's Cloud Center of Excellence to create one for you.

#### Create an administrator user

In case you want to use your own account now or later, and you don't yet have an IAM user with administrator privileges, you need to create one.

1. If you don't already have an AWS account with Administrator access: [create
one now by clicking here](https://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc)

***See below video for AWS account setup***

{{< youtube WviHsoz8yHk >}}

1. Once you have an AWS account, create a new IAM user for this workshop
with administrator access to the AWS account:
[Create a new IAM user to use for the workshop](https://console.aws.amazon.com/iam/home?#/users$new)

1. Enter the user details: and select Access Type as Programatic access

![IAM](../100-introduction/images/iam-1-create-user.png "IAM")

1. Attach the AdministratorAccess IAM Policy, choose Administrator access or fine-grained access depending 
upon the Amazon services used for this lab. For this lab you can choose Administrator access-->Click Next -->Ignore Tags--> Next Review
![IAM](../100-introduction/images/iam-2-attach-policy.png "IAM")

1. Click to create the new user:
![IAM](../100-introduction/images/iam-3-create-user.png "IAM")

1. Download the csv file for Access and Secret Keys:
![IAM](../100-introduction/images/iam-4-save-url.png "IAM")

**To apply Promo codes or AWS Educate credits to your account see the following video**

{{< youtube eXMyxC3_U3c >}}

#### Install AWS Command Line Interface in your local terminal

To install AWS Command Line Interface Installation [see AWS CLI documentation here](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html)

![Node](../100-introduction/images/awscli.png "Node")

**Link the AWCLI from your local termnical to your AWS account**

1. Use the AWS IAM credentials created in the AWS account creation steps
2. Execute the command aws configure and update your access key id, secret access key, default region and output format (don’t update any value in output format).

![Node](../100-introduction/images/awsconfigure.png "Node")

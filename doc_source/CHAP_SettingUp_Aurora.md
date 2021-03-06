# Setting Up Your Environment for Amazon Aurora<a name="CHAP_SettingUp_Aurora"></a>

Before you use Amazon Aurora for the first time, complete the following tasks:

1. [Sign Up for AWS](#CHAP_SettingUp_Aurora.SignUp)

1. [Create an IAM User](#CHAP_SettingUp_Aurora.IAM)

1. [Determine Requirements](#CHAP_SettingUp_Aurora.Requirements)

1. [Provide Access to the DB Cluster in the VPC by Creating a Security Group](#CHAP_SettingUp_Aurora.SecurityGroup)

## Sign Up for AWS<a name="CHAP_SettingUp_Aurora.SignUp"></a>

When you sign up for Amazon RDS \(AWS\), your AWS account is automatically signed up for all services in AWS, including Amazon RDS\. You are charged only for the services that you use\.

With Amazon RDS, you pay only for the resources you use\. The Amazon RDS DB cluster that you create is live \(not running in a sandbox\)\. You incur the standard Amazon RDS usage fees for the cluster until you terminate it\. For more information about Amazon RDS usage rates, see the [Amazon RDS product page](http://aws.amazon.com/rds)\. If you are a new AWS customer, you can get started with Amazon RDS for free; for more information, see [AWS Free Usage Tier](http://aws.amazon.com/free/)\.

If you have an AWS account already, skip to the next task\. If you don't have an AWS account, use the following procedure to create one\.

**To create an AWS account**

1. Open [https://aws\.amazon\.com/](https://aws.amazon.com/), and then choose **Create an AWS Account**\.
**Note**  
This might be unavailable in your browser if you previously signed into the AWS Management Console\. In that case, choose **Sign in to a different account**, and then choose **Create a new AWS account**\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a PIN using the phone keypad\.

Note your AWS account number, because you'll need it for the next task\.

## Create an IAM User<a name="CHAP_SettingUp_Aurora.IAM"></a>

Services in AWS, such as Amazon RDS, require that you provide credentials when you access them, so that the service can determine whether you have permission to access its resources\. The console requires your password\. You can create access keys for your AWS account to access the command line interface or API\. However, we don't recommend that you access AWS using the credentials for your AWS account; we recommend that you use AWS Identity and Access Management \(IAM\) instead\. Create an IAM user, and then add the user to an IAM group with administrative permissions or grant this user administrative permissions\. You can then access AWS using a special URL and the credentials for the IAM user\.

If you signed up for AWS but have not created an IAM user for yourself, you can create one using the IAM console\.

**To create an IAM user for yourself and add the user to an Administrators group**

1. Use your AWS account email address and password to sign in as the *[AWS account root user](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html)* to the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user below and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](http://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane of the console, choose **Users**, and then choose **Add user**\.

1. For **User name**, type **Administrator**\.

1. Select the check box next to **AWS Management Console access**, select **Custom password**, and then type the new user's password in the text box\. You can optionally select **Require password reset** to force the user to create a new password the next time the user signs in\.

1. Choose **Next: Permissions**\.

1. On the **Set permissions** page, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** type **Administrators**\.

1. For **Filter policies**, select the check box for **AWS managed \- job function**\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users, and to give your users access to your AWS account resources\. To learn about using policies to restrict users' permissions to specific AWS resources, go to [Access Management](http://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

To sign in as this new IAM user, sign out of the AWS console, then use the following URL, where *your\_aws\_account\_id* is your AWS account number without the hyphens \(for example, if your AWS account number is `1234-5678-9012`, your AWS account ID is `123456789012`\):

```
https://your_aws_account_id.signin.aws.amazon.com/console/
```

Enter the IAM user name and password that you just created\. When you're signed in, the navigation bar displays "*your\_user\_name* @ *your\_aws\_account\_id*"\.

If you don't want the URL for your sign\-in page to contain your AWS account ID, you can create an account alias\. From the IAM dashboard, choose **Customize** and enter an alias, such as your company name\. To sign in after you create an account alias, use the following URL:

```
https://your_account_alias.signin.aws.amazon.com/console/
```

To verify the sign\-in link for IAM users for your account, open the IAM console and check under **AWS Account Alias** on the dashboard\.

## Determine Requirements<a name="CHAP_SettingUp_Aurora.Requirements"></a>

The basic building block of Aurora is the DB cluster\. One or more DB instances can belong to a DB cluster\. A DB cluster provides a network address called the **Cluster Endpoint**\. Your applications connect to the cluster endpoint exposed by the DB cluster whenever they need to access the databases created in that DB cluster\. The information you specify when you create the DB cluster controls configuration elements such as memory, database engine and version, network configuration, security, and maintenance periods\.

You must know your DB cluster and network needs before you create a security group and before you create a DB cluster\. For example, you must know the following:
+ What are the memory and processor requirements for your application or service? You will use these settings when you determine what DB instance class you will use when you create your DB cluster\. For specifications about DB instance classes, see [Choosing the DB Instance Class](Concepts.DBInstanceClass.md)\.
+ Your DB cluster is in a virtual private cloud \(VPC\)\. Security group rules must be configured to connect to a DB cluster\. The follow list describes the rules for each VPC option:
  + **Default VPC** — If your AWS account has a default VPC in the region, that VPC is configured to support DB clusters\. If you specify the default VPC when you create the DB cluster:
    + You must create a **VPC security group** that authorizes connections from the application or service to the Aurora DB cluster with the database\. Note that you must use the [Amazon EC2 API](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/Welcome.html) or the **Security Group** option on the VPC Console to create VPC security groups\. For information, see [Step 4: Create a VPC Security Group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)\.
    + You must specify the default DB subnet group\. If this is the first DB cluster you have created in the region, Amazon RDS will create the default DB subnet group when it creates the DB cluster\.
  + **User\-defined VPC** — If you want to specify a user\-defined VPC when you create a DB cluster:
    + You must create a **VPC security group** that authorizes connections from the application or service to the Aurora DB cluster with the database\. Note that you must use the [Amazon EC2 API](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/Welcome.html) or the **Security Group** option on the VPC Console to create VPC security groups\. For information, see [Step 4: Create a VPC Security Group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)\.\.
    + The VPC must meet certain requirements in order to host DB clusters, such as having at least two subnets, each in a separate availability zone\. For information, see [Amazon Virtual Private Cloud \(VPCs\) and Amazon RDS](USER_VPC.md)\.
    + You must specify a DB subnet group that defines which subnets in that VPC can be used by the DB cluster\. For information, see the DB Subnet Group section in [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#Overview.RDSVPC.Create)\.
+ Do you need failover support? On Aurora, a Multi\-AZ deployment creates a primary instance and Aurora Replicas\. You can configure the primary instance and Aurora Replicas to be in different Availability Zones for failover support\. We recommend Multi\-AZ deployments for production workloads to maintain high availability\. For development and test purposes, you can use a non\-Multi\-AZ deployment\. For more information, see [High Availability for Aurora](Concepts.AuroraHighAvailability.md)\. 
+ Does your AWS account have policies that grant the permissions needed to perform Amazon RDS operations? If you are connecting to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS operations\. For more information, see [Authentication and Access Control for Amazon RDS](UsingWithRDS.IAM.md)\.
+ What TCP/IP port will your database be listening on? The firewall at some companies may block connections to the default port for your database engine\. If your company firewall blocks the default port, choose another port for the new DB cluster\. Note that once you create a DB cluster that listens on a port you specify, you can change the port by modifying the DB cluster\.
+ What region do you want your database in? Having the database close in proximity to the application or web service could reduce network latency\.

Once you have the information you need to create the security group and the DB cluster, continue to the next step\.

## Provide Access to the DB Cluster in the VPC by Creating a Security Group<a name="CHAP_SettingUp_Aurora.SecurityGroup"></a>

Your DB cluster will most likely be created in a VPC\. Security groups provide access to the DB cluster in the VPC\. They act as a firewall for the associated DB cluster, controlling both inbound and outbound traffic at the cluster level\. DB clusters are created by default with a firewall and a default security group that prevents access to the DB cluster\. You must therefore add rules to a security group that enable you to connect to your DB cluster\. Use the network and configuration information you determined in the previous step to create rules to allow access to your DB cluster\.

The security group you need to create is a *VPC security group*, unless you have a legacy DB cluster not in a VPC that requires a* DB security group*\. If you created your AWS account after March 2013, chances are very good that you have a default VPC, and your DB cluster will be created in that VPC\. DB clusters in a VPC require that you add rules to a VPC security group to allow access to the cluster\.

For example, if you have an application that will access a database on your DB cluster in a VPC, you must add a Custom TCP rule that specifies the port range and IP addresses that application will use to access the database\. If you have an application on an Amazon EC2 cluster, you can use the VPC or EC2 security group you set up for the Amazon EC2 cluster\.

**To create a VPC security group**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc](https://console.aws.amazon.com/vpc)\.

1. In the top right corner of the AWS Management Console, select the region in which you want to create the VPC security group and the DB cluster\. In the list of Amazon VPC resources for that region, it should show that you have at least one VPC and several Subnets\. If it does not, you do not have a default VPC in that region\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create Security Group**\.

1. In the **Create Security Group** window, type the **Name tag**, **Group name**, and **Description** of your security group\. Select the **VPC** that you want to create your DB cluster in\. Choose **Yes, Create**\.

1. The VPC security group you created should still be selected\. The details pane at the bottom of the console window displays the details for the security group, and tabs for working with inbound and outbound rules\. Choose the **Inbound Rules** tab\.

1. On the **Inbound Rules** tab, choose **Edit**\. Select **Custom TCP Rule** from the **Type** list\. Type the port value you will use for your DB cluster in the **Port Range** text box, and then type the IP address range \(CIDR value\) from where you will access the cluster, or select a security group name in the **Source** text box\.

1. If you need to add more IP addresses or different port ranges, choose **Add another rule**\.

1. If you need to, you can use the **Outbound Rules** tab to add rules for outbound traffic\.

1. When you have finished, choose **Save** on each tab with changes\.

   You will use the VPC security group you just created as the security group for your DB cluster when you create it\.

   Finally, a quick note about VPC subnets: If you use a default VPC, a default subnet group spanning all of the VPC's subnets has already been created for you\. When you use the **Launch a DB Instance** wizard to create a DB cluster, you can select the default VPC and use **default** for the **DB Subnet Group**\.

   Once you have completed the setup requirements, you can use your requirements and the security group you created to launch a DB cluster\. For information on creating a DB cluster, see the relevant documentation in the following table: 

   After setting up, you can create a test Amazon Aurora DB cluster\. For instructions, see [Creating a DB Cluster and Connecting to a Database on an Amazon Aurora DB Cluster](CHAP_GettingStartedAurora.md#CHAP_GettingStartedAurora.CreatingConnecting.Aurora)\.
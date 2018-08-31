# Maintaining an Amazon Aurora DB Cluster<a name="USER_UpgradeDBInstance.Maintenance"></a>

Periodically, Amazon RDS performs maintenance on Amazon RDS resources\. Maintenance most often involves updates to the DB cluster's underlying operating system \(OS\) or database engine version\. Updates to the operating system most often occur for security issues and should be done as soon as possible\. 

Maintenance items require that Amazon RDS take your DB cluster offline for a short time\. Maintenance that require a resource to be offline include scale compute operations, which generally take only a few minutes from start to finish, and required operating system or database patching\. Required patching is automatically scheduled only for patches that are related to security and instance reliability\. Such patching occurs infrequently \(typically once every few months\) and seldom requires more than a fraction of your maintenance window\. 

DB instances are not automatically backed up when an OS update is applied, so you should back up your DB instances before you apply an update\. 

You can view whether a maintenance update is available for your DB cluster by using the RDS console, the AWS CLI, or the Amazon RDS API\. If an update is available, it is indicated by the word **Available** or **Required** in the **Maintenance** column for the DB cluster on the Amazon RDS console, as shown following: 

![\[Offline patch available\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/offlinepatchavailable.png)

If an update is available, you can take one of the actions\. 
+ Defer the maintenance items\.
+ Apply the maintenance items immediately\.
+ Schedule the maintenance items to start during your next maintenance window\.
+ Take no action\.

**Note**  
Certain OS updates are marked as **Required**\. If you defer a required update, you receive a notice from Amazon RDS indicating when the update will be performed\. Other updates are marked as **Available**, and these you can defer indefinitely\.

The maintenance window determines when pending operations start, but does not limit the total execution time of these operations\. Maintenance operations are not guaranteed to finish before the maintenance window ends, and can continue beyond the specified end time\. For more information, see [The Amazon RDS Maintenance Window](#Concepts.DBMaintenance)\. 

## Applying Updates for a DB Cluster<a name="USER_UpgradeDBInstance.OSUpgrades"></a>

With Amazon RDS, you can choose when to apply maintenance operations\. You can decide when Amazon RDS applies updates by using the RDS console, AWS Command Line Interface \(AWS CLI\), or RDS API\. 

### AWS Management Console<a name="USER_UpgradeDBInstance.OSUpgrades.Console"></a>

**To manage an update for a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose Clusters\.

1. Select the check box for the DB cluster that has a required update\. 

1. Choose ********Actions****, and then choose one of the following:
   + **Upgrade now**
   + **Upgrade at next window**
**Note**  
If you choose **Upgrade at next window** and later want to delay the update, you can select **Defer upgrade**\.

### CLI<a name="USER_UpgradeDBInstance.OSUpgrades.CLI"></a>

To apply a pending update to a DB cluster, use the [apply\-pending\-maintenance\-action](http://docs.aws.amazon.com/cli/latest/reference/rds/apply-pending-maintenance-action.html) AWS CLI command\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds apply-pending-maintenance-action \
2.     --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db \
3.     --apply-action system-update \
4.     --opt-in-type immediate
```
For Windows:  

```
1. aws rds apply-pending-maintenance-action ^
2.     --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db ^
3.     --apply-action system-update ^
4.     --opt-in-type immediate
```

To return a list of resources that have at least one pending update, use the [describe\-pending\-maintenance\-actions](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-pending-maintenance-actions.html) AWS CLI command\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds describe-pending-maintenance-actions \
2.     --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db
```
For Windows:  

```
1. aws rds describe-pending-maintenance-actions ^
2.     --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db
```

You can also return a list of resources for a DB cluster by specifying the `--filters` parameter of the `describe-pending-maintenance-actions` AWS CLI command\. The format for the `--filters` command is `Name=filter-name,Value=resource-id,...`\.

The following are the accepted values for the `Name` parameter of a filter:
+ `db-instance-id` – Accepts a list of DB instance identifiers or Amazon Resource Names \(ARNs\)\. The returned list only includes pending maintenance actions for the DB instances identified by these identifiers or ARNs\.
+ `db-cluster-id` – Accepts a list of DB cluster identifiers or ARNs for Amazon Aurora\. The returned list only includes pending maintenance actions for the DB clusters identified by these identifiers or ARNs\.

For example, the following example returns the pending maintenance actions for the `sample-cluster1` and `sample-cluster2` DB clusters\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds describe-pending-maintenance-actions \
2. 	--filters Name=db-cluster-id,Values=sample-cluster1,sample-cluster2
```
For Windows:  

```
1. aws rds describe-pending-maintenance-actions ^
2. 	--filters Name=db-cluster-id,Values=sample-cluster1,sample-cluster2
```

### API<a name="USER_UpgradeDBInstance.OSUpgrades.API"></a>

To apply an update to a DB cluster, call the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html) action\.

To return a list of resources that have at least one pending update, call the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribePendingMaintenanceActions.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribePendingMaintenanceActions.html) action\.

## The Amazon RDS Maintenance Window<a name="Concepts.DBMaintenance"></a>

Every DB cluster has a weekly maintenance window during which any system changes are applied\. You can think of the maintenance window as an opportunity to control when modifications and software patching occur, in the event either are requested or required\. If a maintenance event is scheduled for a given week, it is initiated during the 30\-minute maintenance window you identify\. Most maintenance events also complete during the 30\-minute maintenance window, although larger maintenance events may take more than 30 minutes to complete\. 

The 30\-minute maintenance window is selected at random from an 8\-hour block of time per region\. If you don't specify a preferred maintenance window when you create the DB cluster, then Amazon RDS assigns a 30\-minute maintenance window on a randomly selected day of the week\. 

RDS will consume some of the resources on your DB cluster while maintenance is being applied\. You might observe a minimal effect on performance\. For a DB instance, on rare occasions, a Multi\-AZ failover might be required for a maintenance update to complete\. 

Following, you can find the time blocks for each region from which default maintenance windows are assigned\. 


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_UpgradeDBInstance.Maintenance.html)

## Adjusting the Preferred DB Cluster Maintenance Window<a name="AdjustingTheMaintenanceWindow.Aurora"></a>

The Aurora DB cluster maintenance window should fall at the time of lowest usage and thus might need modification from time to time\. Your DB cluster is unavailable during this time only if the updates that are being applied require an outage\. The outage is for the minimum amount of time required to make the necessary updates\. 

### AWS Management Console<a name="AdjustingTheMaintenanceWindow.Aurora.CON"></a>

**To adjust the preferred DB cluster maintenance window**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Clusters** on the left of the console\.

1. Choose the DB cluster for which you want to change the maintenance window\.

1. From **Actions**, choose **Modify cluster**\.

1. In the **Maintenance** section, update the maintenance window\.

1. Choose **Continue**\.

   On the confirmation page, review your changes\.

1. To apply the changes to the maintenance window immediately, select **Apply immediately**\. 

1.  Choose **Modify cluster** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

### CLI<a name="AdjustingTheMaintenanceWindow.Aurora.CLI"></a>

To adjust the preferred DB cluster maintenance window, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) command with the following parameters:
+ `--db-cluster-identifier`
+ `--preferred-maintenance-window`

**Example**  
The following code example sets the maintenance window to Tuesdays from 4:00\-4:30AM UTC\.  
For Linux, OS X, or Unix:  

```
aws rds modify-db-cluster \
--db-cluster-identifier my-cluster \
--preferred-maintenance-window Tue:04:00-Tue:04:30
```
For Windows:  

```
aws rds modify-db-cluster ^
--db-cluster-identifier my-cluster ^
--preferred-maintenance-window Tue:04:00-Tue:04:30
```

### API<a name="AdjustingTheMaintenanceWindow.Aurora.API"></a>

To adjust the preferred DB cluster maintenance window, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) action with the following parameters:
+ `DBClusterIdentifier = my-cluster`
+ `PreferredMaintenanceWindow = Tue:04:00-Tue:04:30`

**Example**  
The following code example sets the maintenance window to Tuesdays from 4:00\-4:30AM UTC\.  

```
 1. https://rds.us-west-2.amazonaws.com/
 2. ?Action=ModifyDBCluster
 3. &DBClusterIdentifier=my-cluster
 4. &PreferredMaintenanceWindow=Tue:04:00-Tue:04:30
 5. &SignatureMethod=HmacSHA256
 6. &SignatureVersion=4
 7. &Version=2014-10-31
 8. &X-Amz-Algorithm=AWS4-HMAC-SHA256
 9. &X-Amz-Credential=AKIADQKE4SARGYLE/20140725/us-east-1/rds/aws4_request
10. &X-Amz-Date=20161017T161457Z
11. &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
12. &X-Amz-Signature=d6d1c65c2e94f5800ab411a3f7336625820b103713b6c063430900514e21d784
```
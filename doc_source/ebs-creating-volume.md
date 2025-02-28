# Create an Amazon EBS volume<a name="ebs-creating-volume"></a>

You can create an Amazon EBS volume and then attach it to any EC2 instance in the same Availability Zone\. If you create an encrypted EBS volume, you can only attach it to supported instance types\. For more information, see [Supported instance types](EBSEncryption.md#EBSEncryption_supported_instances)\.

If you are creating a volume for a high\-performance storage scenario, you should make sure to use a Provisioned IOPS SSD volume \(`io1` or `io2`\) and attach it to an instance with enough bandwidth to support your application, such as an EBS\-optimized instance\. The same advice holds for Throughput Optimized HDD \(`st1`\) and Cold HDD \(`sc1`\) volumes\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

Empty EBS volumes receive their maximum performance the moment that they are available and do not require initialization \(formerly known as pre\-warming\)\. However, storage blocks on volumes that were created from snapshots must be initialized \(pulled down from Amazon S3 and written to the volume\) before you can access the block\. This preliminary action takes time and can cause a significant increase in the latency of an I/O operation the first time each block is accessed\. Volume performance is achieved after all blocks have been downloaded and written to the volume\. For most applications, amortizing this cost over the lifetime of the volume is acceptable\. To avoid this initial performance hit in a production environment, you can force immediate initialization of the entire volume or enable fast snapshot restore\. For more information, see [Initialize Amazon EBS volumes](ebs-initialize.md)\.

**Important**  
If you create an `io2` volume with a size greater than `16` TiB or with IOPS greater than `64,000` in a Region where EBS Block Express is supported, the volume automatically runs on Block Express\. `io2` Block Express volumes can be attached to supported instances only\. For more information, see [`io2` Block Express volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#io2-block-express)\.

**Methods of creating a volume**
+ Create and attach EBS volumes when you launch instances by specifying a block device mapping\. For more information, see [Launch an instance using the new launch instance wizard](ec2-launch-instance-wizard.md) and [Block device mappings](block-device-mapping-concepts.md)\.
+ Create an empty EBS volume and attach it to a running instance\. For more information, see [Create an empty volume](#ebs-create-empty-volume) below\.
+ Create an EBS volume from a previously created snapshot and attach it to a running instance\. For more information, see [Create a volume from a snapshot](#ebs-create-volume-from-snapshot) below\.

## Create an empty volume<a name="ebs-create-empty-volume"></a>

Empty volumes receive their maximum performance the moment that they are available and do not require initialization\.

You can create an empty EBS volume using one of the following methods\.

------
#### [ Console ]

**To create an empty EBS volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\.

1. Choose **Create volume**\.

1. For **Volume type**, choose the type of volume to create\. For more information, see [Amazon EBS volume types](ebs-volume-types.md)\.

1. For **Size**, enter the size of the volume, in GiB\. For more information, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.

1. \(`io1`, `io2`, and `gp3` only\) For **IOPS**, enter the maximum number of input/output operations per second \(IOPS\) that the volume should provide\. 

1. \(`gp3` only\) For **Throughput**, enter the throughput that the volume should provide, in MiB/s\.

1. For **Availability Zone**, choose the Availability Zone in which to create the volume\. A volume can be attached only to an instance that is in the same Availability Zone\.

1. For **Snapshot ID**, keep the default value \(**Don't create volume from a snapshot**\)\.

1. \(`io1` and `io2` only\) To enable the volume for Amazon EBS Multi\-Attach, select **Enable Multi\-Attach**\. For more information, see [Attach a volume to multiple instances with Amazon EBS Multi\-Attach](ebs-volumes-multi.md)\.

1. Set the encryption status for the volume\.

   If your account is enabled for [encryption by default](EBSEncryption.md#encryption-by-default), then encryption is automatically enabled and you can't disable it\. You can choose the KMS key to use to encrypt the volume\.

   If your account is not enabled for encryption by default, encryption is optional\. To encrypt the volume, for **Encryption**, choose **Encrypt this volume** and then select the KMS key to use to encrypt the volume\.
**Note**  
Encrypted volumes can be attached only to instances that support Amazon EBS encryption\. For more information, see [Amazon EBS encryption](EBSEncryption.md)\.

1. \(Optional\) To assign custom tags to the volume, in the **Tags** section, choose **Add tag**, and then enter a tag key and value pair\. For more information, see [Tag your Amazon EC2 resources](Using_Tags.md)\.

1. Choose **Create volume**\.
**Note**  
The volume is ready for use when the **Volume state** is **available**\.

1. To use the volume, attach it to an instance\. For more information, see [Attach an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

------
#### [ AWS CLI ]

**To create an empty EBS volume using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) \(AWS CLI\)
+ [New\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Volume.html) \(AWS Tools for Windows PowerShell\)

The volume is ready for use when the `state` is `available`\.

------

## Create a volume from a snapshot<a name="ebs-create-volume-from-snapshot"></a>

Volumes created from snapshots load lazily in the background\. This means that there is no need to wait for all of the data to transfer from Amazon S3 to your EBS volume before the instance can start accessing an attached volume and all its data\. If your instance accesses data that hasn't yet been loaded, the volume immediately downloads the requested data from Amazon S3, and then continues loading the rest of the volume data in the background\. Volume performance is achieved after all blocks are downloaded and written to the volume\. To avoid the initial performance hit in a production environment, see [Initialize Amazon EBS volumes](ebs-initialize.md)\.

New EBS volumes that are created from encrypted snapshots are automatically encrypted\. You can also encrypt a volume on\-the\-fly while restoring it from an unencrypted snapshot\. Encrypted volumes can only be attached to instance types that support EBS encryption\. For more information, see [Supported instance types](EBSEncryption.md#EBSEncryption_supported_instances)\.

You can create a volume from a snapshot using one of the following methods\.

------
#### [ Console ]

**To create an EBS volume from a snapshot using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\.

1. Choose **Create volume**\.

1. For **Volume type**, choose the type of volume to create\. For more information, see [Amazon EBS volume types](ebs-volume-types.md)\.

1. For **Size**, enter the size of the volume, in GiB\. For more information, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.

1. \(`io1`, `io2`, and `gp3` only\) For **IOPS**, enter the maximum number of input/output operations per second \(IOPS\) that the volume should provide\. 

1. \(`gp3` only\) For **Throughput**, enter the throughput that the volume should provide, in MiB/s\.

1. For **Availability Zone**, choose the Availability Zone in which to create the volume\. A volume can be attached only to instances that are in the same Availability Zone\.

1. For **Snapshot ID**, select the snapshot from which to create the volume\.

1. Set the encryption status for the volume\.

   If the selected snapshot is encrypted, or if your account is enabled for [encryption by default](EBSEncryption.md#encryption-by-default), then encryption is automatically enabled and you can't disable it\. You can choose the KMS key to use to encrypt the volume\.

   If the selected snapshot is unencrypted and your account is not enabled for encryption by default, encryption is optional\. To encrypt the volume, for **Encryption**, choose **Encrypt this volume** and then select the KMS key to use to encrypt the volume\.
**Note**  
Encrypted volumes can be attached only to instances that support Amazon EBS encryption\. For more information, see [Amazon EBS encryption](EBSEncryption.md)\.

1. \(Optional\) To assign custom tags to the volume, in the **Tags** section, choose **Add tag**, and then enter a tag key and value pair\. For more information, see [Tag your Amazon EC2 resources](Using_Tags.md)\.

1. Choose **Create Volume**\.
**Note**  
The volume is ready for use when the **Volume state** is **available**\.

1. To use the volume, attach it to an instance\. For more information, see [Attach an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

------
#### [ AWS CLI ]

**To create an EBS volume from a snapshot using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) \(AWS CLI\)
+ [New\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Volume.html) \(AWS Tools for Windows PowerShell\)

The volume is ready for use when the `state` is `available`\.

------
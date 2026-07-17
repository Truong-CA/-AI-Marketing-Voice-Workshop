---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Migrating Data at Scale and Reducing Storage Costs with Amazon S3 File Gateway

If you are managing an on-premises storage system and planning to migrate a large volume of data to the cloud, you have probably encountered—or will eventually encounter—a common challenge: **How can you migrate data while preserving its original directory structure and file creation timestamps?**

When file timestamps are reset during migration, valuable historical information is lost. More importantly, it becomes impossible to take full advantage of Amazon S3 Lifecycle policies that automatically move older data into lower-cost storage classes, resulting in unnecessary storage expenses.

## 1. The Challenge

When data is migrated to Amazon S3 through **Amazon S3 File Gateway**, the gateway automatically uses the file's **Modified Date** from the source system as the object's new **Create Date** in Amazon S3.

As a result, the file's apparent age is effectively reset. Amazon S3 Lifecycle Policies can no longer determine the file's actual age, preventing older data from being automatically transitioned into lower-cost storage classes based on when it was originally created. This can significantly increase long-term storage costs for organizations.

## 2. The Solution: Automating Metadata Preservation

Instead of allowing AWS to reset file timestamps during migration, organizations can combine **Robocopy**, **Amazon S3 File Gateway**, and **AWS Lambda** to preserve the original metadata throughout the migration process.

After a file is uploaded to Amazon S3, an AWS Lambda function automatically reads the file's original timestamp stored in its metadata and moves the object into the most appropriate storage class—such as **S3 Glacier Instant Retrieval** or **S3 Glacier Deep Archive**—based on its actual age.

## 3. Why Is This an Effective Migration Solution?

This approach provides several important benefits for large-scale data migrations.

### Preserve Original Metadata

The original creation time, modification time, and NTFS file permissions are retained throughout the migration process.

### Optimize Storage Costs at Scale

Older files can be automatically transitioned into lower-cost Amazon S3 storage classes based on their true age rather than the upload date.

### Support Hybrid Cloud Architectures

Organizations can continue accessing cloud-based data from existing on-premises applications using familiar protocols such as **SMB** and **NFS**, without changing their current workflows.

### Fully Automated Operation

AWS Lambda integrates seamlessly with Amazon S3 to automate storage class transitions without requiring manual intervention.

## 4. How the Solution Works

The architecture consists of three primary components working together.

### Robocopy

Robocopy copies files to the Amazon S3 File Gateway file share while preserving important file attributes by using options such as:

- `/TIMEFIX` to preserve timestamps.
- `/SECFIX` to preserve NTFS permissions.

### Amazon S3 File Gateway

When files are uploaded to Amazon S3, the gateway stores the original modification time (`mtime`) as **user-defined metadata** attached to each S3 object.

### AWS Lambda

Whenever a new object is uploaded to Amazon S3 through a **PUT** request, an AWS Lambda function is triggered.

The function reads the stored `mtime` value (in Unix timestamp format) from the object's metadata and determines whether the object should be moved into a lower-cost storage class based on its actual age.

## 5. Deployment Considerations

There are a few important considerations when implementing this solution.

### Robocopy Performance

Copy speed depends on factors such as the number of files and disk performance.

Before performing the actual migration, you can use the `/L` (dry run) option to preview the operations Robocopy will perform.

### Choosing a Platform for Amazon S3 File Gateway

Amazon S3 File Gateway can be deployed in multiple environments, including:

- VMware
- Microsoft Hyper-V
- Linux KVM
- AWS Storage Gateway hardware appliance deployed in an on-premises data center

This flexibility allows organizations to integrate cloud storage with their existing infrastructure while minimizing operational changes.



**Original Post (AWS Study Group):** https://www.facebook.com/share/p/1BVxJjTEmi/?
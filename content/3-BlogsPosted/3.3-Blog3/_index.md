---
title: "Blog 3"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# IAM User & IAM Policy: Two Fundamental Concepts When Learning AWS

IAM User and IAM Policy are two fundamental concepts that anyone learning AWS should understand.

When I first created my AWS account, I only used the Root Account because it is the first account AWS provides. At first, it seemed convenient, but after learning more about AWS security, I realized this is one of the most common mistakes beginners make when starting with cloud computing.

The Root Account has full control over the entire AWS account. If its password or access keys are ever compromised, all AWS resources could be deleted or misused.

To minimize this risk, AWS recommends using the Root Account only for a few special administrative tasks while performing daily operations through IAM Users combined with IAM Policies.

## Why shouldn't you use the Root Account every day?

The Root Account has **Full Administrative Access** to every AWS service and resource. Unlike IAM Users, it is not restricted by any IAM Policy, meaning it can create, modify, or delete any resource within the account.

Because of these unrestricted permissions, the Root Account also carries significant security risks. If an attacker gains access to it, they could delete EC2 instances, RDS databases, S3 buckets, or create new resources that result in unexpected costs.

AWS recommends avoiding the Root Account for everyday activities. Instead, create IAM Users with only the permissions they actually need.

The Root Account should only be used for special tasks such as:

- Changing payment methods.
- Managing billing information.
- Closing the AWS account.
- Performing actions that only the Root Account is allowed to perform.

After completing the initial account setup, I rarely use the Root Account anymore. Instead, I perform all daily work through IAM Users, which is also one of AWS's recommended Security Best Practices.

## IAM User – One Account for Each Person

Instead of allowing multiple people to share the Root Account, I create a separate IAM User for every individual.

Each IAM User has:

- Its own username and password.
- Its own Multi-Factor Authentication (MFA).
- Its own Access Keys when using the AWS CLI or SDK.

This allows every team member to use their own account without sharing credentials. AWS can also accurately identify who performed each action through AWS CloudTrail.

User management becomes much easier as well. When a new employee joins, simply create a new IAM User. When someone leaves, disable or delete that user account.

## IAM Policy – Defining What Users Can Do

If an IAM User is like an employee, then an IAM Policy is the job description.

For example, an intern may only need permissions to manage EC2 by:

- Viewing EC2 instances.
- Starting or stopping instances.
- Rebooting instances.

Meanwhile, permissions such as:

- Deleting EC2 instances.
- Deleting Amazon RDS databases.
- Modifying IAM Users or IAM Policies.
- Accessing Billing.

should not be granted.

IAM Policies ensure that users receive only the permissions required to perform their jobs while reducing security risks.

## Principle of Least Privilege – Grant Only the Permissions That Are Needed

AWS strongly recommends following the **Principle of Least Privilege**, which means granting only the permissions required—nothing more.

For example, if an intern only needs to monitor EC2 instances and view data stored in Amazon S3, then only these permissions should be granted:

- View EC2 resources.
- Read objects in Amazon S3.

Permissions such as:

- Deleting VPCs.
- Deleting Amazon RDS databases.
- Managing IAM.
- Accessing Billing.

should not be assigned.

This principle helps reduce accidental mistakes while significantly improving the overall security of the AWS environment.

## Where Can IAM Policies Be Attached?

IAM Policies can be attached to several different AWS identities.

### IAM User

The policy applies to one specific user.

This approach is simple but becomes difficult to manage as the number of users grows.

### IAM Group

This is the approach most organizations use.

Instead of assigning permissions to individual users, policies are attached to groups. For example:

- **Developer Group:** Manage EC2 and CloudWatch.
- **Tester Group:** Read-only access.
- **Finance Group:** Billing access only.

When a new employee joins, simply add them to the appropriate group.

### IAM Role

IAM Roles are commonly used when AWS services need permission to perform actions on behalf of users.

For example, if an EC2 instance needs to read data from Amazon S3, attaching an IAM Role to the EC2 instance is much safer than storing Access Keys on the server.

This is also the approach recommended by AWS because it is both secure and easy to manage.

## Managed Policies and Inline Policies

AWS supports two common types of IAM Policies.

### Managed Policies

Managed Policies can be reused across multiple Users, Groups, and Roles.

For example, an EC2 management policy can be attached to multiple developers.

Advantages include:

- Reusable across multiple identities.
- Easier to maintain.
- Updating the policy automatically updates permissions for every attached identity.

AWS also provides many built-in managed policies such as:

- `ReadOnlyAccess`
- `AmazonS3ReadOnlyAccess`
- `AdministratorAccess`

### Inline Policies

An Inline Policy belongs to only one User, Group, or Role.

If that identity is deleted, the policy is deleted as well.

Inline Policies are useful for special situations but are generally used less frequently because they are harder to manage at scale.

## A Practical Example

Suppose my team consists of three roles:

- **Intern:** Can only view EC2 and CloudWatch.
- **Developer:** Can deploy applications and manage EC2 and Amazon S3.
- **Administrator:** Has full control over the AWS infrastructure.

Assigning permissions based on roles ensures that everyone can perform only the tasks required for their responsibilities, improving both security and manageability.

## Conclusion

Through learning AWS IAM, I realized that cloud security is not only about using strong passwords but also about granting the right permissions to the right people.

IAM Users provide separate identities for each individual, IAM Policies precisely define what each identity can do, and the Principle of Least Privilege minimizes security risks while protecting AWS resources.

These concepts form one of the most important foundations for anyone beginning their AWS journey.

**Original Post (AWS Study Group):** https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206918726739754/?rdid=6SeKNuZnOr9PjQrn#


**Original Post (AWS Study Group):** https://www.facebook.com/share/p/1BVxJjTEmi/?
{{% notice note %}}
I'd like to borrow a blog post from a member of my group who left the AWS - FCAJ internship program because they already had the signature of their previous employer.
{{% /notice %}}
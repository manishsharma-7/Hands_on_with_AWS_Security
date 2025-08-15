# Cloud Security with AWS IAM

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-iam)

**Author:** Manish Sharma  
**Email:** manishsharma7@hotmail.com

---

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-iam_1c864649)

---

## Introducing Today's Project!

In this project, I’ll use AWS Identity and Access Management (IAM) to control who can access an EC2 instance and what they can do.

I’ll:
💻 Launch an EC2 instance
📏 Create IAM policies
👩‍👩‍👧‍👧 Add IAM users and groups
🔖 Set an AWS account alias

By the end, I’ll understand how AWS handles authentication and authorization.

### Tools and concepts

Services I used were EC2 and IAM (including users, groups, policies, and account aliases). Key concepts I learnt include controlling access with IAM policies, tagging resources to manage environments, testing permissions safely with the IAM Policy Simulator, and organizing users into groups for easier permission management.

### Project reflection

This project took me approximately 1–2 hours. The most challenging part was writing the JSON IAM policy to allow access only to the development instance while restricting other actions. It was most rewarding to see the Policy Simulator confirm that my permissions worked as intended and that the intern user could safely access only the resources they were supposed to.

---

## Tags

Tags in AWS are key–value pairs you attach to resources (like EC2 instances, S3 buckets, or IAM users) to help identify, organize, and manage them.

They’re useful for:

Organization – Group resources by project, environment (e.g., dev/test/prod), or owner.

Cost tracking – Allocate and track costs for specific teams or projects.

Automation – Use tags in scripts or AWS services (like Auto Scaling or backup policies) to target resources.

Access control – Apply IAM policies that grant or restrict access based on tags.

Think of them like sticky notes on your AWS resources — small labels that make finding, managing, and billing much easier.

For my two EC2 instances, I used the tag Env (short for Environment) to indicate their purpose:

Instance 1 is tagged Env = Production, meaning it’s intended for live workloads that end users interact with.

Instance 2 is tagged Env = Development, meaning it’s for testing, experimenting, and building features before they go live.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-iam_2e0e5a5d)

---

## IAM Policies

IAM Policies define permissions in AWS — they specify who can do what on which resources.

In other words, they control access by allowing or denying actions like:

Starting or stopping an EC2 instance

Reading or writing to an S3 bucket

Modifying IAM users or groups

Policies help enforce security by ensuring users and roles can only perform actions they’re authorized for.

### The policy I set up

For this project, I’ve set up a policy using JSON to script the permissions and define exactly what actions are allowed on the development instance.

The effect of my policy is:

Allow full access (ec2:*) to EC2 resources only if the instance has the tag Env = development. This means the user can start, stop, or modify the development instance but not the production one.

Allow read-only access (ec2:Describe*) to all EC2 resources, so the user can view details of any instance, including production.

Deny creating or deleting tags (ec2:CreateTags and ec2:DeleteTags) on any EC2 resource, preventing the user from modifying instance tags.

In short, the policy gives controlled access to the development instance, lets the user view all instances, and blocks tag changes to maintain security and organization.

### When creating a JSON policy, you have to define its Effect, Action and Resource.

The Effect, Action, and Resource attributes of a JSON policy mean:

Effect – whether the policy allows or denies the specified actions.

Action – the specific AWS operations the policy applies to (for example, starting or stopping EC2 instances).

Resource – the AWS resources the actions apply to (for example, a particular EC2 instance or all instances).

---

## My JSON Policy

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-iam_1c864649)

---

## Account Alias

An account alias is a custom name for your AWS account that replaces the default account ID, making it easier for users to sign in.

Creating an account alias took me a few minutes. Now, my new AWS console sign-in URL is https://nextwork-alias-manish.signin.aws.amazon.com/console.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-iam_0eb4439b)

---

## IAM Users and User Groups

### Users

IAM users are individual identities created in AWS that represent a person or application.

They have unique credentials (like a username and password or access keys) and can be assigned permissions directly or through groups to control what actions they can perform in your AWS account.

### User Groups

IAM user groups are collections of IAM users in AWS.

They allow you to:

Manage permissions collectively – assign policies to the group instead of individual users.

Simplify administration – add or remove users from a group to automatically grant or revoke permissions.

Organize users – group users by role, team, or project for easier management.

Think of a group as a shortcut to applying the same rules to multiple users at once.

I attached the policy I created to this user group, which means all users in the group automatically inherit the permissions defined in the policy.

---

## Logging in as an IAM User

The first way is to share the AWS console sign-in URL, username, and temporary password with the user so they can log in to the AWS Management Console.

The second way is to provide access keys (Access Key ID and Secret Access Key) so the user or application can access AWS programmatically via the CLI, SDKs, or APIs.

Once I logged in as my IAM user, I noticed “Access denied to servicecatalog:ListApplications.” This was because the policy attached to the user group only allows actions on EC2 instances and related resources, and does not grant permissions for Service Catalog or other AWS services.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-iam_6f2ab446)

---

## Testing IAM Policies

I tested my JSON IAM policy by trying to stop, start and view details of my two EC2 instances to confirm that I could fully manage the development instance, had read-only access to the production instance, and was prevented from modifying tags.

### Stopping the production instance

When I tried to stop the production instance, I received the message: “You are not authorized to perform this operation.” This was because the IAM policy attached to my user only allows actions on instances tagged Env = development, so I don’t have permission to stop the production instance.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-iam_0e7a9d6a)

---

## Testing IAM Policies

### Stopping the development instance

Next, when I tried to stop the development instance, the action succeeded. This was because the IAM policy attached to my user allows full EC2 actions on instances tagged Env = development.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-iam_1811801c)

---

## The IAM Policy Simulator

The IAM Policy Simulator is a tool that lets you test and validate IAM policies without affecting actual AWS resources. It's useful for checking whether a user, group, or role has the correct permissions and ensuring policies work as intended before applying them in a real environment.

### How I used the simulator

I set up a simulation for my IAM Group policy using the IAM Policy Simulator. The results were initially denied for both DeleteTags and StopInstances, showing which statement in my policy was blocking these actions. I had to adjust the simulation by specifying the development instance tag for StopInstances, and after running it again, the simulator showed that access was granted, confirming that my policy works as intended for the development instance.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-iam_069d8a621)

---

---

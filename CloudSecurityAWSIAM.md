<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Cloud Security with AWS IAM

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-iam)

**Author:** David Ivory  
**Email:** davidmivory@gmail.com

---

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-iam_1c864649)

---

## Introducing Today's Project!

In this lab, I demonstrated how to securely manage cloud resources using AWS IAM. I created and applied IAM policies, set up users and groups, configured an account alias, launched EC2 instances, and tested access controls for different user roles.

### Tools and concepts

In this project, I learned key AWS services like EC2 and IAM, and concepts such as resource tagging, IAM policies, user groups, and permission management to securely control access and scale cloud infrastructure.

### Project reflection

This project took me approximately 1 hour. The most challenging part was creating precise IAM policies to restrict access correctly. It was most rewarding to successfully secure resources while enabling the intern to work in the dev environment.

---

## Tags

Tags are key-value pairs used to organize AWS resources. They are useful for identifying resources, managing costs, automating tasks, and applying access controls based on resource attributes.

The tag I’ve used on my EC2 instances is called Env. The values I’ve assigned for my instances are production for nextwork-prod-holiday and development for nextwork-dev-holiday.

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-iam_2e0e5a5d)

---

## IAM Policies

IAM policies define permissions for AWS resources. They control who can access what by specifying allowed or denied actions on specific resources, helping enforce security and access management across an AWS environment.

### The policy I set up

For this project, I’ve set up a policy using JSON. 

The effect of my policy is that it allows full EC2 access only to instances tagged with Env=development, allows read-only access to all EC2 resources, and explicitly denies the ability to create or delete tags on any resource.

### When creating a JSON policy, you have to define its Effect, Action and Resource.

Effect - defines whether the action is allowed or denied. 
Action - specifies what operations are being permitted or restricted.
Resource - identitifies the AWS resources the action applies to

---

## My JSON Policy

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-iam_1c864649)

---

## Account Alias

An account alias is a custom name that replaces the default AWS account ID in the login URL. It makes the sign-in process easier and more user-friendly, especially for team members like interns.

Creating an account alias took me less than a minute... Now, my new AWS console sign-in URL is https://nextwork-alias-holiday.signin.aws.amazon.com/console. 

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-iam_0eb4439b)

---

## IAM Users and User Groups

### Users

IAM users are individual identities in AWS with specific credentials and permissions. They represent real people or applications and allow secure access to AWS services based on the policies attached to them or their user groups.

### User Groups

IAM user groupsIAM user groups are collections of IAM users that share the same permissions. Instead of assigning policies to each user individually, I can attach policies to the group, making access management simpler and more consistent. are...

The effect of attaching my policy to the user group is that all users in the group inherit the permissions defined in the policy, allowing them controlled access to the development EC2 instance without needing individual policy assignments.

---

## Logging in as an IAM User

I can share a new user's sign-in details by either downloading the .csv file with their credentials or emailing them the custom sign-in URL along with their username and temporary password.

I observed that the new IAM user had limited access in the AWS dashboard, with permissions scoped only to the development EC2 instance and no visibility or control over the production instance.

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-iam_6f2ab446)

---

## Testing IAM Policies

I tested my JSON IAM policy by stopping the development instance successfully and attempting to stop the production instance, which was denied, confirming the policy correctly restricts access based on resource tags.

### Stopping the production instance

When I tried to stop the production instance using the intern account, the action was denied because the IAM policy restricts the intern’s permissions to only the development instance, preventing any changes to production resources.

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-iam_0e7a9d6a)

---

## Testing IAM Policies

### Stopping the development instance

When I tried to stop the development instance, the action was successful because my IAM policy allows full EC2 access to instances tagged with Env=development.

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-iam_1811801c)

---

## The IAM Policy Simulator

### How I used the simulator

---

---

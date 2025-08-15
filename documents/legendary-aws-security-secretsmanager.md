<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Secure Secrets with Secrets Manager

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-secretsmanager)

**Author:** Manish Sharma  
**Email:** manishsharma7@hotmail.com

---

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-secretsmanager_r7s8t9u0)

---

## Introducing Today's Project!

In this project, I will demonstrate how to secure credentials in a web application using AWS Secrets Manager. I'm doing this project to learn:
🕵️‍♀️ How a web app can insecurely store credentials.
😮‍💨 How GitHub’s secret scanning feature can block insecure code from being pushed.
🔐 How to update the web app to use AWS Secrets Manager for secure credential storage and retrieval.
👏 How secured web app code can be made public without exposing sensitive credentials.

### Tools and concepts

Services I used in this project were:
-AWS Secrets Manager for securely storing and retrieving credentials.
-Git & GitHub for version control and managing the web app code.

Key concepts I learnt include:
-Avoiding hardcoded credentials in application code.
-Using Secrets Manager to securely fetch AWS credentials at runtime.
-Updating application configuration (config.py) to work with secrets.
-GitHub secret scanning to prevent sensitive data from being pushed.
-Git rebasing and resolving merge conflicts to maintain a clean commit history.

### Project reflection

This project took me approximately 2 hours.

The most challenging part was resolving the merge conflict and successfully pushing my changes to GitHub afterward.

It was most rewarding to see the app run successfully with secure credentials and to verify that GitHub blocked any attempts to push sensitive data, demonstrating a real improvement in security practices.

chose to do this project today because I wanted to practice securing application credentials and understand how AWS Secrets Manager works in a real-world scenario.

The project met my goals. I successfully replaced hardcoded credentials with Secrets Manager, ensured the app ran securely, and observed GitHub’s secret scanning in action to prevent sensitive data exposure.

---

## Hardcoding credentials

In this project, a sample web app is exposing AWS credentials directly in its code. It is unsafe to hardcode credentials because anyone who accesses the code—intentionally or accidentally—can use them to access your AWS resources, potentially leading to data breaches, unauthorized actions, or security incidents.

I've set up the initial configuration with dummy credentials.

These credentials are just examples because they are sample access keys for AWS—we’re imagining that these are real credentials that could give someone access to your AWS account if they were valid.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-secretsmanager_j2k3l4m5)

---

## Using my own AWS credentials

As an extension for this project, I also decided to set up a virtual environment to safely run and test the web app. To create the environment, I ran:

python3 -m venv venv

Then, within this virtual environment, I installed the necessary packages:
fastapi==0.115.8
uvicorn==0.33.0
boto3==1.36.20
python-multipart==0.0.5

This ensures the project has an isolated Python environment with all the dependencies it needs.

When I first ran the app, I ran into an error:

"error": "An error occurred (InvalidAccessKeyId) when calling the ListBuckets operation: The AWS Access Key Id you provided does not exist in our records."

This happened because the AWS Access Key ID provided for the web app is not valid. The app is running, but it can't access my AWS account because we are using the placeholder credentials in config.py, which are not real AWS credentials.

To resolve the InvalidAccessKeyId error, I updated the web app’s configuration in config.py with my real AWS credentials, including a valid AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, and AWS_REGION. This allowed the app to authenticate properly and access my AWS resources.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-secretsmanager_wghjteykut)

---

## Pushing Insecure Code to GitHub

Once I updated the web app code with credentials, I forked the repository because I wanted my own copy on GitHub to safely experiment without affecting the original project.

A fork is different from a clone because a fork creates a separate copy of the repository on your GitHub account, while a clone makes a local copy on your computer. Forking lets you propose changes, experiment, and push updates independently of the original repository.

To connect my local repository to the forked repository, I added the forked repository as a remote. Then I used git add and git commit to stage and save my changes locally. Finally, git push uploads my committed changes from the local repository to the forked repository on GitHub.

GitHub blocked my push because the code contained exposed AWS credentials. This is a good security feature because it prevents sensitive information from being accidentally shared publicly, reducing the risk of unauthorized access to your AWS account.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-secretsmanager_o2p3q4r5)

---

## Secrets Manager

Secrets Manager is an AWS service that securely stores and manages sensitive information, such as passwords, API keys, and database credentials. I’m using it to store my AWS credentials for the web app safely instead of hardcoding them. Other common use cases include managing database passwords, API keys, third-party service tokens, and rotating secrets automatically to enhance security.

What is Secret Rotation?
Secret rotation is the process of automatically changing your secrets on a regular schedule. This is a security best practice because it reduces the risk of compromised credentials. If a secret is compromised, it will only be valid for a limited time before it's automatically rotated.

Secrets Manager can automatically rotate secrets for databases and other services. You can also configure custom rotation for other types of secrets.


💡 When should you use secret rotation?
Secret rotation is best for high-risk credentials like database passwords, privileged API keys, and service account credentials. These types of secrets, if compromised, could give attackers access to sensitive data or critical systems, so regular rotation helps limit the damage window.

However, you might want to skip rotation for keys (where automated rotation isn't supported), hardware security tokens, or legacy systems that can't handle credential updates without downtime.

Secrets Manager provides sample code in various languages, like Python, Java, and JavaScript. This is helpful because it allows developers to quickly integrate secret retrieval and management into their applications without having to write all the code from scratch, ensuring secure and consistent handling of sensitive information.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-secretsmanager_h2i3j4k5)

---

## Updating the web app code

I updated the config.py file to retrieve AWS credentials directly from AWS Secrets Manager instead of using hardcoded values.

The get_secret() function will:
-Connect to AWS Secrets Manager using the boto3 client.
-Retrieve the secret by its name.
-Parse the returned JSON string to extract the -AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, and any other stored credentials.
-Make these credentials available to the rest of the application at runtime.

This ensures that sensitive information is never stored in code or version control, significantly reducing the risk of exposure.

I also added code to config.py to extract the credentials from the retrieved secret and match them with the required string format expected by the application.

This is important because:

AWS services require credentials to follow an exact structure (e.g., "AWS_ACCESS_KEY_ID", "AWS_SECRET_ACCESS_KEY", and "AWS_REGION").

By extracting and matching them to the correct keys, the application can authenticate successfully without errors.

It ensures that even if the secret contains additional data, only the relevant, correctly formatted credentials are passed

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-secretsmanager_v0w1x2y3)

---

## Rebasing the repository

Git rebasing is the process of rewriting commit history so that changes appear in a straight, linear sequence rather than as multiple diverging branches.

I used it to reapply my local commits on top of the remote branch’s latest state before pushing.

This was necessary because:
-I wanted my GitHub branch to reflect my local changes without cluttering history with extra merge commits.
-Rebasing kept the commit log clean and easy to follow.
-It ensured that my push would not be rejected due to conflicts or diverging histories.

A merge conflict occurred during rebasing because both my local branch and the remote branch had changes to the same lines of code in the same file.
When this happens, Git can’t automatically decide which version to keep, so it pauses the rebase and asks you to resolve it manually.

I resolved the merge conflict by:
-Opening the affected file and reviewing the conflict markers (<<<<<<<, =======, >>>>>>>).
-Choosing which changes to keep — in some cases, I merged both sets of changes.
-Saving the file and running:
git add <filename>
git rebase --continue
-Testing the code to ensure it still worked as intended before continuing the rebase.

This ensured my local changes were preserved while still integrating updates from the remote branch cleanly.

Once the merge conflict was resolved, I verified that the rebase was successful by:
-Checking the repository on GitHub to confirm that:
->My latest changes appeared in the code.
->The commit history was clean and linear, showing my local commits on top of the remote updates.
-Reviewing the “Commits” tab to ensure no duplicate or unwanted commits remained after the rebase.

This confirmed that my local branch and the GitHub branch were perfectly in sync after the rebase.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-secretsmanager_t5u6v7w8)

---

---

# Encrypt Data with AWS KMS

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-kms)

**Author:** Manish Sharma  
**Email:** manishsharma7@hotmail.com

---

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-kms_w0x1y2z3)

---

## Introducing Today's Project!

In this project, I will demonstrate how to use AWS Key Management Service (KMS) to protect data stored in DynamoDB. The goal is to:

🔑 Create encryption keys with AWS KMS.
🗄️ Encrypt a DynamoDB database using a KMS key.
➕ Add and retrieve data from the database to test the encryption.
🕵️‍♀️ Observe how AWS blocks unauthorized access to the encrypted data.
💎 Grant a user encryption access so they can work securely with the protected data.

### Tools and concepts

Services I used include AWS KMS and DynamoDB. Key concepts I learnt include encryption, key management, IAM permissions, transparent data encryption, and controlling access to sensitive data using KMS policies.

### Project reflection

his project took me approximately 30 mins. The most challenging part was configuring the KMS key policies correctly to control access without affecting other users. It was most rewarding to see that the test user could only access the encrypted data after I granted the proper permissions, confirming that the encryption and access controls worked as intended.

I chose to do this project today because I wanted to learn how to securely encrypt data in DynamoDB using AWS KMS and practice managing access with IAM policies. This project met my goals because I successfully created an encrypted database, controlled access with a KMS key, and verified that only authorized users could access the data.

---

## Encryption and KMS

Encryption is the process of converting data into a secure format that can only be read by someone with the correct key. Companies and developers do this to protect sensitive information from unauthorized access or breaches. Encryption keys are digital codes used to lock (encrypt) and unlock (decrypt) the data.

AWS KMS (Key Management Service) is a managed service that lets you create, manage, and control encryption keys used to protect your data in AWS. Key management systems are important because they ensure encryption keys are stored securely, control who can use them, and help maintain compliance and data security.

Encryption keys are broadly categorized as symmetric and asymmetric. I set up a symmetric key because it uses a single key for both encryption and decryption, which is simpler and efficient for encrypting my DynamoDB data.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-kms_a2b3c4d5)

---

## Encrypting Data

My encryption key will safeguard data in DynamoDB, which is a fully managed NoSQL database service that stores and retrieves any amount of data with high availability and performance.

The different encryption options in DynamoDB include AWS owned keys, AWS managed keys (AWS/KMS), and customer managed keys (CMK). Their differences are based on who manages the key and the level of control you have over it. I selected a customer managed symmetric key because it gives me full control over the encryption and access policies for my data.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-kms_q8r9s0t1)

---

## Data Visibility

Rather than controlling who has access to the key, KMS manages user permissions by using key policies and IAM policies to define which users or roles can use the key to encrypt or decrypt data.

Despite encrypting my DynamoDB table, I could still see the table's items because DynamoDB uses transparent data encryption (TDE), which means the data is automatically encrypted at rest but is decrypted transparently when accessed by authorized users or applications.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-kms_c0d1e2f3)

---

## Denying Access

I configured a new IAM user to access my DynamoDB table for testing purposes. The permission policies I granted this user are full DynamoDB access, but not access to the KMS key used to encrypt the table.

After accessing the DynamoDB table as the test user, I encountered an access denied error when trying to view the encrypted data because the user did not have permissions for the KMS key. This confirmed that AWS correctly enforces encryption access separately from DynamoDB permissions, protecting the data from unauthorized users.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-kms_w0x1y2z3)

---

## EXTRA: Granting Access

To let my test user use the encryption key, I updated the KMS key policy as the administrator. My key's policy was updated to grant the test user permission to encrypt and decrypt data while keeping full administrative control with myself.

Using the test user, I retried accessing and decrypting items in the encrypted DynamoDB table. I observed that the user could now view and work with the data, which confirmed that updating the KMS key policy successfully granted the necessary permissions without changing other DynamoDB access controls.

Encryption secures data instead of relying solely on database permissions or network security. I could combine encryption with IAM policies and resource tagging to ensure that only authorized users can access sensitive data while maintaining fine-grained control over who can do what.

![Image](http://learn.nextwork.org/elated_cyan_peaceful_duck/uploads/aws-security-kms_feffb2fb8)

---

---

<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Encrypt Data with AWS KMS

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-kms)

**Author:** David Ivory  
**Email:** davidmivory@gmail.com

---

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-kms_w0x1y2z3)

---

## Introducing Today's Project!

In this project, I will demonstrate how to implement data encryption using AWS KMS (Key Management Service) to protect sensitive information. The goal is to create a secure data protection system by generating encryption keys through KMS, encrypting a DynamoDB database with those keys, and testing the encryption by adding and retrieving data. I'll explore how AWS prevents unauthorized access to encrypted data and learn to grant proper encryption permissions to users. This hands-on experience will show how encryption converts data into secure formats that only authorized users can decrypt and read, addressing the critical need for data security that costs companies millions when breached. Through practical implementation, I'll understand how encryption keys work as digital codes for securing and accessing data, demonstrating real-world data protection strategies used by companies to safeguard customer information and sensitive data from unauthorized access.

### Tools and concepts

Services I used include AWS KMS (Key Management Service) for creating and managing encryption keys, DynamoDB for storing encrypted data, and IAM (Identity and Access Management) for creating users and controlling access permissions. Key concepts I learnt include encryption, which converts readable data into secure ciphertext using cryptographic algorithms, symmetric encryption keys that use the same key for both encryption and decryption operations, key policies that explicitly define user permissions and access controls for KMS keys, encryption at rest which protects stored data in databases, and the principle of layered security where database access permissions are separate from encryption key permissions. I also learned about the deny-by-default security model where no user has KMS key access unless explicitly granted, and how transparent encryption allows authorized users to access data normally while maintaining protection against unauthorized access.

### Project reflection

This project took me approximately 30-45 minutes to complete, following the step-by-step process of creating the KMS key, setting up the encrypted DynamoDB table, adding test data, and configuring IAM users with different permission levels. The most challenging part was understanding how KMS key policies work with the deny-by-default approach and ensuring the test user had the right combination of DynamoDB permissions but not KMS access to properly demonstrate the encryption security. It was most rewarding to see the encryption protection work in action when the test user was denied access to the encrypted data, proving that the layered security model effectively protects sensitive information even from users with legitimate database permissions.

I am adding this documentation to my github to show my knowledge of AWS. All goals were met. 

---

## Encryption and KMS

Encryption is the process of converting readable data, plaintext, into an unreadable, scrambled format, called ciphertext, using mathematical algorithms and encryption keys. Companies and developers do this to protect sensitive information from unauthorized access, ensuring that even if data is intercepted, stolen, or accessed by malicious actors, it remains useless without the proper decryption key. This is especially critical for protecting customer data, financial information, personal records, and business secrets, helping organizations comply with privacy regulations and avoid costly data breaches. Encryption keys are the digital codes or passwords that control both the encryption and decryption processes, they're like master keys that lock and unlock the data, determining who can transform the scrambled ciphertext back into readable plaintext and access the original information.

AWS KMS is a managed encryption service that allows you to create, control, and manage cryptographic keys used to encrypt and decrypt data across AWS services and applications. It provides a centralized, secure platform for key lifecycle management, including key creation, rotation, deletion, and access control policies. Key management systems are important because they solve the critical challenge of securely storing, distributing, and managing encryption keys at scale,  without proper key management, even the strongest encryption becomes vulnerable if keys are compromised, lost, or mismanaged. KMS ensures that encryption keys are protected by hardware security modules, HSMs, automatically handles key rotation to maintain security over time, provides detailed audit logs for compliance requirements, and integrates seamlessly with other AWS services like DynamoDB, S3, and EC2 to enable transparent encryption without requiring developers to handle the complex cryptographic operations th

Encryption keys are broadly categorized as either symmetric or asymmetric keys, with fundamental differences in how they handle encryption and decryption operations. Symmetric keys use the same key for both encrypting and decrypting data, while asymmetric keys use a pair of mathematically related keys - a public key for encryption and a private key for decryption. I set up a symmetric key because it's the most efficient choice for database encryption scenarios like DynamoDB, where the same system needs to both encrypt data when storing it and decrypt data when retrieving it. Symmetric encryption is significantly faster than asymmetric encryption, making it ideal for high-volume database operations that require quick read and write performance. Additionally, AWS KMS uses symmetric keys by default for service integrations like DynamoDB encryption, as the key remains securely within the AWS environment and doesn't need to be shared externally, eliminating the complexity of key distributio

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-kms_a2b3c4d5)

---

## Encrypting Data

My encryption key will safeguard data in DynamoDB, which is a fully managed NoSQL database service provided by AWS that offers fast and predictable performance with seamless scalability. DynamoDB is designed to handle high-traffic applications by automatically scaling up or down based on demand, making it ideal for web applications, mobile backends, gaming, IoT, and other use cases that require consistent, single-digit millisecond latency at any scale.

The different encryption options in DynamoDB include AWS owned key, AWS managed key, or customer managed key Managing encrypted tables in DynamoDB - AWS Documentation. Their differences are based on who controls the encryption keys and access policies. The AWS-owned key is owned by DynamoDB and is used by default Enable Encryption at Rest with Amazon KMS Keys | Trend Micro with no customer control, the AWS managed key is created by AWS in your account with limited control, and the customer managed key provides full control over key policies and access permissions. I selected the customer managed key option because it offers the highest level of security control, allowing me to define exactly who can access the encryption key and demonstrate proper key management practices for securing sensitive database data.

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-kms_q8r9s0t1)

---

## Data Visibility

Rather than controlling who has access to the key, KMS manages user permissions by using key policies that explicitly define what actions each principal can perform with the key. No AWS principal, including the account root user or key creator, has any permissions to a KMS key unless they are explicitly allowed How Amazon DynamoDB uses AWS KMS - AWS Key Management Service in a key policy. Key administrators have permissions to manage the KMS key, but do not have permissions to use the KMS key in cryptographic operations DynamoDB Encryption - Everything You Need To Know [2025]. This means KMS uses a deny-by-default approach where permissions must be explicitly granted through key policies, differentiating between administrative permissions (managing the key) and usage permissions (encrypting/decrypting data) for fine-grained access control.

Despite encrypting my DynamoDB table, I could still see the table's items because I am logged in as my root account. I could also access it if I were logged in as the IAM user account. DynamoDB uses transparent data encryption, which means the data is secure and already decrypted. The data is accessible to those who should have access to it. 

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-kms_c0d1e2f3)

---

## Denying Access

I configured a new IAM user to test the encryption security by creating a user with database access but without encryption key permissions. The permission policies I granted this user are full DynamoDB access, allowing them to perform all database operations like reading, writing, updating, and deleting items from tables, but not access to the KMS key that encrypts the DynamoDB table. This configuration demonstrates the layered security approach where database permissions and encryption permissions are separate - the user can authenticate and has authorization to use DynamoDB services, but the encrypted data remains protected because they lack the KMS decrypt permissions needed to access the actual content stored in the encrypted table.


After accessing the DynamoDB table as the test user, I encountered Access denied to kms:Decrypt because this test user does not have access to the encrypted data. This confirmed encryption policy is working. 

![Image](http://learn.nextwork.org/glowing_rose_loyal_hind/uploads/aws-security-kms_w0x1y2z3)

---

## EXTRA: Granting Access

---

---

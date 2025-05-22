#  AWS-X-DevOps
##  Project Overview
In this project I'll build from scratch a CI/CD pipeline that automates the build and deployment of a web app. A **CI/CD (Continuous Integration and Continuous Deployment/Delivery)** pipeline is a tool that automates the steps from development (i.e. writing changes to code) to deployment (i.e. making the code changes available to users). This helps engineering teams build and release software much, much faster! That's why most modern tech companies use CI/CD pipelines to ship software to users, and why it's a crucial skill for DevOps Engineers.

---
##  Table Of Content
-  [Set Up a Web App in the Cloud](#Set-Up-a-Web-App-in-the-Cloud)

---

## Prerequisites


---

##  Set Up a Web App in the Cloud
  **Step - 1 : Set up an IAM user**
  
When you create an AWS account for the first time, the login you get is called the root user of the AWS account. AWS actually recommends to not use your root user for everyday tasks to protect it from security breaches. You should create IAM users instead. If a root user is a master key to your AWS account, think of IAM users as key copies. IAM users have separate usernames and passwords to your root user, and you can set them to have limited access to your account's resources.

-  Head to your AWS Account as the root user.
-  Open the **AWS IAM** console.
-  From the left hand navigation panel, choose **Users**.
-  Choose **Create user**.
-  For the User name, use `Yourname-IAM-Admin‍`
-  Make sure to select the checkbox next to **Provide user access to the AWS Management Console - optional**.‍

If you're prompted with a pop up panel that says **Are you providing access to a person?**, choose **I want to create an IAM user**.

![image alt](DevOps-1)

-  For the console password, choose **Custom password**.
-  Type in a password that you will be able to remember/access in the future.

![image alt](DevOps-2)

-  Deselect the checkbox for **Users must create a new password at next sign-in - Recommended**.

![image alt](DevOps-3)

-  Choose **Next**.
-  In the permissions set up page, choose **Attach policies directly**.
-  From the list of **Permissions policies**, select **AdministratorAccess**.
-  Choose **Next**.
-  Choose **Create user**.
-  Voilà - you've just created your new user! **Stay on this page**.

-  Choose **Download .csv file**.

![image alt](DevOps-4)

-  Copy the **Console sign-in URL**.

![image alt](DevOps-5)

-  Now you're ready to start using your IAM user. 🏁
-  Log out of your root user's AWS Account.
-  Paste and go to your copied console sign-in URL.
-  Open your downloaded .csv file containing your user's access instructions.
-  Log in using your IAM user's username and password in the .csv file.
-  Once you're logged in, you're ready to use your IAM user for this project! 

 **Step - 2 : Launch an EC2 Instance**

 Since we want your web app to be entirely created and run on the cloud, we'll use a virtual server (EC2 instance) to house our development work.

-  Switch your **Region** to the one closest to you.

![image alt](DevOps-6)

-  Head to **Amazon EC2** in your AWS Management Console.

Amazon EC2 (Elastic Compute Cloud) is a service that lets you rent and use virtual computers in the cloud. They're like your personal computers, but they exist on the internet instead of being physically in front of you. You can create, customize, and use these computers for all different reasons, from running applications to hosting websites.

-  In your EC2 console, select **Instances** from the left hand navigation panel.
-  Choose **Launch instances**.

If EC2 is the service that creates virtual computers/servers, an instance is one of those computers/servers. Picking an EC2 instance is like customising a virtual computer that fits what you need for your project. You can customize your EC2 instance's CPU, memory, storage, and networking capacity and more!

-  In **Name**, enter the value `nextwork-devops-yourname`.
-  Choose **Amazon Linux 2023 AMI** under **Amazon Machine Image(AMI)**.
-  Leave **t2.micro** under **Instance type**.
-  Under **Key pair (login)**, choose **Create a new key pair**.

A key pair in EC2 is like the keys to your virtual computer. It's made of two halves: a public key that AWS keeps, and a private key that you download. When you use the private key, it verifies that you're the one allowed to access that specific virtual machine, keeping everything secure and just for you.

-  Use `nextwork-keypair` as your key pair's name.
-  Keep the **Key pair type** as **RSA**, and the **Private key file format** as **.pem**

The key pair type determines the algorithm used for generating the key pair's cryptographic keys. We use **RSA (Rivest-Shamir-Adleman)**, which is one of the most common cryptographic algorithms used due to its strength and security. RSA is widely supported and trusted for creating digital signatures and encrypting data.

-  Select **Create key pair**.

![image alt](DevOps-7)

-  A new file will automatically download to your local computer - This is your private key. Before we lose track of our .pem file, let's organise it in our computer. Head to your local computer's desktop. Create a new folder in your desktop called **DevOps**.
-  Move your .pem file from your **Downloads** folder into your **DevOps** folder.
-  Back to our EC2 instance setup, head to the **Network settings** section.
-  For **Allow SSH traffic from**, select the dropdown and choose **My IP**. This makes sure only you can access your EC2 instance.

SSH i.e. **Secure Shell** is a protocol used to make sure only authorized users can access a remote server. When you connect to your EC2 instance later in this project, SSH verifies you have the correct private key that matches the public key on the server.
SSH is also a type of network traffic. Once SSH has authorized you, it'll set up a secure connection between you and the EC2 instance. All data transferred (including your commands and the responses from the instance) gets encrypted. This encryption makes SSH an ideal method for working with virtual servers!

-  Leave the default values for the remaining sections.

![image alt](DevOps-8)

-  When you're ready, choose **Launch instance**.

**Step - 3 : Connect to your EC2 Instance**

Our EC2 instance is working and we'll use VS Code to connect to our EC2 instance. Once we're connected, we can work inside your EC2 instance to set up that web app.

-  Open **VS Code** in your local computer.
-  Select **Terminal** from the top menu bar.
-  Select **New Terminal** from the dropdown.

![image alt](DevOps-9)

A terminal is where you send instructions to your computer using text instead of clicks. Every computer has a terminal. On Windows, it's often called Command Prompt or PowerShell, while macOS and Linux systems use Terminal.

-  Navigate your terminal to the DevOps folder. You'll do this by entering this command in the terminal:

```bash
  cd ~/Desktop/DevOps
```

-  Once you’re in the DevOps folder, you might want to check if your .pem file is there.
-  Use `ls` (Mac/Linux) or `dir` (Windows).
-  Change the permissions of your .pem file: In the terminal, run the following command to allow access to your .pem file.

```bash
  chmod 400 nextwork-keypair.pem
```

This command stands for "**change mode**", and it changes the permissions of your .pem file. Using `400` makes it readable only by you (the owner) and restricts access for everyone else. We're changing the permissions of your .pem file so that you have access to it when you connect to your EC2 instance later. Blocking out everyone else keeps your .pem file i.e. your secret key secure.

-  Head back to your AWS Management Console.
-  Click on **Instances** from the left hand navigation panel.
-  Click on the checkbox next to your EC2 instance to view its details.
-  Under the **Details** tab, look for **Public IPv4 DNS**. You'll need this in the next instruction!
-  We'll need this DNS in a second - so keep this tab open!

A Public IPv4 DNS (which stands for Domain Name System) is the public address for your EC2 server that the internet uses to find and connect to it. The local computer you're using to do this project will find and connect to your EC2 instance through this IPv4 DNS.

![image alt](DevOps-10)

-  Now we'll connect to our instance via SSH.
-  Head back to VS Code and open your terminal again.
-  Use the following command to connect to your EC2 instance: `ssh -i [PATH TO YOUR .PEM FILE] ec2-user@[YOUR PUBLIC IPV4 DNS]`
    -  Replace **[PATH TO YOUR .PEM FILE]** with the actual path to your private key file (e.g., ~/Desktop/DevOps/nextwork-keypair.pem). Delete the square brackets!
    -  Replace **[YOUR PUBLIC IPV4 DNS]** with the Public DNS you just found. Delete the square brackets!
-  Your terminal will ask if you want to continue connecting to this EC2 instance. This is SSH's way of asking if you trust this server.
-  Enter `yes` to continue connecting.
-  Congrats! You've connected your EC2 instance via SSH.

![image alt](DevOps-11)


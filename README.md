#  AWS-X-DevOps
##  Project Overview
In this project I'll build from scratch a CI/CD pipeline that automates the build and deployment of a web app. A **CI/CD (Continuous Integration and Continuous Deployment/Delivery)** pipeline is a tool that automates the steps from development (i.e. writing changes to code) to deployment (i.e. making the code changes available to users). This helps engineering teams build and release software much, much faster! That's why most modern tech companies use CI/CD pipelines to ship software to users, and why it's a crucial skill for DevOps Engineers.

---
##  Table Of Content
-  [Set Up a Web App in the Cloud](#set-up-a-web-app-in-the-cloud)
-  [Connect a GitHub Repo with AWS](#connect-a-github-repo-with-aws)
-  [Secure Packages with CodeArtifact](#secure-packages-with-codeartifact)
-  [Continuous Integration with CodeBuild](#continuous-integration-with-codebuild)
-  [Deploy a Web App with CodeDeploy](#deploy-a-web-app-with-codedeploy)
-  [Build a CI/CD Pipeline with AWS](#build-a-ci-cd-pipeline-with-aws)

---
##  Set Up a Web App in the Cloud
**Step - 1 : Set up an IAM user**

When we create an AWS account for the first time, the login we get is called the root user of the AWS account. AWS actually recommends to not use our root user for everyday tasks to protect it from security breaches. We should create IAM users instead. If a root user is a master key to our AWS account, think of IAM users as key copies. IAM users have separate usernames and passwords to our root user, and we can set them to have limited access to our account's resources.
-  Head to our AWS Account as the root user.
-  Open the **AWS IAM** console.
-  From the left hand navigation panel, choose **Users**.
-  Choose **Create user**.
-  For the User name, use `Yourname-IAM-Admin‍`
-  Make sure to select the checkbox next to **Provide user access to the AWS Management Console - optional**.‍

If we're prompted with a pop up panel that says **Are you providing access to a person?**, choose **I want to create an IAM user**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-1.png)

-  For the console password, choose **Custom password**.
-  Type in a password that we will be able to remember/access in the future.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-2.png)

-  Deselect the checkbox for **Users must create a new password at next sign-in - Recommended**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-3.png)

-  Choose **Next**.
-  In the permissions set up page, choose **Attach policies directly**.
-  From the list of **Permissions policies**, select **AdministratorAccess**.
-  Choose **Next**.
-  Choose **Create user**.
-  Voilà - we've just created our new user! **Stay on this page**.
-  Choose **Download .csv file**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-4.png)

-  Copy the **Console sign-in URL**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-5.png)

-  Now we're ready to start using our IAM user.
-  Log out of our root user's AWS Account.
-  Paste and go to our copied **console sign-in URL**.
-  Open our downloaded **.csv** file containing our user's access instructions.
-  Log in using our IAM user's username and password in the .csv file.
-  Once we're logged in, we're ready to use our IAM user for this project! 

 **Step - 2 : Launch an EC2 Instance**

Since we want our web app to be entirely created and run on the cloud, we'll use a virtual server (EC2 instance) to house our development work.

-  Switch our **Region** to the one closest to us.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-6.png)

-  Head to **Amazon EC2** in our AWS Management Console.

**Amazon EC2 (Elastic Compute Cloud)** is a service that lets us rent and use virtual computers in the cloud. They're like our personal computers, but they exist on the internet instead of being physically in front of us. We can create, customize, and use these computers for all different reasons, from running applications to hosting websites.

-  In our EC2 console, select **Instances** from the left hand navigation panel.
-  Choose **Launch instances**.

If EC2 is the service that creates virtual computers/servers, an **instance** is one of those computers/servers. Picking an EC2 instance is like customising a virtual computer that fits what we need for our project. We can customize our EC2 instance's CPU, memory, storage, and networking capacity and more!

-  In **Name**, enter the value `nextwork-devops-yourname`.
-  Choose **Amazon Linux 2023 AMI** under **Amazon Machine Image(AMI)**.
-  Leave **t2.micro** under **Instance type**.
-  Under **Key pair (login)**, choose **Create a new key pair**.

A **key pair** in EC2 is like the keys to our virtual computer. It's made of two halves: a public key that AWS keeps, and a private key that we download. When we use the private key, it verifies that we're the one allowed to access that specific virtual machine, keeping everything secure and just for us.

-  Use `nextwork-keypair` as our key pair's name.
-  Keep the **Key pair type** as **RSA**, and the **Private key file format** as **.pem**

The **key pair type** determines the algorithm used for generating the key pair's cryptographic keys. We use **RSA (Rivest-Shamir-Adleman)**, which is one of the most common cryptographic algorithms used due to its strength and security. RSA is widely supported and trusted for creating digital signatures and encrypting data.

-  Select **Create key pair**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-7.png)

-  A new file will automatically download to our local computer - This is our private key. Before we lose track of our .pem file, let's organise it in our computer. Head to our local computer's desktop. Create a new folder in your desktop called **DevOps**.
-  Move our .pem file from our **Downloads** folder into our **DevOps** folder.
-  Back to our EC2 instance setup, head to the **Network settings** section.
-  For **Allow SSH traffic from**, select the dropdown and choose **My IP**. This makes sure only we can access our EC2 instance.

SSH i.e. **Secure Shell** is a protocol used to make sure only authorized users can access a remote server. When we connect to our EC2 instance later in this project, SSH verifies we have the correct private key that matches the public key on the server.
SSH is also a type of network traffic. Once SSH has authorized us, it'll set up a secure connection between us and the EC2 instance. All data transferred (including our commands and the responses from the instance) gets encrypted. This encryption makes SSH an ideal method for working with virtual servers!

-  Leave the default values for the remaining sections.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-8.png)

- Finally, click **Launch instance** at the bottom right of the page.

**Step - 3 : Connect to our EC2 Instance**

Our EC2 instance is working and we'll use VS Code to connect to our EC2 instance. Once we're connected, we can work inside our EC2 instance to set up that web app.

-  Open **VS Code** in our local computer.
-  Select **Terminal** from the top menu bar.
-  Select **New Terminal** from the dropdown.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-9.png)

A terminal is where we send instructions to our computer using text instead of clicks. Every computer has a terminal. On Windows, it's often called Command Prompt or PowerShell, while macOS and Linux systems use Terminal.

-  Navigate our terminal to the DevOps folder. We'll do this by entering this command in the terminal:

```bash
  cd ~/Desktop/DevOps
```

-  Once we’re in the **DevOps** folder, we might want to check if our **.pem** file is there.
-  Use `ls` (Mac/Linux) or `dir` (Windows).
-  Change the permissions of our **.pem** file: In the terminal, run the following command to allow access to our .pem file.

```bash
  chmod 400 nextwork-keypair.pem
```

This command stands for "**change mode**", and it changes the permissions of our .pem file. Using `400` makes it readable only by us (the owner) and restricts access for everyone else. We're changing the permissions of our .pem file so that we have access to it when we connect to our EC2 instance later. Blocking out everyone else keeps our .pem file i.e. our secret key secure.

-  Head back to our AWS Management Console.
-  Click on **Instances** from the left hand navigation panel.
-  Click on the checkbox next to our EC2 instance to view its details.
-  Under the **Details** tab, look for **Public IPv4 DNS**. We'll need this in the next instruction!
-  We'll need this DNS in a second - so keep this tab open!

A Public IPv4 DNS (which stands for Domain Name System) is the public address for our EC2 server that the internet uses to find and connect to it. The local computer we're using to do this project will find and connect to our EC2 instance through this IPv4 DNS.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-10.png)

-  Now we'll connect to our instance via SSH.
-  Head back to **VS Code** and open our terminal again.
-  Use the following command to connect to our EC2 instance:
`ssh -i [PATH TO YOUR .PEM FILE] ec2-user@[YOUR PUBLIC IPV4 DNS]`
    -  Replace **[PATH TO YOUR .PEM FILE]** with the actual path to our private key file (e.g., ~/Desktop/DevOps/nextwork-keypair.pem). Delete the square brackets!
    -  Replace **[YOUR PUBLIC IPV4 DNS]** with the Public DNS we just found. Delete the square brackets!
-  Our terminal will ask if we want to continue connecting to this EC2 instance. This is SSH's way of asking if we trust this server.
-  Enter `yes` to continue connecting.
-  Congrats! We've connected our EC2 instance via SSH.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-11.png)

**Step - 4 : Install Apache Maven and Amazon Corretto 8**

**Apache Maven** is a tool that helps developers build and organize Java software projects. It's also a package manager, which means it automatically download any external pieces of code our project depends on to work. It uses something called **archetypes**, which are like templates, to lay out the foundations for different types of projects e.g. web apps. We'll use Maven later on to help us set up all the necessary web files to create a web app structure, so we can jump straight into the fun part of developing the web app sooner.

- Install Apache Maven using the commands below. We can copy and paste **all** of these lines into the terminal together, no need to run them line by line.

```bash
wget https://archive.apache.org/dist/maven/maven-3/3.5.2/binaries/apache-maven-3.5.2-bin.tar.gz

sudo tar -xzf apache-maven-3.5.2-bin.tar.gz -C /opt

echo "export PATH=/opt/apache-maven-3.5.2/bin:$PATH" >> ~/.bashrc

source ~/.bashrc
```

- Once we've pasted these commands, don't forget to press **Enter** on our keyboard.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-12.png)

- Now we're going to install Java 8, or more specifically, **Amazon Correto 8**.

**Java** is a popular programming language used to build different types of applications, from mobile apps to large enterprise systems. Maven, which we just downloaded, is a tool that NEEDS Java to operate. So if we don't install Java, we won't be able to use Maven to generate/build our web app. **Amazon Corretto 8** is a version of Java that we're using for this project. It's free, reliable and provided by Amazon.

- Run these commands:

```bash
sudo dnf install -y java-1.8.0-amazon-corretto-devel

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-amazon-corretto.x86_64

export PATH=/usr/lib/jvm/java-1.8.0-amazon-corretto.x86_64/jre/bin/:$PATH
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-13.png)

- To verify that Maven is installed correctly, run the following command next: `mvn -v`

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-14.png)

- To verify that we've installed Java 8 correctly, run this next: `java -version`

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-15.png)

**Step - 5 : Create the Application**

We've assembled both Maven and Java into our EC2 instance. Now let's cut straight to generating the web app!

- Run Maven commands in our terminal to generate a Java web app.
- Use **mvn** to generate a Java web app. To do this, use these commands:

```bash
mvn archetype:generate \
   -DgroupId=com.nextwork.app \
   -DartifactId=nextwork-web-project \
   -DarchetypeArtifactId=maven-archetype-webapp \
   -DinteractiveMode=false
```

When we run **mvn** commands, we're asking Maven to perform tasks (like creating a new project or building an existing one). The **mvn archetype:generate** command specifically tells Maven to create a new project from a template (which Maven calls an archetype). This command sets up a basic structure for our project, so we don't have to start from scratch.

- Watch out for a **BUILD SUCCESS** message in our terminal once our application is all set up.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-16.png)

Now we'll connect VS Code to our EC2 instance so we can see and edit the web app we've just created. Earlier we connected with SSH in the terminal lets us send text commands to our EC2 instance, but we don't get all the benefits of having an **IDE** like VS Code. When we connect VS Code itself to our EC2 instance (not just our terminal), it unlocks VS Code’s **IDE features (like file navigation and code editing)** directly on our EC2 instance. This will make it so much easier for us to edit and manage yur web app.

- Click on the **Extensions** icon at the side of our VS Code window.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-17.png)

- In the search bar, type `Remote - SSH` and click **Install** for the extension.

**Remote - SSH extension** in VS Code lets us connect directly via SSH to another computer securely over the internet. This lets us use VS Code to work on files or run programs on that server as if we were doing it on our own computer, which will come in handy when we edit the web app in our EC2 instance!

- Click on the **double arrow** icon at the **bottom left corner** of our **VS Code window**. This button is a shortcut to use **Remote - SSH**.
- Select **Connect to Host...**
- Select **+ Add New SSH Host...**

An **SSH Host** is the computer or server we're connecting to using SSH. It's the target location where we want to run commands or manage files; in our case, the SSH Host is the EC2 instance we created.

- Enter the SSH command we used to connect to our EC2 instance: `ssh -i [PATH TO YOUR .PEM FILE] ec2-user@[YOUR PUBLIC IPV4 DNS]`
  - Replace **[PATH TO YOUR .PEM FILE]** with the actual path to our private key file (e.g., ~/Desktop/DevOps/nextwork-keypair.pem). Delete the square brackets!
  - Replace **[YOUR PUBLIC IPV4 DNS]** with the Public DNS we just found. Delete the square brackets!
- Select the configuration file at the top of our window. It should look similar to `/Users/username/.ssh/config`
- A **Host added!** popup will confirm that we've set up our SSH Host.
- Select the blue **Open Config** button on that popup.
- Confirm that all the details in our configuration file look correct:
  - **Host** should match up with our EC2 instance's IPv4 DNS.
  - **IdentityFile** should match up to nextwork-keypair.pem's location in our local computer.
  - **User** should say ec2-user

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-18.png)

- Now we’re ready to connect VS Code with our EC2 instance!
- Click on the double arrow button on the bottom left corner and select **Connect to Host** again.
- We should now see our EC2 instance listed at the top.
- Select the EC2 instance and off we go to a new VS Code window.
- Check the bottom right hand corner of our new VS Code window - it should show our EC2 instance's IPV4 DNS.

Now let's open up our web app's files.
- From VS Code's left hand navigation bar, select the **Explorer** icon.
- Select **Open folder**.
- At the top of our VS Code window, we should see a drop down of different file and folder names. Ooooo, this is VS Code asking us which specific file/folder you'd like to open!
- Enter `/home/ec2-user/nextwork-web-project`.
- Press **OK**.
- VS Code might show us a popup asking if we trust the authors of the files in this folder. If we see this popup, select **Yes, I trust the authors**.
- Check our VS Code window's file explorer again - a folder called **nextwork-web-project** is here!
- Try expanding all the subfolders in the file explorer. All folders have a `>` icon next to their name.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-19.png)

All the files and subfolders we see under **nextwork-web-project** are parts of a web app! We can start working right away on the content we want to display on our web app, since Maven's taken care of the basic structuring and setup.

- Exploring done! So how can VS Code help us edit our application files? Let's find out.
- From our file explorer, click into **index.jsp**.

**index.jsp** is a file used in Java web apps. It's similar to an HTML file because it contains markup to display web pages. However, index.jsp can also include Java code, which lets it generate dynamic content. This means content can change depending on things like user input or data from a database. Social media apps are great examples of web apps because the content we see is always changing, updating and personalised to us. HTML files are static and can’t include Java code. That's why it's so important to install Java in our EC2 instance - so we can run the Java code in our web app!

- Welcome to editor view of **index.jsp**. Now we're really using VS Code's IDE abilities - editing code is much easier here than in the terminal.
- Let's try modifying index.jsp by changing the placeholder code to the code snippet below. Don't forget to replace **{YOUR NAME}** from the following code with our name:

```html
<html>

<body>

<h2>Hello {YOUR NAME}!</h2>

<p>This is my NextWork web application working!</p>

</body>

</html>
```

- Save the changes we've made to **index.jsp** by selecting **Command/Ctrl + S** on our keyboard.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-20.png)

---

## Connect a GitHub Repo with AWS

**Git repositories** are essential in a **CI/CD pipeline** because they store our code safely in the cloud, and tracks every change our team makes to the code! Using a shared repository makes it much easier for engineering teams to hand off code, collaborate and update each other on changes they've made.

**Step - 1 : Set up GitHub**

Now that our development environment is ready, the next step is to set up **Git** on our EC2 instance. Git is like a time machine and filing system for our code. It tracks every change we make, which lets us go back to an earlier version of our work if something breaks. We can also see who made specific changes and when they were made, which makes teamwork/collaboration a lot easier. Git is often called a **version control system** since it tracks our changes by taking snapshots of what our files look like at specific moments, and each snapshot is considered a 'version'.

- Install **Git** on our **EC2 instance**.
- Open our EC2 instance's terminal.
- In the terminal, run these commands to install Git:

```bash
sudo dnf update -y
sudo dnf install git -y
```

- Verifiy the installation:

```bash
git --version
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-21.png)

**GitHub** is a place for engineers to store and share their code and projects online. It's called GitHub because it uses Git to manage our projects' version history.

- Head to **GitHub's signup** page.
- Follow the prompts to create our account by entering our **email**, creating a **password**, and choosing a **username**.
- Complete one of their **bot verification tasks**b. Switch the task type to Audio if the Visual task crashes our website.
- Once our account is created, confirm our email address with a verification code sent to our inbox.
- Log into our GitHub account once we've verified our email.
- Welcome to our GitHub account!

If **Git** is the tool for tracking changes, think of **GitHub** as a storage space for different version of our project that Git tracks. Since GitHub is a cloud service, it also lets us access our work from anywhere and collaborate with other developers over the internet. Even though our code is on a cloud server like EC2, GitHub helps us to use Git and see our file changes in a more user-friendly way. It's just like how using an IDE (VSCode) makes editing code easy. GitHub is also especially useful in situation where we're working in teams and need to share our updates and reviews to a shared code base.

Nice, we're ready to set up a **new repository on GitHub**! To store our code using Git, we create repositories (aka 'repos'), which are folders that contain all our project files and their entire version history. Hosting a repo in the cloud, like on GitHub, means we can also collaborate with other engineers and access our work from anywhere.

- After signing in to **GitHub**, click on the `+` icon next to our GitHub profile icon at the top right hand corner.
- Select **New repository**.
- Select **Create repository**.
- This loads up a new page where we can create a repository.
- Under **Owner**, click on the **Choose an owner dropdown** and select our GitHub username.
- Under **Repository name**, enter **nextwork-web-project**
- For the **Description**, enter `Java web app set up on an EC2 instance`.
- Choose **Your visibility** settings. We'd recommend selecting **Public** to make our repository available for the world to see.
- Select **Create repository**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-22.png)

**Step - 2 : Commit and Push Your Changes to GithHub**

So now we have a place in the cloud that will store our code and track the changes we make. But this storage folder (our GitHub repo) still doesn't know where our web app files are. Lets connect our GitHub repo with our web app project stored in our EC2 instance.

- Head back to our **VSCode remote window**. Make sure it's still SSH connected to our EC2 instance by checking the bottom left corner.
- Check that we are in the right folder by running this command in our terminal: `pwd`
- If not in nextwork-web-project, use `cd` to navigate terminal into our web app project.
- Now let's tell Git that we'd like to track changes made inside this project folder.

```bash
git init
```

To start using **Git** for our project, we need to create a **local repository** on our computer. When we run `git init` inside a directory e.g. nextwork-web-project, it sets up the directory as a **local Git repository** which means changes are now tracked for version control. The local repository is where we use Git directly on our own EC2 instance. The edits we make in our local repo is only visible to us and isn't shared with anyone else yet. This is different to the GitHub repository, which is the remote/cloud version of our repo that others can see.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-23.png)

This is just Git giving us a heads-up about naming our main branch **master** and suggesting that we can choose a different name like 'main' or 'development' if we want. We can think of Git branches as parallel versions or 'alternate universes' of the same project. For example, if we wanted to test a change to our code, we can set up a new branch that lets us diverge from the original/main version of our code (called master) so we can experiment with new features or test bug fixes safely. We won't create new branches in this project and we'll save all new changes directly to master, but it's best practice to make all changes in a separate branch and then merge them into master when they're ready.

- Head back to our GitHub repository's page.
- In the blue section of the page titled **Quick setup — if you’ve done this kind of thing before**, copy the **HTTPS URL** to our repository page. It will look like `https://github.com/username/nextwork-web-project.git`
- Head back to our terminal in VSCode.
- Run this command. Don't forget to replace **[YOUR GITHUB REPO LINK]** with the link we've just copied.

```bash
git remote add origin [YOUR GITHUB REPO LINK]
```

Our local and GitHub repositories aren't automatically linked, so we'll need to connect the two so that updates made in our local repo can also reflect in our GitHub repo. When we set `remote add origin`, we're telling Git where our GitHub repository is located. Think of **origin** as a bookmark for our GitHub project's URL, so we don't have to type it out every time we want to send our changes there.

- To verify that the remote origin has been set up correctly, run the command:

```bash
git remote -v
```

- This command lists all configured remote repositories!
- We should see `origin` listed, with both `fetch` and `push` URLs pointing to our GitHub repository URL.

Next, we'll save our changes and push them into GitHub.
- Run this command in our terminal:

```bash
git add .
```

`git add .` stages all (marked by the '.') files in nextwork-web-project to be saved in the next version of our project. When we stage changes, we're telling Git to put together all our modified files for a final review before we commit them. This is incredibly handy because we get to see all our edits in one spot, which means its much easier to check if there were mistakes or unwanted changes before we commit.

- Run this command next in our terminal:

```bash
git commit -m "Updated index.jsp with new content"
```

`git commit -m "Updated index.jsp with new content"` saves the staged changes as a snapshot in our project’s history. This means our project's version control history has just saved our latest changes in a new version. `-m` flag lets us leave a **message** describing what the commit is about, making it easier to review what changed in this version.

- Finally, run this command:

```bash
git push -u origin master
```

`git push -u origin master` uploads i.e. 'pushes' our committed changes to origin, which we've bookmarked as our GitHub repo. 'master' tells Git that these updates should be pushed to the master branch of our GitHub repo. By using `-u` we're also setting an 'upstream' for our local branch, which means we're telling Git to remember to push to master by default. Next time, we can simply run `git push` without needing to define origin and master.

- But Git can't push our work to the Github repository yet. It's now asking for a username!
- Enter our Github username, and press **Enter** on our keyboard.
- Next, enter our password. We'll notice that as we type this out, nothing shows on our terminal. This is totally expected - our terminal is hiding our input for our privacy. Press **Enter** on our keyboard when we've typed out our password, even if we don't see it printed out in our terminal.
- Hmmmm, now Git is letting us know that it can't actually accept our password.

**GitHub phased out password authentication** to connect with repositories over HTTPS - there are too many security risks and passwords can get intercepted over the internet. We need to use a `personal access token` instead, which is a more secure method for logging in and interacting with our repos. A token in GitHub is a unique string of characters that looks like a random password. For example, a GitHub token might look like **ghp_xHJNmL16GHSZSV88hjP5bQ24PRTg2s3Xk9ll**. As we can imagine, tokens are great for security because they're unique and would be very hard to guess.

Now that we know passwords won't work for authentication, we'll have to find a replacement. Let's generate an authentication token on GitHub!
- Head back into our browser with GitHub open.
- Select our profile icon and select **Settings**.
- Select **Developer settings** at the very bottom of the left hand navigation panel.
- Select **Personal access tokens**.
- Select **Tokens (classic)**.
- Select **Generate new token**.
- Select **Generate new token (classic)**.
- Give our token a descriptive note, like `Generated for EC2 Instance Access. This is a part of NextWork's DevOps Project`.
- Lower the token expiration limit from 30 days to **7 days**.
- Select the checkbox next to **repo**.
- Select **Generate token**.
- Make sure to copy our token now. Keep it safe somewhere else, we won't be able to see our token once we close this tab.
- Head to our VS Code terminal.
- Run `git push -u origin master` again, which will trigger Git to ask for our GitHub username.
- Enter our username again.
- When Git asks for our password, **paste in our token instead**. Our terminal won't show our password for privacy reasons, so press **Enter** on our keyboard once we've pasted our token (even if we don't see it on screen).
- Looks like Github recognises our token and pushed our changes to our repository.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-24.png)

This message appears when we successfully push changes to a GitHub repository. It shows the progress of transferring objects (like files and commits), how many objects were processed, and tells us that our local branch is now tracking the remote branch after the push.

- Head to our GitHub repository in our web browser.
- **Refresh** the page, and we’ll see our web app files in the repository, along with the commit message we wrote.

When we add, commit and push our changes, we might notice the terminal automatically sets two other things - our name and email address - before it asks for our GitHub username.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-25.png)

Git needs author information for commits to track who made what change. If we don't set it manually, Git uses the system's default username, which might not accurately represent our identity in our project's version history.

- Run `git log` to see our history of commits, which also mentions the commit author's name.
- **EC2 Default User** isn't really our name, and the EC2 instance's IPv4 DNS is not our email. Let's configure our local Git identity so Git isn't using these default values.
- Run these commands in our terminal to manually set our name and email. Don't forget to replace **Your name** with our name (keep the quote marks), and **you@nextwork.org** with our email address.

```bash
git config --global user.name "Your Name"
git config --global user.email you@nextwork.org
```

We've set up our local Git identity, which means Git can associate our changes in the local repo to our name and email. This setup is best practice for keeping a clear history of who made which changes.

So we've learnt how to link our EC2 instance's files with a cloud repo, now let's see what happens when we make **new** changes to our web app files. We'll edit **index.jsp** again using VSCode, and run commands that pushes those changes to our GitHub repository too.

- Keep our GitHub page open, and switch back to our EC2 instance's VSCode window.
- Find **index.jsp** in our file navigator on the left hand panel.
- Find the line that says **This is my NextWork web application working!** and add this line below:

```html
<p>If you see this line in Github, that means your latest changes are getting pushed to your cloud repo :o</p>
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-26.png)

- Save our changes by pressing **Command/Ctrl + S** on our keyboard while keeping our **index.jsp** editor open.
- Head back to our GitHub tab and click into the **src/main/webapp** folders to find index.jsp.
- Click into **index.jsp** - have there been any updates to index.jsp in our GitHub repo?

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-27.png)

We won't see our changes in GitHub yet, because saving changes in our VSCode environment only updates our **local** repository. Remember that the local repository in VSCode is separate from our GitHub repository in the cloud. To make our changes visible in GitHub, we need to write commands that send (push) them from our local repository into our origin.

- Head back to our VSCode window.
- In the terminal, let's stage our changes: `git add .`
- Ready to see what changes are staged? Run `git diff --staged` next.

`git diff --staged` shows us the exact changes that have been staged compared to the last commit. Now we get to review our modifications in our code that we are about to save into our local repo's version history!

- Nice, these changes are what we want to save and send to GitHub, lets do that with these commands:

```bash
git commit -m "Add new line to index.jsp"
git push
```

- We might need to enter our username and token again to complete our push.

Right after we enter our username and token again, we can run `git config --global credential.helper store` to ask Git to remember our credential details for next time!

- Head back to our GitHub tab and refresh it - do we see our changes now?

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-28.png)

---

## Secure Packages with CodeArtifact

When building apps, we don't create everything from scratch. Instead, we often use pre-made "packages" (chunks of code) that other developers have already created. **CodeArtifact is an artifact repository**, which means we use it to store all of our app's packages in one place. It's an important part of a CI/CD pipeline because it makes sure an engineering team is always using the same, verified versions of packages when building and deploying the app, which reduces errors and security risks!

**Step - 1 : Set Up AWS CodeArtifact Repository**

Now, let's set up **AWS CodeArtifact**, a fully managed **artifact repository service**. We'll use it to store and manage our project's dependencies, ensuring secure and reliable access to Java packages. This is important because CodeArtifact provides a centralized, secure, and scalable way to manage dependencies for our Java projects, improving build consistency and security.

- Head back to the AWS Management Console.
- Head to the `CodeArtifact` service.
- In the CodeArtifact console, in the left-hand menu, click on **Repositories**.
- Click the **Create repository** button to start creating a new repository.
- On the **Create repository** page, head to the **Repository configuration** section.
- In the **Repository name** field, enter `nextwork-devops-cicd`.
- In the **Repository description - optional** field, enter:
`This repository stores packages related to a Java web app created as a part of NextWork's CI/CD Pipeline series.`
- Under **Public upstream repositories - optional**, select the checkbox next to `maven-central-store`.
- This will configure **Maven Central** as an upstream repository for your CodeArtifact repository.

**Upstream repositories** are like **backup libraries** that our primary repository can access when it doesn't have what we need. If we didn't set up CodeArtifact or have an upstream repository, our build would fail because a package is missing! When our application looks for a package that isn't in our CodeArtifact repository, CodeArtifact will check its upstream repositories (like Maven Central in our case) to find it. Once found, Maven will then store a copy in our CodeArtifact repository for future use.

**Maven Central** is essentially the App Store of the Java world - it's the most popular public repository where developers publish and share Java libraries. When we're building Java applications, chances are we'll need packages from Maven Central. It contains virtually every popular open-source Java library out there, from database connectors to testing frameworks and UI components.

By connecting our **CodeArtifact repository** to **Maven Central**, we're setting up a system where we get the best of both worlds: access to all these public libraries, but with the added benefits of caching, control, and consistency that come with our private CodeArtifact repository.

- Click **Next**.
- We're now ready to set up our CodeArtifact domain!

CodeArtifact domain is like a folder that holds multiple repositories belonging to the same project or organization. We like using domains because they give you a single place to manage permissions and security settings that apply to all repositories inside it. This is much more convenient than setting up permissions for each repository separately, especially in large companies where many teams need access to different repositories. With domains, you can ensure consistent security controls across all your package repositories in an efficient way.

- Under **Domain selection**, choose **This AWS account**.
- Under **Domain name**, enter `nextwork`.
- Click **Next** to proceed.
- Review the details of our repository configuration, including the package flow at the top.

**Package flow diagram** shows us exactly how dependencies will travel to our application. When our project needs a dependency, it first looks in our CodeArtifact repository. If the package is already there, great! It uses that version. If not, CodeArtifact automatically reaches out to Maven Central to fetch it. The first time we request a package, it might take a moment longer as CodeArtifact fetches it from Maven Central. But every build afterwards will be faster because the package is now cached in our repository.

- Select the **Create repository** button to create our CodeArtifact repository.
- We'll be taken to the repository's details page.
- We should see a success message at the top of the page, telling us that the CodeArtifact repository `nextwork-devops-cicd` has been successfully created.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-29.png)

**Step - 2 : Create and Attach an IAM Policy for CodeArtifact Access**

For Maven to start working with CodeArtifact, we need to create an **IAM role** that grants our **EC2 instance** the permission it needs to access CodeArtifact. Otherwise, Maven can try all it wants to command our EC2 instance to store and retrieve packages from CodeArtifact, but our EC2 instance simple wouldn't be able to do anything! And going another layer deeper, IAM roles are made of policies; so we need to create policies first before setting up the role.

- On our newly created repository's page, click the **View connection instructions** button at the top right corner.
- In the **Connection instructions** page, we're configuring how Maven will connect to our CodeArtifact repository.
- For **Operating system**, select **Mac and Linux**.

Even if we're doing this project on a Windows computer, don't forget that the EC2 instance we're using was launched with **Amazon Linux 2023** as its AMI! `mvn` is short for Maven, which is the tool we installed to manage the building process for our Java web app. This also makes Maven our package manager, i.e. the tool that helps us install, update and manage the external packages our web app uses.

- For **Package manager client**, select **mvn** (Maven).
- Double check that we're using **Mac and Linux** as the operating system. Even if we're doing this project on a Windows computer, Mac and Linux is the right choice - our **EC2 instance** is an **Amazon Linux 2023** instance!
- Make sure that **Configuration method** is set to **Pull** from our repository.
- Nice! The menu will now show us the steps and commands needed to connect Maven to our CodeArtifact repository.

Export CodeArtifact authorization token
- In the **Connection instructions** dialog, find **Step 3: Export a CodeArtifact authorization token....**
- Copy the entire command in **Step 3**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-30.png)

**"Export a CodeArtifact authorization token for authorization to your repository from your preferred shell"** It actually just means we need to run the command in Step 3 to give our terminal a temporary password. That password will grant our development tools (i.e. Maven) access to our repositories in CodeArtifact. Maven uses this token whenever it needs to fetch something from our CodeArtifact repository.

- Go back to our VS Code terminal, which is connected to our EC2 instance.
- Paste the copied command into the terminal and press **Enter** to run it.
- Uhhh... looks like we got an error!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-31.png)

That `Unable to locate credentials` error is actually a good security feature in action! Our EC2 instance is essentially saying, "I don't know who you are, so I can't let you access CodeArtifact." This happens because, by default, our EC2 instance **doesn't** have permission to access our other AWS services (including CodeArtifact). This is intentional - AWS follows the "principle of least privilege," meaning resources only get the **minimum** permissions they need to function.

Create a new IAM policy
- In the AWS Management Console, head to the the `IAM` console.
- In the IAM console, in the left-hand menu, click on **Policies**.
- Click the **Create policy** button to start creating a new IAM policy.
- On the **Create policy** page, select the **JSON** tab.
- Replace the default content in the text editor with the following JSON policy document. Copy and paste the entire JSON code block:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "codeartifact:GetAuthorizationToken",
                "codeartifact:GetRepositoryEndpoint",
                "codeartifact:ReadFromRepository"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "sts:GetServiceBearerToken",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "sts:AWSServiceName": "codeartifact.amazonaws.com"
                }
            }
        }
    ]
}
```

This JSON document is like a security rulebook. It's written in a specific format that AWS understands, with two main parts:
  1. The first part (`codeartifact:*` actions) gives permission to get authentication tokens, find repository locations, and read packages from repositories.
  2. The second part (`sts:GetServiceBearerToken`) allows temporarily elevated access specifically for CodeArtifact operations.

The `"Resource": "*"` means these permissions apply to all relevant resources, while the Condition narrows the second permission to only work with CodeArtifact. This follows the "principle of least privilege" - granting only the minimum permissions needed to perform the required tasks, enhancing our security posture.

- After pasting the JSON policy document, click the **Next** button at the bottom right.
- On the **Review policy** page, in the **Policy name** field, enter `codeartifact-nextwork-consumer-policy`.
- In the **Description - optional** field, add a description like:
`Provides permissions to read from CodeArtifact. Created as a part of NextWork CICD Pipeline series`.
- Review the **Summary** of your policy to ensure the permissions and details are correct.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-32.png)

- Click the **Create policy** button to create the IAM policy.
- After clicking **Create policy**, we should see a success message at the top of the IAM Policies page, telling us that the policy `codeartifact-nextwork-consumer-policy` has been successfully created.

Now that we've created the IAM policy for CodeArtifact access, let's attach it to an IAM role and then associate that role with our EC2 instance. This will grant our EC2 instance the permissions it needs to securely access CodeArtifact. Finally, we'll verify the connection to CodeArtifact from our EC2 instance. This is important because attaching the IAM role to our EC2 instance is what actually grants the instance the permissions defined in the policy, enabling secure access to CodeArtifact.

Create a new IAM role for EC2
- In the IAM console, in the left-hand menu, click on **Roles**.
- Click the **Create role** button to start creating a new IAM role.
- For **Select entity type**, choose **AWS service**.
- Under **Choose a use case**, select **EC2** from the list of services.
- Click **Next** to proceed to the **Add permissions** step.
- In the **Add permissions** step, in the **Filter policies** search box, type `codeartifact-nextwork-consumer-policy`.
- Select the checkbox next to the `codeartifact-nextwork-consumer-policy` that we created in the previous step.
- Click **Next** to head to the **Name, review, and create** step.
- In the **Name, review, and create** step:
  - In the **Role name** field, enter `EC2-instance-nextwork-cicd`.
  - In the **Description - optional** field, enter:
`Allows EC2 instances to access services related to the NextWork CI/CD pipeline series.`
- Next, in the review page, click the **Create role** button to create the IAM role.
- After clicking **Create role**, we should see a success message at the top of the IAM Roles page, telling us that the IAM role `EC2-instance-nextwork-cicd` has been successfully created. Our new role will be listed in the roles table.

Attach IAM role to EC2
- Now, we need to associate this IAM role with our EC2 instance.
- Head back to the `EC2` console.
- Head to the `Instances` tab.
- Select our running EC2 instance (`nextwork-devops-yourname`).
- Click on **Actions** in the menu bar, then select **Security**, and then **Modify IAM role**.
- In the **Modify IAM role** dialog box, select **Refresh** to load the IAM roles available for our EC2 instance.
- Under **IAM role**, select the IAM role we just created, `EC2-instance-nextwork-cicd`, from the dropdown menu.
- Select **Update IAM role** to attach the role to our EC2 instance.
- After attaching the IAM role, we should see a green banner at the top of the EC2 Instances dashboard confirming that the IAM role was successfully modified for our instance.

Now that our EC2 instance has the necessary IAM role attached, let's re-run the command to export the CodeArtifact authorization token.
- Head back to our VS Code terminal connected to our EC2 instance.
- Re-run the same export token command from Step 3.
- This command will retrieve a temporary authorization token for CodeArtifact and store it in an environment variable named `CODEARTIFACT_AUTH_TOKEN`.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-33.png)

**Step -3 : Verify CodeArtifact Connection and See Packages in CodeArtifact**

Let's make sure everything is set up correctly by verifying the connection to our CodeArtifact repository from our EC2 instance. We'll configure Maven to use CodeArtifact and then try to compile our web app, which should now download dependencies from CodeArtifact. This verification is crucial to ensure that our EC2 instance can successfully access and retrieve packages from CodeArtifact, which is a key part of our CI/CD pipeline setup.

- We might notice that we still have a few steps left in CodeArtifact's connection settings panel!
- We'll use the code in each step in a minute, but we'll have to set up a special file called **settings.xml** first.
- In VS Code, in our left hand **file explorer**, head to the root directory of your `nextwork-web-project`.
- Create a new file at the root of our `nextwork-web-project` directory.
- Name the new file `settings.xml`.

**settings.xml** is like a settings page for Maven - it stores all the settings we saw in Steps 4-6 of the connection window. It tells Maven how to behave across all our projects. In our case, we need a settings.xml file to tell Maven where to find the dependencies and how to get access to the right repositories (e.g. the ones in CodeArtifact).

- Open the `settings.xml` file. If we created a new file, it will be empty.
- In your `settings.xml` file, add the `<settings>` root tag if it's not already there:

```xml
<settings>
</settings>
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-34.png)

- Go back to the CodeArtifact connection settings panel.
- From the **Connection instructions** dialog, copy the XML code snippet from **Step 4: Add your server to the list of servers in your settings.xml**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-35.png)

The **servers** section is where we store our access details to the repositories we're connecting with our web app project. In this example, we've added our authentication token to access our local CodeArtifact repository.

- Paste the code in the **settings.xml** file, in between the `<settings>` tags.
- Let's copy the XML code snippet from **Step 5: Add a profile containing your repository to your settings.xml.**

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-36.png)

The **profiles** section is where we write a rulebook on when Maven should use which repository. We only have one package repository in this project, so our profiles section is more straightforward than other projects that might be pulling from multiple repositories! Our profiles section is telling Maven to go to the nextwork-packages repository to find the tools / packages needed to build our Java web app.

- Paste the code snippet we copied right underneath the `<servers>` tags. Make sure the `<profiles>` tags are also nested inside the `<settings>` tags.
- Finally, paste the XML code snippet from **Step 6: (Optional) Set a mirror in your settings.xml...** right underneath the `<profiles>` tags.

The **mirrors** section sets up backup locations that Maven can check if it can't find what it needs in the first local repository it goes to. The backup location that we'll set by default is... our CodeArtifact repository again. This means that for any repository requests (denoted by the asterisk * in the * line), Maven will redirect those requests to the same CodeArtifact repository since it's our only local repository. It might seem unnecessary now, but mirrors are great in complex scenarios and is a great fallback option to set up from the start!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-37.png)

Save the `settings.xml` file.

Compile our project and verify the CodeArtifact integration
-  In our VS Code terminal, run `pwd` to check that we're in the root directory of our `nextwork-web-project`
-  If we're not at the root directory, run `cd nextwork-web-project` to get there!
-  Next, we'll **compile** our project.
-  Run the Maven compile command, which uses the `settings.xml` file we just configured:

```bash
mvn -s settings.xml complie
```

-  Press **Enter** to execute the command.
-  As Maven compiles our project, observe the terminal output.
-  We should see messages like `Downloading from nextwork-devops-cicd` telling us that Maven is downloading dependencies from your CodeArtifact repository. This is a good sign that Maven is using CodeArtifact to manage dependencies!
-  If the compilation is successful and dependencies are downloaded from CodeArtifact, we'll see a `BUILD SUCCESS` message at the end of the Maven output.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-38.png)

When we run `mvn -s settings.xml compile`, Maven first looks at our project's dependencies in the `pom.xml` file. Then, instead of downloading them directly from public repositories, it checks our CodeArtifact repository. If the dependency isn't already in CodeArtifact, it will fetch it from the upstream repository (Maven Central in our case), cache it in CodeArtifact, and then deliver it to our project. This process happens for each required dependency, ensuring that our build process is secure, controlled, and faster for subsequent builds when dependencies are already cached in CodeArtifact.

-  Let's head back to the CodeArtifact console in our browser.
-  Close the connection instructions window.
-  If we don't see any packages in our repository listed yet, click the **refresh** button in the top right corner of the Packages pane.
-  After refreshing, we should now see a list of Maven packages in our CodeArtifact repository.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-39.png)

Those packages appearing in our CodeArtifact repository are proof that the entire system is working! Here's what happened behind the scenes: when we ran the Maven compile command, Maven checked our project's pom.xml file and determined which dependencies our application needs. It then requested these dependencies through CodeArtifact. Since this was the first time these dependencies were requested, CodeArtifact didn't have them yet. So it reached out to Maven Central (the upstream repository we configured), downloaded the packages, stored copies in our repository, and then provided them to Maven. Now that these packages are stored in our CodeArtifact repository, anyone else in our organization who needs the same dependencies will get them directly from our repository instead of from Maven Central. This gives us faster builds, more reliability, and the ability to control exactly which package versions our organization uses.

---

##  Continuous Integration with CodeBuild

AWS CodeBuild is a **continuous integration service** that **automates** this entire build process for us. When a developer pushes new code, CodeBuild **automatically** compiles the code, runs the tests, and packages everything up. This saves enormous time, eliminates human error, and helps us deliver better software faster! That's why CodeBuild is an essential part of a CI/CD pipeline - it automatically makes sure our application is always built consistently and correctly.

**Continuous Integration** is like having a quality control checkpoint that automatically kicks in whenever anyone on our team makes changes to our code. Instead of waiting until the end of a project to discover that something broke, CI helps us catch and fix issues early and often. CI helps us constantly check that everything still works as expected - running tests, compiling code, and making sure new changes play nicely with the existing codebase.

**Step -1 : Setting Up Our CodeBuild Project**

**CodeBuild project** is basically the blueprint for our CI process. It's where we tell AWS everything it needs to know about how to build our application. This includes things like **where** our code lives (like GitHub), what kind of **environment** we need (Linux or Windows? Java or Python?), exactly what **commands** to run during the build, and where to **store** the results when it's done. Think of it as a recipe that CodeBuild follows every time it needs to build our application.

-  Log in to our AWS Management Console.
-  In the top search bar, type `CodeBuild`.
-  Select **CodeBuild** from the dropdown menu under **Services**.
-  In the CodeBuild dashboard, find the left navigation menu.
-  Select **Build projects**.
-  On the **Create build project** page, scroll to the **Project configuration** section.
-  Under **Project name**, enter `nextwork-devops-cicd`.
-  Under **Project type**, make sure **Default project** is selected.

CodeBuild gives us two main types of projects, each designed for different CI/CD needs:
  1.  **Default project**: This is our standard option that most teams use. It's perfect when we want to manage our entire build process within AWS. We get full control over how our build runs, what goes in, and what comes out - all without leaving the AWS ecosystem.
  2.  **Runner project**: This option is for teams who already have CI systems like GitHub Actions or GitLab CI but want to tap into the power of CodeBuild's build environment. It's like having CodeBuild do the heavy lifting while our existing CI system orchestrates the overall process.

For this project, we're using a **Default project** to directly manage our CI process within CodeBuild.

-  Scroll down to the **Source** section.
-  Under **Source provider**, select **GitHub**.

CodeBuild doesn't know where our web app's code lives! **Source** means the location of the code that CodeBuild will fetch, compile, and package into a file we can deploy. We're choosing GitHub as our source provider because that's where we've stored our web app's code. By connecting CodeBuild directly to GitHub, we're creating a seamless pipeline where code changes automatically flow into our build process.

CodeBuild plays well with lots of different code repositories - AWS CodeCommit, Bitbucket, GitHub, and even plain S3 buckets. This flexibility lets us keep using whichever code storage system our team prefers while still getting all the benefits of AWS's build capabilities.

To allow CodeBuild to access our private GitHub repository, we need to establish a connection using **AWS CodeConnections**. Connecting to GitHub is crucial for CodeBuild to access our project's code.
-  In the **Source** section, under **Credential**, we might see the message `You have not connected to GitHub. Manage account credentials`.
-  Click on **Manage account credentials**.
-  We will be taken to the **Manage default source credential** page.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-40.png)

-  Ensure **GitHub App** is selected for **Credential type**.

When connecting to GitHub, CodeConnections gives us a few different options, each with their own trade-offs:
  1.  **GitHub App**: This is generally the simplest and most secure option. AWS manages the application and connection, reducing the need for us to handle tokens or keys directly. It's recommended for most use cases due to its ease of use and enhanced security.
  2.  **Personal access token**: This method uses a personal access token generated from our GitHub account. We might remember using this for authenticating to GitHub from the terminal. While straightforward, it requires us to manage and rotate tokens, which can be less secure and more operationally intensive.
  3.  **OAuth app**: This involves setting up an OAuth application in GitHub and configuring CodeConnections to use it. It provides a more granular control over permissions but is more complex to set up compared to GitHub App.

-  Select **create a new GitHub connection**.
-  On the **Create connection** page, under **Connection details**, enter `nextwork-devops-cicd` as the **Connection name**.
-  Click **Connect to GitHub**.
-  We will be taken to GitHub to authorize the **AWS Connector for GitHub** application.
-  Select our GitHub user account where our repository is located.
-  Select **Select**.
-  After authorization on GitHub, we'll get taken back to the AWS console.
-  Under **GitHub Apps**, we'll see that our GitHub username is an option now!
-  Select our GitHub username.
-  Click **Connect**.

**Awesome! We've successfully connected to GitHub**. It might seem like a few steps, but this process securely links our AWS account to our GitHub account using the AWS Connector for GitHub.
-  We should get redirected back to CodeBuild's **Manage default source credential** page after successful connection.
-  On the **Manage default source credential** page, we should see our newly created connection listed.
-  Click **Save** at the bottom of the page.

By saving the GitHub App connection as the default credential, we make it easier to reuse this connection for future CodeBuild projects. This avoids the need to repeat the connection setup process each time we create a new project that uses the same GitHub account.

-  Now, back in the **Create build project** page, in the **Source** section, we should see a success message in green: "Your account is successfully connected by using an AWS managed GitHub App."

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-41.png)

When we were taken to different pages to connect to GitHub, that was CodeBuild passing us to another Code service (called **AWS CodeConnections**) behind the scenes! AWS CodeConnections is like a secure bridge between AWS and our external code repositories. Instead of dealing with the headache of managing API keys, tokens (like GitHub's Personal Access Tokens!), or SSH credentials, CodeConnections handles all that authentication complexity for us - so we can focus on building our application.

If we'd like, we can open the left hand navigation menu, expand **Settings** at the bottom of the list, and open the **Connections** page. We can manage all connections we set up with CodeConnections there!

-  We can now select our GitHub repository `nextwork-web-project` as the source.

**Next, we need to define the environment where our builds will run. This includes the operating system, runtime, and compute resources.**
-  Scroll down to the **Primary source webhook events** section.
-  **Untick** the **Webhook** checkbox that says "Rebuild every time a code change is pushed to this repository."*
-  Scroll down to the **Environment** section.
-  Under **Compute**, for **Provisioning model**, choose **On-demand**.

**Provisioning model** determines how AWS will set up and manage everything needed for our build. Choosing **On-demand** means AWS will create the resources we need for our build only when we start it, and tear them down when the build is done. This is cost-effective and efficient!

-  For **Environment image**, choose **Managed image**.

**Environment image** is like a template for our build environment (just like how AMI's are templates for our EC2 instances). In more technical terms, environment images are pre-configured versions of the build environment so we won't need to install all the software/tools/settings required to build a project. We choose **Managed image** here, which means we're using a template that AWS has already created for us. The next few settings we pick underneath this will then tell CodeBuild what kind of image we're looking for.

- For **Compute type**, choose **EC2**.

**Compute** sets up the servers that will actually run the commands and do the work for our project's build! Our project's build will run on **Amazon EC2** instances, which are more flexible and powerful than **AWS Lambda** functions. Lambda is optimized for speed and faster startup times. Our web app is fairly simple and doesn't require a lot of resources, but it's also built in **Java Corretto 8**, which is a language that's not supported by Lambda.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-42.png)

-  Under **Environment**, for **Operating system**, select **Amazon Linux**.
-  For **Runtime(s)**, select **Standard**.
-  For **Image**, choose `aws/codebuild/amazonlinux-x86_64-standard:corretto8`.
-  Keep **Image version** as **Always use the latest image for this runtime version**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-43.png)

-  Under **Service role**, select **New service role**.

Now, let's define how CodeBuild will actually build our application using a `buildspec.yml` file.
-  Scroll down to the **Buildspec** section.
-  Under **Buildspec format**, select **Use a buildspec file**.
-  Leave **Buildspec name** as default `buildspec.yml`.

`buildspec.yml` file is like a **detailed instruction manual for CodeBuild**. Placed in the root of our repository, it tells CodeBuild exactly what to do at each stage of the build process - what tools to install, what commands to run, and what files to package up when it's done. CodeBuild automatically looks for a file named `buildspec.yml` in the root directory of our source code. If it finds one, it uses it to execute the build. If not, the build will fail (as we'll see later)!

-  Scroll down to the **Batch configuration** section.

Batch configuration lets us run multiple builds at once as part of a single batch job. This becomes super useful when we need to test our code across different environments (like various operating systems or browser configurations) or when we want to parallelize parts of our build process to save time. **While we won't use it in this project**, it's a powerful feature for more complex workflows.

-  Scroll down to the **Artifacts** section. We need to configure where CodeBuild will store the **build artifacts**.

**Build artifacts** are the tangible outputs of our build process. They're what we'll actually deploy to our servers or distribute to users. That's why storing them properly in S3 is so important - they're the whole reason we're running the build in the first place.

For our project, we want our build process to create one build artifact that packages up everything a server could need to host our web app in one neat bundle. This bundle is called a WAR file (which stands for Web Application Archive, or Web Application Resource) and it works just like a zip file - a server will simply "unzip" our WAR file to find a bunch of files and resources (which are also build artifacts, i.e. a WAR file is a build artifact that bundles up other build artifacts) and host our web app straight away. Notice how we haven't been able to view our web app on a web browser so far in this project series - that's because we haven't created and deployed the WAR file yet!

Note: our build process will create a .war file (a packaged Java web application) as the build artifact, but artifacts could be executables, libraries, documentation, or any output our build creates.

-  For **Type**, select **Amazon S3**.

Our compiled applications, libraries, or any output files from our build need a safe, accessible home after the build finishes. **S3** is perfect for this - it's a highly reliable and scalable storage solution that's also in our AWS environment (which makes the artifact easily accessible for deployment later).

-  Let's head to the `S3` console. In the AWS Management Console search bar, type `S3` and select **S3** from the dropdown menu under **Services**.
-  Make sure we're still in the same region where we set up the CodeBuild build project.
-  On the **Buckets** page, click **Create bucket**.
-  In the **Create bucket** page, under **General configuration**, for **Bucket name**, enter `nextwork-devops-cicd-yourname`.
-  Leave all other settings as default.
-  Click **Create bucket** at the bottom of the page.
-  **Refresh** the build project page in our browser - we might need to configure our build project again.
-  In our CodeBuild project, head back to the **Artifacts** section.
-  For **Type**, select **Amazon S3**.
-  For **Bucket name**, choose our newly created bucket `nextwork-devops-cicd` from the dropdown.
-  In **Name**, enter `nextwork-devops-cicd-artifact`. This names our artifact, so it's easy to spot it in the S3 bucket.
-  For **Artifacts packaging**, select **Zip**.

**Finally, let's enable CloudWatch logs to monitor our build process. Logs are essential for tracking build progress, debugging errors, and auditing build activities.**
- Scroll down to the **Logs** section.
-
- Make sure **CloudWatch logs** is checked.

**Amazon CloudWatch Logs** is a monitoring service that collects and tracks logs from AWS services. In this project, CloudWatch will record everything that happens during the build process, including the commands that are run, the output of those commands, and any errors that occur. This is incredibly useful for debugging and understanding what went wrong if a build fails.

-  In **Group name**, enter `/aws/codebuild/nextwork-devops-cicd`.

By using a specific name like `/aws/codebuild/nextwork-devops-cicd`, we're essentially creating a dedicated folder for all logs related to this project. This becomes super helpful as our AWS environment grows. Imagine having hundreds of projects and services to track! With custom group names, we can instantly filter and find the logs we need with this classic naming convention.

-  Scroll to the bottom of the page and click **Create build project**.

**Step - 2 : Run the Build and Troubleshoot Failures**

Now that our CodeBuild project is fully configured, let's initiate our first build and see our CI pipeline in action!

-  Navigate to our newly created CodeBuild project `nextwork-devops-cicd`.
-  Click the **Start build** button.
-  We will be redirected to the build execution page.
-  We should see the build status change to **In progress**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-44.png)

-  Wait for the build to complete.
-  Oooo, looks like the build failed!
-  By checking the logs and phase details, we can pinpoint exactly where and why our build is failing.
-  Select the **Phase details** tab to understand the failure.
-  Aha, the `DOWNLOAD_SOURCE` phase has failed with the error message `YAML_FILE_ERROR: YAML file does not exist`.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-45.png)

Good news - this is exactly what we expected to happen! This error simply means CodeBuild couldn't find the `buildspec.yml` file in our GitHub repository, which makes sense because we haven't created it yet. Without this file, CodeBuild doesn't know how to build our project. We'll create this file in the next steps.

-  Click on the **Build details** tab.
-  Scroll down to the **Buildspec** section.
-  Let's make sure that it says **Using the buildspec.yml in the source code root directory**.
-  This confirms that CodeBuild is looking for **buildspec.yml** in the root directory of our web app's `nextwork-web-project` code repository... we haven't created that file yet!
-  In fact, let's check our GitHub repository. Select the **Repository** link in our CodeBuild project.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-46.png)

-  Welcome back to our GitHub repo!
-  As we might've noticed, there's no `buildspec.yml` file - it should be in the root of our web app repository. Let's create it now.
-  Open our project in VS Code or our preferred code editor.
-  Select **File** > **Open Folder...**
-  Open our `nextwork-web-project` folder.
-  Select **Ok**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-47.png)

-  In VS Code, in the Explorer pane, create a new file on the project root (`nextwork-web-project`).
-  Enter `buildspec.yml` as the file name.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-48.png)

-  Paste the following code for buildspec.yml:

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      java: corretto8
  pre_build:
    commands:
      - echo Initializing environment
      - export CODEARTIFACT_AUTH_TOKEN=`aws codeartifact get-authorization-token --domain nextwork --domain-owner 123456789012 --region us-east-2 --query authorizationToken --output text`

  build:
    commands:
      - echo Build started on `date`
      - mvn -s settings.xml compile
  post_build:
    commands:
      - echo Build completed on `date`
      - mvn -s settings.xml package
artifacts:
  files:
    - target/nextwork-web-project.war
  discard-paths: no
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-49.png)

-  In the `buildspec.yml` file:
    -  Replace the placeholder AWS Account ID `123456789012` with our actual AWS Account ID.
    -  Check that the region code is correct! Update the region section from `--region us-east-2` to the AWS region we're using.
-  Save the `buildspec.yml` file (press Ctrl+S or Cmd+S on the keyboard).
-  Now, we need to commit and push the `buildspec.yml` file to our GitHub repository so CodeBuild can access it.
-  Open the terminal in VS Code (Ctrl+or Cmd+).
-  Run the following Git commands:

```bash
git add .
git commit -m "Adding buildspec.yml file"
git push
```

**Seeing errors when pushing our code?** Try resetting our Git credentials, and starting over again! Run these commands in our terminal:

```bash
git config --global --unset credential.helper
git config --local --unset credential.helper
git remote set-url origin https://<YOUR_GITHUB_USERNAME>@github.com/<YOUR_GITHUB_USERNAME>/<YOUR_REPOSITORY_NAME>.git
```

  -  Make sure to replace <YOUR_GITHUB_USERNAME> with our GitHub username and <YOUR_REPOSITORY_NAME> with our repository name.
  -  Then try git push again and enter our GitHub PAT when prompted for the password.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-50.png)

We need to push our `buildspec.yml` file to GitHub because CodeBuild doesn't look inside our local computer for build instructions - it looks in our GitHub repository. When CodeBuild connects to our GitHub repo, it first looks for this special file. Without it, CodeBuild has no idea what to do with our code. By pushing the file to GitHub, we're making sure our instructions travel alongside our code.

-  Go to our GitHub repository `nextwork-web-project` in our browser and **refresh** the page to verify that `buildspec.yml` is now present in the repository.

Check the terminal output to ensure the changes, including the new `buildspec.yml` file, have been successfully pushed to our GitHub repository.
-  Go to our GitHub repository in a web browser and refresh the page.
-  `buildspec.yml` should be in our GitHub repo now!

**Step - 3 :  Verify Successful Build and Artifacts**

Now that we've fixed our CodeBuild setup, let's re-run the build process. We'll also check our S3 bucket to see if it's storing the build artifact correctly.
-  Return to the CodeBuild console in our browser.
-  Retry the build by clicking the **Retry build** button.
-  The build status should now show In progress. Wait for this build to complete.
-  Damn it, we failed again!
-  Check the **Phase details** tab again after this retry.
-  We will likely see that the `DOWNLOAD_SOURCE` phase succeeded this time, but other phases might have failed!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-51.png)

This error typically means CodeBuild still can't access the `settings.xml` file. This usually happens because our CodeBuild service role **doesn't have permission to access CodeArtifact**, which is needed to download project dependencies.

-  To fix this, we need to grant CodeBuild's IAM role the permission to access CodeArtifact.
-  Head to the `IAM` console.
-  In the IAM console, select **Roles** in the left navigation menu.
-  In the roles search bar, type `Codebuild` to filter the roles.
-  Select the role that starts with `codebuild-nextwork-devops-cicd-service-role`. This is the new service role that CodeBuild created when we set up our build project.
-  Select our CodeBuild service role.
-  Click on the **Add permissions** button.
-  Choose **Attach policies**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-52.png)

-  In the **Filter policies** search bar, type `codeartifact-nextwork-consumer-policy`.
-  Check the checkbox next to the policy named `codeartifact-nextwork-consumer-policy`.
-  We can even expand the policy name to review the permissions granted by this policy.
-  After selecting the policy, click the **Add permissions** button.
-  We should see a green banner confirming `Policy was successfully attached to role.` and the policy should now be listed under the **Permissions policies** of our CodeBuild service role.
-  Return to the CodeBuild console, navigate to our `nextwork-devops-cicd` project.
-  Retry the build one more time by clicking **Retry build**.

The build status should again be **In progress**. With the added permissions, this time the build should proceed successfully! The build status should now be **Succeeded** with a green checkmark, indicating a successful CI pipeline run!

Let's check if our build process has successfully created and stored the artifact.
-  To verify the successful build, let's check if the build artifact was correctly uploaded to our S3 bucket.
-  Head to the `S3` console.
-  Select **Buckets** from the left navigation menu.
-  Select the `nextwork-devops-cicd` bucket we created earlier.
-  Initially, the bucket might be empty. Click **Refresh** to update the bucket content.
-  We should now see the artifact `nextwork-devops-cicd-artifact.zip` listed in our bucket 

By confirming that an artifact exists in our S3 bucket, we know that:
  1.  Our code was successfully compiled and packaged
  2.  The build process completed without errors
  3.  The artifact was properly uploaded to its destination
  4.  The artifact is now available for the next step in our process (like deployment)

Think of it as the final proof that our CI process is delivering what it promised! If we choose to download and inspect the artifact, we should find the `nextwork-web-project.war` file inside the downloaded zip archive, confirming the build process produced the expected output.

---

## Deploy a Web App with CodeDeploy

When we're developing software, we need a reliable way to release new versions of our app to the world. **AWS CodeDeploy** is a **continuous deployment service**, which means it automates how we get new software versions onto our servers. Instead of manually moving files and restarting services ourself, CodeDeploy automatically runs a deployment using settings and commands that we define. As part of a CI/CD pipeline, CodeDeploy makes software releases faster, more consistent, and way less stressful.

**Step -1 : Launch EC2 with CloudFormation**

Earlier, we created our **development instance**, by setting up an EC2 instance for us to use as a development environment i.e. to create and edit code for our web app. This **new instance** is created specifically for running our application in a live, **production environment** i.e. what users will see. By having a separate EC2 instance for deploying our application, we avoid the risk of testing any code changes/new features in a live production environment that users end up seeing too.

To set up the deployment infrastructure we'll use **CloudFormation** to launch an EC2 instance and its networking resources. CloudFormation is AWS' **infrastructure as code** tool. Instead of clicking around the AWS console to set up resources, we write a single template file that describes everything we need - our EC2 instances, security groups, databases, and more. Then, CloudFormation reads this file and builds our entire environment for uw, exactly the same way every time.

-  Head to the `CloudFormation` console.
-  Select **Create stack**.
-  Select **With new resources (standard)** from the dropdown menu.

When we deploy a CloudFormation template, we're creating a **stack** - think of it as a project folder that holds all our connected resources. The cool thing is that CloudFormation treats this stack as a single unit, so we can create, update, or delete all those resources together with one command.

-  Select **Template is ready**.
-  Select **Upload a template file**.
-  Select **Choose file.**
-  Upload the following CloudFormation template: [nextworkwebapp.yaml](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/d12b68a507368e363e5a76a3f31cef40d7268132/nextworkwebapp.yaml)

A CloudFormation **template** is a text file where we tell CloudFormation the AWS resources we want to create and how they should be configured - like specifying "I want a t2.micro EC2 instance with this security group and permissions to access this S3 bucket." The beauty is that this template is both human-readable AND machine-executable. In our project, we're using YAML because it's a bit friendlier to read than JSON (fewer brackets and quotes to keep track of!).

-  Verify the file `nextworkwebapp.yaml` is uploaded and select **Next**.
-  Select **Next**.
-  Enter `NextWorkCodeDeployEC2Stack` as the **Stack name**.
-  Next, we'll need to add our IP address to the template.
-  Head to https://checkip.amazonaws.com/ and copy our IP address.
-  Paste our IP address into the **MyIP** parameter field and add `/32` at the end.

Adding `/32` to our IP address is like telling AWS "I only want this exact address to have access, not any others." The `/32` is CIDR notation that specifies exactly how many IP addresses are included in our rule.

-  Select **Next**.
-  In **Configure stack options**, under **Stack failure options**, select **Roll back all stack resources**.
-  Next, select **Delete all newly created resources**.

**Stack failure options** are our safety net when things don't go as planned. They determine what CloudFormation should do if it runs into an error while creating our resources:
  1.  **Roll back all stack resources**: This is like having an "undo" button for our entire deployment. If anything fails, CloudFormation will automatically revert everything back to how it was before we started. This prevents us from ending up with a half-built environment that might not work or cost us money unnecessarily.
  2.  **Delete all newly created resources**: This makes sure CloudFormation cleans up after itself during a rollback. No resources left behind to surprise us on our bill next month!

-  Scroll down to **Capabilities** and check the box **I acknowledge that AWS CloudFormation might create IAM resources**.
-  Select **Next**.
-  Select **Submit**.

CloudFormation is now launching the stack in the background! This process will create the EC2 instance and networking resources in the background.

-  Let's check out the **Resources** tab. We can see a list of the resources being created!

CloudFormation template is creating a:
  1.  **VPC (Virtual Private Cloud)**: A virtual network in the cloud for our AWS resources.
  2.  **Subnet**: A subdivision of the VPC where we can place resources.
  3.  **Route Tables**: Define how network traffic is routed within the VPC.
  4.  **Internet Gateway**: Allows resources in our VPC to connect to the internet.
  5.  **Security Group**: Acts as a virtual firewall to control inbound and outbound traffic for our EC2 instance.
  6.  **EC2 Instance**: The virtual server where our web application will be deployed.

By defining networking resources in the template, we're not just launching an EC2 instance, but creating a complete, secure, and configurable infrastructure that can be easily replicated or modified. This is an especially good idea for EC2 instances that are hosting web apps, because they have more complex needs like connecting with multiple databases and controlling both public and private network traffic.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-53.png)

-  Next, we can also check the **Events** tab.
-  We can watch our CloudFormation stack's events in real-time in the **Events** tab - just keep refreshing it!

Every time CloudFormation creates, updates, or deletes something, it records an **event** - "Starting to create EC2 instance," "Security group created successfully," "Oh no, there isn't enough capacity to create a new VPC." These events give us visibility into exactly what's happening behind the scenes, which is super helpful for troubleshooting if something goes wrong.

-  Wait for the stack's status to become `CREATE_COMPLETE`.

**Step - 2 : Prepare Deployment Scripts and AppSpec**

To start deploying our application, we need to prepare a **set of scripts and configuration files** for CodeDeploy. It's like we need to write a set of instructions for CodeDeploy to follow - otherwise, it wouldn't know how to deploy our application!

-  In our IDE, create a new folder at the root of our project directory.
-  Name the folder `scripts`

Scripts are essentially text files filled with commands that we'd normally type one by one in the terminal, but packaged together so they run automatically in sequence. In our project, we're using scripts to automate deployment tasks.

-  Inside the `scripts` folder, create a new file.
-  Let's name the file `install_dependencies.sh`

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-54.png)

-  Add the following content to `install_dependencies.sh`:

```bash
#!/bin/bash
sudo yum install tomcat -y
sudo yum -y install httpd
sudo cat << EOF > /etc/httpd/conf.d/tomcat_manager.conf
<VirtualHost *:80>
  ServerAdmin root@localhost
  ServerName app.nextwork.com
  DefaultType text/html
  ProxyRequests off
  ProxyPreserveHost On
  ProxyPass / http://localhost:8080/nextwork-web-project/
  ProxyPassReverse / http://localhost:8080/nextwork-web-project/
</VirtualHost>
EOF
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-55.png)

`install_dependencies.sh` script sets up all the software needed to run our website by installing programs (called Tomcat and Apache) that handle internet traffic and host our application. It then creates special settings that let these programs to work together, making our website accessible to visitors on the internet.

-  Save the `install_dependencies.sh` file by pressing **Ctrl+S**.
-  Still inside the `scripts` folder, let's create a new file again.
-  This time, let's name the file `start_server.sh`
-  Add the following content to `start_server.sh`:

```bash
#!/bin/bash
sudo systemctl start tomcat.service
sudo systemctl enable tomcat.service
sudo systemctl start httpd.service
sudo systemctl enable httpd.service
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-56.png)

This script starts both **Tomcat** (our Java application server) and **Apache** (our web server) and makes sure they'll restart automatically if the EC2 instance ever reboots.

-  Save the `start_server.sh` file by pressing **Ctrl+S**.
-  Inside the `scripts` folder, create a new file named `stop_server.sh`
-  Add the following content to `stop_server.sh`:

```bash
#!/bin/bash
isExistApp="$(pgrep httpd)"
if [[ -n $isExistApp ]]; then
sudo systemctl stop httpd.service
fi
isExistApp="$(pgrep tomcat)"
if [[ -n $isExistApp ]]; then
sudo systemctl stop tomcat.service
fi
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-57.png)

This script safely stops web server services by first checking if they're running. It uses pgrep to check for running processes of Apache (httpd) and Tomcat, and only attempts to stop the services if they are actually active. We check if the server is running first because trying to stop something that's not running can cause errors that might interrupt our deployment.

This makes our script more robust and reliable. If we blindly tried to stop services regardless of their state, we might get error messages that could cause our deployment to fail unnecessarily. Good scripts anticipate potential issues instead of assuming everything is in an ideal state - that's the difference between code that works in perfect conditions versus code that works in the real world!

-  Save the `stop_server.sh` file by pressing **Ctrl+S**.
-  Check that we have `install_dependencies.sh`, `start_server.sh`, and `stop_server.sh` inside the `scripts` folder.
-  Create a new file, but this time at the **root** of our project.
-  Make sure this file is **NOT** inside the `scripts` folder!
-  Let's name the file `appspec.yml`
-  Add the following content to `appspec.yml`:

```yaml
version: 0.0
os: linux
files:
  - source: /target/nextwork-web-project.war
    destination: /usr/share/tomcat/webapps/
hooks:
  BeforeInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
      runas: root
  ApplicationStart:
    - location: scripts/start_server.sh
      timeout: 300
      runas: root
  ApplicationStop:
    - location: scripts/stop_server.sh
      timeout: 300
      runas: root
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-58.png)

`appspec.yml` file is essentially the instruction manual for CodeDeploy. Each phase in the appspec.yml file is a CodeDeploy lifecycle event. Lifecycle events are like the chapters in our deployment story - predefined points where we can hook in custom scripts to perform specific actions. They're the key to customizing exactly how our application gets deployed.

-  Save the `appspec.yml` file by pressing **Ctrl+S**.
-  Open `buildspec.yml` and modify the `artifacts` section to include `appspec.yml` and the `scripts` folder:

```yaml
artifacts:
  files:
    - target/nextwork-web-project.war
    - appspec.yml
    - scripts/**/*
  discard-paths: no
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-59.png)

The artifacts section we just edited is particularly important - it's telling CodeBuild: "After we build the app, make sure to include these additional files in the final package." We're adding our `appspec.ym`l and `scripts` folder because CodeDeploy will need them to properly deploy the application. Without them, CodeDeploy wouldn't know what to do with our compiled code!

-  Save the `buildspec.yml` file by pressing **Ctrl+S**.
-  Open the terminal in VS Code.
-  Stage all changes using `git add .`
-  Commit changes with the message "Adding CodeDeploy files" using `git commit -m "Adding CodeDeploy files"`
-  Push changes to our GitHub repository using `git push`
-  Check the output of `git push` to confirm that our changes are pushed successfully.
-  Head to our GitHub repository in the browser. Let's check that the `scripts` folder and `appspec.yml` file are in our repository!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-60.png)

**Step - 3 : Set Up CodeDeploy**

Now, let's get to know CodeDeploy and set it up to automate the deployment of our web app!
-  Head to the `CodeDeploy` console.
-  Select **Applications** in the left hand navigation menu.
-  Select **Create application**.

**CodeDeploy application** is like the main folder for our deployment project. It doesn't do much on its own, but it helps us organize everything related to deploying one application. In more technical, AWS terms, a CodeDeploy application is a namespace or container that groups deployment configurations, deployment groups, and revisions for a specific application. Having separate CodeDeploy applications helps us manage multiple applications without mixing up their deployment resources.

-  Enter `nextwork-devops-cicd` as the **Application name**.
-  Choose **EC2/On-premises** as the **Compute platform**.

CodeDeploy's compute platforms are basically the different types of environments where our application can live:
  1.  **EC2/On-premises**: This is for traditional server-based applications - like what we're doing in this project. Our app runs on actual servers (either in AWS or in our own data center).
  2.  **AWS Lambda**: This is for serverless applications where we don't manage any servers. Our code just runs when triggered, and AWS handles all the infrastructure.
  3.  **Amazon ECS**: This is for containerized applications running in Docker containers managed by Amazon's Elastic Container Service.

Choosing the right platform depends on how our application is built. Each has its own advantages for different types of applications!

-  That's it! Select **Create application**.
-  Wait for the success message that the application **nextwork-devops-cicd** is created.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-61.png)

-  Select **Create deployment group**.

A deployment group is a collection of EC2 instances that are grouped to deploy something together. It's also where we plan out exactly **where** and **how** our application gets deployed on this group of instances. In other words, it's where we tell CodeDeploy "let's deploy this app to these specific servers, using this deployment pattern, with these load balancing settings, and handle failures this way."

The power of deployment groups is that we can have multiple groups within the same application - maybe one for our test environment, another for staging, and a third for production. Each can have different settings and target different sets of servers, but they all deploy the same core application. This adds to the (many) reasons why CI/CD tools are so powerful - in this case, CodeDeploy saves us the time it'd take to manually deploy the same app to each instance in each environment.

-  Enter `nextwork-devops-cicd-deploymentgroup` as the **Deployment group name**.
-  Under **Service role**, check if we have a service role available... Maybe not!
-  We'll have to create a new service role.

**CodeDeploy needs IAM roles** to get permissions to access and manage AWS resources on our behalf. These permissions let CodeDeploy do things like:
  1.  Accessing EC2 instances to deploy applications.
  2.  Reading application artifacts from S3 buckets.
  3.  Updating Auto Scaling groups.
  4.  Write CloudWatch logs about what it's doing

-  Head to the `IAM` console.
-  In the IAM console, select **Roles** from the left hand navigation bar.
-  Select **Create role**.
-  Choose **AWS service** as the trusted entity type.
-  Choose **CodeDeploy** as the service and select **CodeDeploy** as the use case.
-  Select **Next**.
-  We'll notice the `AWSCodeDeployRole` default policy is suggested already - nice! That's all we need.
-  Click on the plus button to take a look at the permissions this grants. There are many actions that we're allowing in this policy!
-  Phew, this saves us the time from defining all these permission policies ourselves.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-62.png)

-  Select **Next**.
-  Expand the `AWSCodeDeployRole` policy to review its permissions.
-  Review the EC2 permissions within the policy.

`AWSCodeDeployRole` policy lets CodeDeploy work with our EC2 instances. It includes permissions for:
  1.  **Auto Scaling**, so CodeDeploy can handle deployments when we're automatically scaling instances up or down
  2.  **EC2**, so CodeDeploy can interact with our instances - tag them, query them, and deploy to them
  3.  **Elastic Load Balancing**, so CodeDeploy can temporarily remove instances from load balancers during deployment
  4.  **CloudWatch**, so CodeDeploy can send logs and metrics so we can monitor what's happening
  5.  **S3**, so CodeDeploy can access the build artifacts stored in S3 buckets

-  Select **Next**.
-  Enter `NextWorkCodeDeployRole` as the **Role name**.
-  Add a description to help us remember why we created this role.

```text
Allows CodeDeploy to call AWS services such as Auto Scaling on your behalf.

Created as a part of NextWork's Cl/CD Pipeline series.
```

-  Review the **Permissions policies** and make sure `AWSCodeDeployRole` is attached.
-  Select **Create role**.
-  Head back to the **CodeDeploy deployment group** configuration tab.
-  Looks like we'll need to refresh the page to use the new service role!
-  Refresh the page, and re-enter `nextwork-devops-cicd-deploymentgroup` as **Deployment group name**.
-  Select the newly created `NextWorkCodeDeployRole` as the **Service role**.
-  Choose **In-place** as the **Deployment type**.

**In-place deployment** updates the application on existing instances. During the deployment, the application on the instances is stopped, the new version is installed, and then the application is restarted. This can cause a brief downtime during deployment. For most production applications, blue/green is preferred because of the zero downtime and easy rollback, but for our learning project, in-place is simpler and more cost-effective.

-  Under **Environment configuration**, select **Amazon EC2 instances**.
-  In **Tag group 1**, enter `role` as the Key.
-  Enter `webserver` as the **Value**.

We are using tags here so that CodeDeploy can identify the target EC2 instances for deployment. In our CloudFormation template, we tagged our EC2 instance with `role: webserver`. CodeDeploy will use this tag to find and deploy to the correct instance.

Check the line below our tag settings - we might notice that **1 unique matched instance** is found.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-63.png)

-  Click **Click here for details** to view the matched instance.
-  We'll see an EC2 instance called **NextWorkCodeDeployEC2Stack::WebServer** - that's the EC2 instance we launched from our CloudFormation template. This confirms to us that the web app will be deployed onto that instance instance.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-64.png)

-  Now let's head back to our CodeDeploy **Deployment group** set up.
-  Under **Agent configuration with AWS Systems Manager**, select **Now and schedule updates** and **Basic scheduler** with **14 Days**.

**CodeDeploy Agent** is a software that lets our EC2 instance communicate with CodeDeploy. Whenever we initiate a deployment, it's the CodeDeploy Agent that receives the deployment instructions (i.e. bash scripts) from CodeDeploy and carries them out on the EC2 instance. Setting it to update every 14 days simply makes sure our agent software is always up to date to the latest version that AWS released.

-  In **Deployment settings**, keep the default `CodeDeployDefault.AllAtOnce`

Deployment settings help us control how quickly we'd like to roll out our application. `CodeDeployDefault.AllAtOnce` deploys the application to all instances in the deployment group at the same time. It's the fastest option, but also the riskiest - if something goes wrong, all our instances could be affected at once.

**How do you know what deployment setting to choose?** For production environments, we might choose more conservative options like `OneAtATime` (update one instance, make sure it works, then move to the next) or `HalfAtATime` (update 50% of instances, verify, then do the rest). These slower approaches reduce risk by limiting the blast radius of any potential issues.

For production systems with hundreds of instances, these settings become crucial. Imagine updating our banking app on all servers simultaneously vs. trying it on 10% first to make sure customers can still access their accounts! But since we only have one instance in our project, the cautious approach doesn't offer much benefit. We're using `AllAtOnce` in this project because we only have one instance and we're learning - speed is more important than caution here!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-65.png)

-  Deselect **Enable load balancing**.

**Load balancing** directs visitors to whichever server is least busy at the moment. Instead of everyone lining up at one server (which could get overwhelmed), traffic is distributed across multiple servers. When a visitor tries to access our website, they actually connect to the load balancer first. The load balancer then decides which server should handle this particular request - typically choosing the least busy one.

We're **not using load balancing** in this project because we only have one server - there's nothing to balance between! But in a production environment, we'd almost always use it to improve the availability and performance of our application.

-  Select **Create deployment group**.

**Step - 4 : Create and Verify Deployment**

It's time to put everything together and deploy our web application to the EC2 instance!
-  In the deployment group details page, select **Create deployment**.

CodeDeploy **deployment** represents a single update to our application, with its own unique ID and history. When we create a deployment, we're telling CodeDeploy:
  1.  Which version of the application to deploy (the revision)
  2.  Where to deploy it (the deployment group)
  3.  How to deploy it (the deployment settings) 

CodeDeploy then orchestrates the entire process - stopping services, copying files, running scripts, starting services - and keeps track of whether it succeeds or fails. We can monitor it happening in real-time and see a detailed log of each step.

-  Some of this deployment configuration is already pre-filled for us.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-66.png)

-  Under **Revision type**, make sure **My application is stored in Amazon S3** is selected. That's because our deployment artifact is inside an S3 bucket!
-  Head back to our S3 bucket called `nextwork-devops-cicd`.
-  Click into the `nextwork-devops-cicd-artifact` build artifact.
-  Copy the file's S3 URI.
-  Paste the S3 URI into the **Revision location** field in CodeDeploy.
-  Select **.zip** as the **Revision file type**.

Revision location is the place where CodeDeploy looks to find our application's build artifacts. We're using the S3 bucket that's storing our WAR file, so CodeDeploy knows where to find the latest version of our web app it's deploying to the deployment EC2 instances!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-67.png)

-  Next, we'll leave **Additional deployment behavior settings** as default.

These settings give us extra flexibility when we need to handle special cases without changing our standard deployment settings:
  1.  **Additional deployment behaviors settings** let us configure options like how to handle file permissions during deployment or whether to immediately allow traffic to new instances.
  2.  **Deployment group overrides** let us to override deployment group settings for a specific deployment - maybe we normally deploy one instance at a time (safe but slow), but for a critical security fix, we want to override that and deploy to all instances at once (faster but riskier).

-  Select **Create deployment**.
-  Off we go! CodeDeploy kicks off a deployment of our web app.
-  Scroll down to **Deployment lifecycle events** and monitor the events by clicking **View events**.
-  See the lifecycle events progressing, such as `BeforeInstall`, `ApplicationStart`, etc. These are the events we defined in `appspec.yml`!

-  Whoops! After a few minutes of waiting, we might notice that we **hit an error**...
-  Why do you think your deployment failed?
-  Don't forget, CodeDeploy is deploying our web app by grabbing the latest build artifact from our S3 bucket.
-  When was the last time we ran a build on CodeBuild? 🤔
-  Aha - our last time running a build was before we added appspec and the deployment scripts!
-  Because of this, our deployment instance isn't getting any of the scripts we've written - causing the error we're seeing now.
-  Head back to our **CodeBuild build project**, and **rebuild your project**.
-  Once our second build is a success, **return to CodeDeploy and retry the deployment**.
-  Wait until the deployment status says **Success**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-69.png)

-  Select the **Instance ID** in the **Deployment lifecycle events** panel. This takes us to the deployment EC2 instance we launched with CloudFormation.
-  Get the **Public IPv4 DNS** of our EC2 instance from the EC2 console.
-  Open the **Public IPv4 DNS** in a web browser.
-  It might take a minute or two for the application to become fully accessible after deployment.
-  WOOOHOOO! **Welcome to our application**!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-DevOps-1/blob/5c3ed52a2d1eb233458216cb0f097061f6b8ac1c/Images/DevOps-70.png)

If we don't see the application! Check whether our URL is using `https` - if it is, change it to `http` in the browser. This is because the EC2 instance's security group lets in connections on port 80 (HTTP) but not port 443 (HTTPS).

**Congratulations!** You've successfully automated a web app's deployment to EC2 using AWS CodeDeploy!

---
## Build a CI/CD Pipeline with AWS
**CI/CD pipeline** automatically builds, tests, and deploys our code changes. Instead of manually running these steps, **CodePipeline** manages the entire process from code commit to deployment. This means faster releases, fewer errors, and more time to focus on writing great code rather than managing the deployment process.

So far, we've built CodeBuild projects that compile and test our code, and CodeDeploy applications that handle deployment. But there's still a missing piece: **how do these services work together automatically?**

Right now, we would need to:
1. **Manually** build our project again if the code changes.
2. **Manually** track which build artifacts should be deployed.
3. **Manually** start deployments for new build artifacts.
4. **Manually** restart the process if something fails.

**CodePipeline** solves these problems by orchestrating our entire workflow, and giving us the visibility into the entire process in one place. It can:
1. **Automatically** detect code changes in our GitHub repository.
2. **Automatically** trigger CodeBuild to build a new project.
3. **Automatically** start CodeDeploy with the new build artifacts.
4. **Automatically** roll back a change if something fails.

Using CodePipeline transforms separate tools into a true CI/CD system, making deployments consistent, reliable, and fully automated - with no manual steps required from us!

**Step - 1 : Set Up our Pipeline**

We'll start by setting up the basic pipeline structure and configuring its settings.
-  Head to the `CodePipeline` console.
-  Select the **Getting started** page. Welcome to CodePipeline!

With CodePipeline, we can create a workflow that automatically moves our code changes through the build and deployment stage. In our case, we'll see how a new push to our GitHub repository automtically triggers a build in CodeBuild (continuous integration), and a then a deployment in CodeDeploy (continuous deployment)!

Using CodePipeline makes sure our deployments are consistent, reliable and happen automatically whenever we update our code - with less risk of human errors! It saves us time too.

-  In the CodePipeline dashboard, Select **Create pipeline**.
-  Select **Build custom pipeline**.

By choosing to **build a custom pipeline**, we get to define each stage and action step-by-step. This gives us a deeper understanding of how CodePipeline works and allows us to tailor the pipeline precisely to our needs. For learning purposes, building a custom pipeline from scratch is the best way to go !

![image alt](DevOps-71)

Click **Next**.

**Configure Pipeline Settings**
-  Name our pipeline `nextwork-devops-cicd`

![image alt](DevOps-72)

-  Under **Execution mode**, select **Superseded**.

**Execution mode** determines how CodePipeline handles multiple runs of the same pipeline.
1. In **Superseded** mode, if a new pipeline execution is triggered while another execution is already in progress, the newer execution will immediately take over and cancel the older one. This is perfect for making sure only the latest code changes are processed, which is exactly what we want for our CI/CD pipeline!
2. In **Queued** mode, executions are processed one after another. If a pipeline is already running, any new executions will wait in a queue until the current
execution finishes.
3. **Parallel** mode allows multiple executions to run at the same time, completely independently of each other. This can speed up the overall processing time if we have multiple branches or code changes that can be built and deployed concurrently.

![image alt](DevOps-73)

-  Under **Service role**, select **New service role**. Keep the default role name.

A **service role** is a special type of IAM role that AWS services like CodePipeline use to perform actions on our behalf. It's like giving CodePipeline permission to access other AWS resources it needs to run our pipeline, such as S3 buckets for storing artifacts or CodeBuild for building our code.

![image alt](DevOps-74)

-  Expand **Advanced settings**.
-  Leave the default settings for **Artifact store**, **Encryption key**, and **Variables**.

**Artifact store**: Without an artifact store, there's no way for our build outputs to be passed to deployment! This S3 bucket is where CodePipeline automatically saves the files created at each stage - like our source code from GitHub and the build artifacts from CodeBuild - making them available to the next stage in our pipeline.

**Encryption key**: By default, CodePipeline encrypts everything in our artifact store using AWS managed keys. This keeps our code and build artifacts secure while they're being stored and transferred between stages. For most projects, this default encryption is perfectly sufficient.

**Variables**: Right now we might be manually tracking information like version numbers or build timestamps. Pipeline variables solve this by letting us pass dynamic values between different stages automatically. While we won't use variables in this project, they become essential in more complex pipelines when we need information generated in one stage (like a build number) to be available in another stage (like deployment).

![image alt](DevOps-75)

-  Click **Next**.

![image alt](DevOps-76)

We've configured the basic settings for our pipeline. Let's move on to setting up the Source stage.

**Step - 2 : Configuring the Source, Build and Deploy Stages**

Now we're ready to pull together all the different parts of our CI/CD architecture!

**Source Stage**

Now, let's configure the Source stage of our pipeline. This is where we'll tell CodePipeline where to fetch our source code from.
-  In the **Source provider** dropdown, select **GitHub (via GitHub App)**.

![image alt](DevOps-77)

The **Source stage** is the very first step in any CI/CD pipeline. Its job is simple but crucial: it fetches the latest version of our code from our chosen repository whenever there are updates. Without this stage, our pipeline would have nothing to build or deploy. CodePipeline supports various source providers, but for this project, we're using GitHub because that's where our web app's code is stored.

-  Under **Connection**, select our existing GitHub connection

![image alt](DevOps-78)

-  Under **Repository name**, select `nextwork-web-project`.
-  Under **Default branch**, select `master`.

In Git, a **branch** is like a parallel timeline of our project. It allows us to work on new features or bug fixes without affecting the main codebase. The master branch is typically considered the main branch, representing the stable, production-ready code. By specifying the master branch as the default branch, we're telling CodePipeline to monitor this branch for changes and trigger the pipeline whenever there's a commit to it.

![image alt](DevOps-79)

-  Under **Output artifact format**, leave it as **CodePipeline default**.

**Output artifact format** determines how CodePipeline packages the source code it fetches from GitHub.
1. **CodePipeline default**: This option packages the source code as a ZIP file, which is efficient for most deployment scenarios. It does not include Git metadata about the repository.
2. **Full clone**: This option provides a full clone of the Git repository as an artifact, including Git history and metadata. This is useful if our build process requires Git history, but it results in a larger artifact size.

-  Make sure that **Webhook events** is checked under **Detect change events**.

![image alt](DevOps-80)

**Webhook events** let CodePipeline automatically start our pipeline whenever code is pushed to our specified branch in GitHub. This is what makes our pipeline truly "continuous" – it reacts to code changes in real-time!

Webhooks are like digital notifications. When we enable webhook events, CodePipeline sets up a webhook in our GitHub repository. This webhook is configured to listen for specific events, such as code pushes to the `master` branch. Whenever we push code to the `master` branch, GitHub sends a webhook event (a notification) to CodePipeline. CodePipeline then automatically starts a new pipeline execution in response to this event. It's a seamless way to automate 
our CI/CD process!

-  Click **Next**.

We've set up the **Source stage** of our pipeline. We're now ready to set up the Build stage.

**Build stage**

The **Build stage** is where our source code gets transformed into a deployable build artifact. We'll tell CodePipeline to use AWS CodeBuild to compile and package our web application.
-  In the **Build provider** dropdown, select **AWS CodeBuild** from **Other build providers**.

![image alt](DevOps-81)

-  Under **Project name**, select our existing CodeBuild project.
-  In the **Project name** dropdown, search for and select `nextwork-devops-cicd`.
-  Leave the default settings for **Environment variables**, **Build type**, and **Region**.

![image alt](DevOps-82)

-  Under **Input artifacts**, **SourceArtifact** should be selected by default.

**Input artifacts** are the outputs from the previous stage that are used as inputs for the current stage. In our Build stage, we're using SourceArtifact, which is the ZIP file containing our source code that was outputted by the Source stage.

-  Click **Next**.

We've configured the **Build stage** of our pipeline. We're now ready to move on to the next stage.

**Skip Test Stage**
-  On the **Add test stage** page, click **Skip test stage**.

The **Test stage** is where we automate testing our application. This can include different types of tests, likes:
1. **Unit tests**: Testing individual components or functions of our code.
2. **Integration tests**: Testing how different parts of our application work together.
3. **UI tests**: Testing the user interface to make sure it works correctly.

The Test stage helps ensure the quality of our code and catch any issues before they reach production. While we're skipping it for this project to simplify things, in real-world scenarios, a Test stage is essential for maintaining software quality and reliability.

**Deploy Stage**
-  In the **Deploy provider** dropdown, select **AWS CodeDeploy**.

![image alt](DevOps-83)

The **Deploy stage** is the final step in our pipeline. It's responsible for taking the application artifacts (the output from the Build stage) and deploying them to the target environment, which in our case is an EC2 instance.

-  Under **Input artifacts**, **BuildArtifact** should be selected by default.
-  Under **Application name**, select our existing CodeDeploy application

![image alt](DevOps-84)

-  Under **Deployment group**, select our existing CodeDeploy deployment group.

![image alt](DevOps-85)

-  Check the box for **Configure automatic rollback on stage failure**.

**Automatic rollback** is a safety net for our deployments. By enabling it, we're telling CodePipeline that if the Deploy stage fails for any reason, it should automatically revert to the last successful deployment. This helps minimize downtime and ensures that our application remains stable, even if a new deployment goes wrong.

-  Click **Next**.

Awesome! We've configured the Deploy stage of our pipeline. We're just one step away from creating your pipeline.

**Step - 3 : Run Our Pipeline!**

Let's watch our pipeline run for the first time! This will help us verify that everything is working correctly.

**Review Our Pipeline**
-  On the **Review** page, take a moment to review all the settings we've configured for our pipeline.

![image alt](DevOps-86)

![image alt](DevOps-87)

![image alt](DevOps-88)

-  Once we've reviewed all the settings and confirmed they are correct, click **Create pipeline**.

**Run Our Pipeline**
-  After clicking **Create pipeline**, we will be taken to the pipeline details page.
-  Note the pipeline diagram at the top of the page.

![image alt](DevOps-89)

-  CodePipeline automatically starts executing the pipeline as soon as it's created.
-  We can see the progress of each stage in the pipeline diagram. The stages will transition from grey to blue (in progress) to green (success) as the pipeline executes.

As our pipeline runs, each stage will display a status:
1. **Grey**: Stage has not started yet.
2. **Blue**: Stage is currently in progress.
3. **Green**: Stage has completed successfully.
4. **Red**: Stage has failed.

-  Wait for the pipeline execution to complete. We can monitor the status of each stage in the pipeline diagram.
-  To see more details about each execution, click on the **Executions** tab above the pipeline diagram.

**Pipeline executions** represent each instance of our pipeline running. Every time our pipeline is triggered (either manually or automatically by a webhook), a new execution is created. Each execution has a unique ID and shows the status and details of each stage in that particular run.

![image alt](DevOps-90)

-  To view details of a specific stage execution, click on the **Stage ID** link in the **Executions** tab. For example, click on the **Source** stage ID to see details about the source code retrieval.

![image alt](DevOps-91)

-  Wait for all stages in the pipeline diagram to turn **green**, which means our pipeline is all set up using the latest code change!

![image alt](DevOps-92)


**Step - 4 : Test Our Pipeline!**

It's time for the ULTIMATE test for this project... let's see how CodePipeline handles a code change! Testing with a code change will confirm that our pipeline is automatically triggered and deploys our updates.

-  Open our web app code in our local IDE (e.g., VS Code).
-  Open the `index.jsp` file located in `src/main/webapp/`.
-  Add a new line in the `<body>` section of `index.jsp`:

```html
<p>If you see this line, that means your latest changes are automatically deployed into production by CodePipeline!</p>
```

![image alt](DevOps-93)

-  Save the `index.jsp` file.
-  Open our terminal and navigate to our local git repository for the web app.
-  Commit and push the changes to our GitHub repository using the following commands:

```bash
git add .
git commit -m "Update index.jsp with a new line to test CodePipeline"
git push origin master
```

![image alt](DevOps-94)

-  Go back to the CodePipeline console and watch our pipeline react to the code change.
-  We should see a new execution starting automatically after we push the changes to GitHub.

![image alt](DevOps-95)

-  Click on the **Source** stage box in the pipeline diagram.
-  Scroll down in the stage details panel to see the commit message.

![image alt](DevOps-96)

-  Click on the **Commit ID** link in the Source stage details panel.

![image alt](DevOps-97)

-  This should open the commit page in our GitHub repository in a new browser tab.
-  Verify that the commit page shows the code changes we just pushed (the new line we added to index.jsp)!

![image alt](DevOps-98)

-  Wait for the **Build** and **Deploy** stages to complete successfully (turn green) in the CodePipeline console.

![image alt](DevOps-99)

**Verify Automated Deployment**

Let's try accessing the web app to see our code change live!

![image alt](DevOps-100)

-  To find the Public IPv4 DNS, in the CodePipeline console, click on the **Deploy** stage, then click on the **CodeDeploy** link in the details panel.
-  In the CodeDeploy console, scroll down to **Deployment lifecycle events** and click on the **Instance ID**.
-  On the EC2 instance summary page, copy the **Public IPv4 DNS**.

![image alt](DevOps-101)

-  Paste the copied Public IPv4 DNS in a new browser tab and press **Enter**.
-  We should see our web application with the new line we added:

![image alt](DevOps-102)

This confirms that our latest code changes were automatically deployed by CodePipeline.

Our CI/CD pipeline is now automatically building and deploying our web application whenever we push changes to GitHub.

**Step -5 : Delete our resources**

Now that we've successfully built, tested, and rolled back our CI/CD pipeline, it's time to clean up the AWS resources we created to avoid incurring any unnecessary costs.

**CloudFormation**
-  Head to the `CloudFormation` console.
-  Select our deployment EC2 stack.
-  Click **Delete**.
-  Confirm the deletion by clicking **Delete stack**.

**CodePipeline**
-  Head to the `CodePipeline` console.
-  Select **Pipelines** from the left-hand menu.
-  Select the pipeline named `nextwork-devops-cicd`.
-  Select **Delete**.
-  Type `delete` in the confirmation field.
-  Select **Delete**.

**CodeDeploy**
-  Head to the `CodeDeploy` console.
-  Select **Applications** from the left hand menu.
-  Select the `nextwork-devops-cicd` application.
-  Click **Delete application**.
-  Confirm the deletion by typing `delete` and clicking **Delete**.

**CodeBuild**
-  Head to the `CodeBuild` console.
-  Select **Build projects** from the left hand menu.
-  Select the `nextwork-devops-cicd` project.
-  Click **Delete build project**.
-  Confirm the deletion by typing `delete` and clicking **Delete**.

**CodeArtifact**
-  Head to the `CodeArtifact` console.
-  Select **Repositories** from the left hand menu.
-  Select the `nextwork-devops-cicd` repository.
-  Click **Delete repository**.
-  Confirm the deletion by typing `delete` and clicking **Delete repository**.
-  Select **Domains** from the left hand menu.
-  Select the `nextwork` domain.
-  Click **Delete domain**.
-  Confirm the deletion by typing `delete` and clicking **Delete domain**.

**CodeConnection**
-  Expand the **Settings** arrow at the bottom of the left hand navigation panel.
-  Select **Connections**.
-  Select our connection.
-  Select **Delete**
-  Confirm the deletion by typing `delete` and clicking **Delete**.

**IAM Roles and Policies**
-  Head to the `IAM` console.
-  Select **Roles** from the left hand menu.
-  Search for and delete the following roles:
    -  `ec2-instance-nextwork-cicd`
    -  `aws-codedeploy-role`
    -  `codebuild-nextwork-devops-cicd-service-role`
    -  `AWSCodePipelineServiceRole`
-  Select **Policies** from the left hand menu.
-  Search for and delete the following polcies:
    -  `codeartifact-nextwork-consumer-policy`
    -  `CodeBuildBasePolicy-nextwork-devops-cicd`
    -  `CodeBuildCloudWatchLogsPolicy-nextwork-devops`
    -  `CodeBuildCodeConnectionsSourceCredentialsPolicy-nextwork`
    -  `AWSCodePipelineServiceRole`
 
**Development EC2 instance**
-  Head to the `EC2` console.
-  Select **Instances** from the left hand menu.
-  Select the `nextwork-devops-yourname instance`.
-  Click **Instance state**, then **Terminate instance**.
-  Confirm termination by clicking **Terminate**.

**S3 bucket**
-  Head to the `S3` console.
-  Select **Buckets** from the left hand menu.
-  Select the `nextwork-devops-cicd` S3 bucket (our build artifacts bucket)
-  Click **Empty bucket**.
-  Confirm emptying the bucket by typing **permanently delete** and clicking **Empty bucket**.
-  Once the bucket is empty, select the bucket again and click **Delete bucket**.
-  Confirm deletion by typing the bucket name and clicking **Delete bucket**.
-  Also delete the bucket created by **CloudFormation**!
    -  CloudFormation automatically creates a new bucket to store templates we upload when we create a new stack. The bucket's name should start with `cf`.
    -  CodePipeline also creates a new bucket to store artifacts created in the pipeline. The bucket's name should start with `codepipeline`.

#  AWS-X-DevOps
##  Project Overview
In this project I'll build from scratch a CI/CD pipeline that automates the build and deployment of a web app. A **CI/CD (Continuous Integration and Continuous Deployment/Delivery)** pipeline is a tool that automates the steps from development (i.e. writing changes to code) to deployment (i.e. making the code changes available to users). This helps engineering teams build and release software much, much faster! That's why most modern tech companies use CI/CD pipelines to ship software to users, and why it's a crucial skill for DevOps Engineers.

---
##  Table Of Content
-  [Set Up a Web App in the Cloud](#Set-Up-a-Web-App-in-the-Cloud)
-  [Connect a GitHub Repo with AWS](Connect-a-GitHub-Repo-with-AWS)
-  [Secure Packages with CodeArtifact](Secure-Packages-with-CodeArtifact)
-  [Continuous Integration with CodeBuild](Continuous-Integration-with-CodeBuild)

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
-  Use the following command to connect to your EC2 instance:
`ssh -i [PATH TO YOUR .PEM FILE] ec2-user@[YOUR PUBLIC IPV4 DNS]`
    -  Replace **[PATH TO YOUR .PEM FILE]** with the actual path to your private key file (e.g., ~/Desktop/DevOps/nextwork-keypair.pem). Delete the square brackets!
    -  Replace **[YOUR PUBLIC IPV4 DNS]** with the Public DNS you just found. Delete the square brackets!
-  Your terminal will ask if you want to continue connecting to this EC2 instance. This is SSH's way of asking if you trust this server.
-  Enter `yes` to continue connecting.
-  Congrats! You've connected your EC2 instance via SSH.

![image alt](DevOps-11)

**Step - 4 : Install Apache Maven and Amazon Corretto 8**

**Apache Maven** is a tool that helps developers build and organize Java software projects. It's also a package manager, which means it automatically download any external pieces of code your project depends on to work. It uses something called **archetypes**, which are like templates, to lay out the foundations for different types of projects e.g. web apps. We'll use Maven later on to help us set up all the necessary web files to create a web app structure, so we can jump straight into the fun part of developing the web app sooner.

- Install Apache Maven using the commands below. You can copy and paste **all** of these lines into the terminal together, no need to run them line by line.

```bash
wget https://archive.apache.org/dist/maven/maven-3/3.5.2/binaries/apache-maven-3.5.2-bin.tar.gz

sudo tar -xzf apache-maven-3.5.2-bin.tar.gz -C /opt

echo "export PATH=/opt/apache-maven-3.5.2/bin:$PATH" >> ~/.bashrc

source ~/.bashrc
```

- Once you've pasted these commands, don't forget to press **Enter** on your keyboard.

![image alt](DevOps-12)

- Now we're going to install Java 8, or more specifically, **Amazon Correto 8**.

**Java** is a popular programming language used to build different types of applications, from mobile apps to large enterprise systems. Maven, which we just downloaded, is a tool that NEEDS Java to operate. So if we don't install Java, we won't be able to use Maven to generate/build our web app. **Amazon Corretto 8** is a version of Java that we're using for this project. It's free, reliable and provided by Amazon.

- Run these commands:

```bash
sudo dnf install -y java-1.8.0-amazon-corretto-devel

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-amazon-corretto.x86_64

export PATH=/usr/lib/jvm/java-1.8.0-amazon-corretto.x86_64/jre/bin/:$PATH
```

![image alt](DevOps-13)

- To verify that Maven is installed correctly, run the following command next: `mvn -v`

![image alt](DevOps-14)

- To verify that you've installed Java 8 correctly, run this next: `java -version`

![image alt](DevOps-15)

**Step - 5 : Create the Application**

We've assembled both Maven and Java into our EC2 instance. Now let's cut straight to generating the web app!

- Run Maven commands in your terminal to generate a Java web app.
- Use **mvn** to generate a Java web app. To do this, use these commands:

```bash
mvn archetype:generate \
   -DgroupId=com.nextwork.app \
   -DartifactId=nextwork-web-project \
   -DarchetypeArtifactId=maven-archetype-webapp \
   -DinteractiveMode=false
```

When you run **mvn** commands, you're asking Maven to perform tasks (like creating a new project or building an existing one). The **mvn archetype:generate** command specifically tells Maven to create a new project from a template (which Maven calls an archetype). This command sets up a basic structure for your project, so you don't have to start from scratch.

- Watch out for a **BUILD SUCCESS** message in your terminal once your application is all set up.

![image alt](DevOps-16)

Now we'll connect VS Code to your EC2 instance so you can see and edit the web app you've just created. Earlier we connected with SSH in the terminal lets us send text commands to our EC2 instance, but we don't get all the benefits of having an **IDE** like VS Code. When we connect VS Code itself to our EC2 instance (not just your terminal), it unlocks VS Code’s **IDE features (like file navigation and code editing)** directly on our EC2 instance. This will make it so much easier for us to edit and manage your web app.

- Click on the **Extensions** icon at the side of your VS Code window.

![image alt](DevOps-17)

- In the search bar, type `Remote - SSH` and click **Install** for the extension.

**Remote - SSH extension** in VS Code lets us connect directly via SSH to another computer securely over the internet. This lets us use VS Code to work on files or run programs on that server as if we were doing it on our own computer, which will come in handy when we edit the web app in your EC2 instance!

- Click on the double arrow icon at the bottom left corner of your VS Code window. This button is a shortcut to use Remote - SSH.
- Select **Connect to Host...**
- Select **+ Add New SSH Host...**

An **SSH Host** is the computer or server we're connecting to using SSH. It's the target location where we want to run commands or manage files; in our case, the SSH Host is the EC2 instance we created.

- Enter the SSH command you used to connect to your EC2 instance: `ssh -i [PATH TO YOUR .PEM FILE] ec2-user@[YOUR PUBLIC IPV4 DNS]`
  - Replace **[PATH TO YOUR .PEM FILE]** with the actual path to your private key file (e.g., ~/Desktop/DevOps/nextwork-keypair.pem). Delete the square brackets!
  - Replace **[YOUR PUBLIC IPV4 DNS]** with the Public DNS you just found. Delete the square brackets!
- Select the configuration file at the top of your window. It should look similar to `/Users/username/.ssh/config`
- A **Host added!** popup will confirm that you've set up your SSH Host.
- Select the blue **Open Config** button on that popup.
- Confirm that all the details in your configuration file look correct:
  - **Host** should match up with our EC2 instance's IPv4 DNS.
  - **IdentityFile** should match up to nextwork-keypair.pem's location in our local computer.
  - **User** should say ec2-user

![image alt](DevOps-18)

- Now you’re ready to connect VS Code with your EC2 instance!
- Click on the double arrow button on the bottom left corner and select **Connect to Host** again.
- You should now see your EC2 instance listed at the top.
- Select the EC2 instance and off we go to a new VS Code window.
- Check the bottom right hand corner of your new VS Code window - it should show our EC2 instance's IPV4 DNS.

Now let's open up your web app's files.
- From VS Code's left hand navigation bar, select the **Explorer** icon.
- Select **Open folder**.
- At the top of your VS Code window, you should see a drop down of different file and folder names. Ooooo, this is VS Code asking you which specific file/folder you'd like to open!
- Enter `/home/ec2-user/nextwork-web-project`.
- Press **OK**.
- VS Code might show you a popup asking if you trust the authors of the files in this folder. If you see this popup, select **Yes, I trust the authors**.
- Check your VS Code window's file explorer again - a folder called **nextwork-web-project** is here!
- Try expanding all the subfolders in the file explorer. All folders have a `>` icon next to their name.

![image alt](DevOps-19)

All the files and subfolders we see under **nextwork-web-project** are parts of a web app! We can start working right away on the content we want to display on our web app, since Maven's taken care of the basic structuring and setup.

- Exploring done! So how can VS Code help you edit your application files? Let's find out.
- From your file explorer, click into **index.jsp**.

**index.jsp** is a file used in Java web apps. It's similar to an HTML file because it contains markup to display web pages. However, index.jsp can also include Java code, which lets it generate dynamic content. This means content can change depending on things like user input or data from a database. Social media apps are great examples of web apps because the content you see is always changing, updating and personalised to you. HTML files are static and can’t include Java code. That's why it's so important to install Java in your EC2 instance - so you can run the Java code in your web app!

- Welcome to editor view of **index.jsp**. Now we're really using VS Code's IDE abilities - editing code is much easier here than in the terminal.
- Let's try modifying index.jsp by changing the placeholder code to the code snippet below. Don't forget to replace **{YOUR NAME}** from the following code with your name:

```html
<html>

<body>

<h2>Hello {YOUR NAME}!</h2>

<p>This is my NextWork web application working!</p>

</body>

</html>
```

- Save the changes you've made to **index.jsp** by selecting **Command/Ctrl + S** on your keyboard.

![image alt](DevOps-20)

---

## Connect a GitHub Repo with AWS
**Step - 1 : Set up GitHub**

Now that our development environment is ready, the next step is to set up **Git** on our EC2 instance. Git is like a time machine and filing system for our code. It tracks every change we make, which lets us go back to an earlier version of our work if something breaks. We can also see who made specific changes and when they were made, which makes teamwork/collaboration a lot easier. Git is often called a **version control system** since it tracks our changes by taking snapshots of what our files look like at specific moments, and each snapshot is considered a 'version'.

- Install Git on your EC2 instance.
- Open your EC2 instance's terminal.
- In the terminal, run these commands to install Git:

```bash
sudo dnf update -y
sudo dnf install git -y
```

- Verifiy the installation:

```bash
git --version
```

![image alt](DevOps-21)

**GitHub** is a place for engineers to store and share their code and projects online. It's called GitHub because it uses Git to manage your projects' version history.

- Head to GitHub's signup page.
- Follow the prompts to create your account by entering your email, creating a password, and choosing a username.
- Complete one of their bot verification tasks. Switch the task type to Audio if the Visual task crashes your website.
- Once your account is created, confirm your email address with a verification code sent to your inbox.
- Log into your GitHub account once you've verified your email.
- Welcome to your GitHub account!

If **Git** is the tool for tracking changes, think of **GitHub** as a storage space for different version of your project that Git tracks. Since GitHub is a cloud service, it also lets us access our work from anywhere and collaborate with other developers over the internet. Even though our code is on a cloud server like EC2, GitHub helps us to use Git and see our file changes in a more user-friendly way. It's just like how using an IDE (VSCode) makes editing code easy. GitHub is also especially useful in situation where we're working in teams and need to share your updates and reviews to a shared code base.

Nice, we're ready to set up a **new repository on GitHub**! To store our code using Git, we create repositories (aka 'repos'), which are folders that contain all our project files and their entire version history. Hosting a repo in the cloud, like on GitHub, means we can also collaborate with other engineers and access our work from anywhere.

- After signing in to GitHub, click on the `+` icon next to your GitHub profile icon at the top right hand corner.
- Select **New repository**.
- Select **Create repository**.
- This loads up a new page where you can create a repository.
- Under **Owner**, click on the **Choose an owner dropdown** and select your GitHub username.
- Under **Repository name**, enter **nextwork-web-project**
- For the **Description**, enter `Java web app set up on an EC2 instance`.
- Choose your visibility settings. We'd recommend selecting **Public** to make your repository available for the world to see.
- Select **Create repository**.

![image alt](DevOps-22)

**Step - 2 : Commit and Push Your Changes to GithHub**

So now we have a place in the cloud that will store our code and track the changes we make. But this storage folder (our GitHub repo) still doesn't know where our web app files are. Lets connect our GitHub repo with our web app project stored in your EC2 instance.

- Head back to our **VSCode remote window**. Make sure it's still SSH connected to our EC2 instance by checking the bottom left corner.
- Check that we are in the right folder by running this command in our terminal: `pwd`
- If not in nextwork-web-project, use `cd` to navigate terminal into our web app project.
- Now let's tell Git that we'd like to track changes made inside this project folder.

```bash
git init
```

To start using Git for our project, we need to create a **local repository** on our computer. When we run `git init` inside a directory e.g. nextwork-web-project, it sets up the directory as a **local Git repository** which means changes are now tracked for version control. The local repository is where we use Git directly on our own EC2 instance. The edits we make in our local repo is only visible to us and isn't shared with anyone else yet. This is different to the GitHub repository, which is the remote/cloud version of our repo that others can see.

![image alt](DevOps-23)

This is just Git giving us a heads-up about naming our main branch **master** and suggesting that we can choose a different name like 'main' or 'development' if we want. You can think of Git branches as parallel versions or 'alternate universes' of the same project. For example, if you wanted to test a change to your code, you can set up a new branch that lets you diverge from the original/main version of your code (called master) so you can experiment with new features or test bug fixes safely. We won't create new branches in this project and we'll save all new changes directly to master, but it's best practice to make all changes in a separate branch and then merge them into master when they're ready.

- Head back to your GitHub repository's page.
- In the blue section of the page titled **Quick setup — if you’ve done this kind of thing before**, copy the HTTPS URL to your repository page. It will look like `https://github.com/username/nextwork-web-project.git`
- Head back to your terminal in VSCode.
- Run this command. Don't forget to replace **[YOUR GITHUB REPO LINK]** with the link you've just copied.

```bash
git remote add origin [YOUR GITHUB REPO LINK]
```

Your local and GitHub repositories aren't automatically linked, so you'll need to connect the two so that updates made in your local repo can also reflect in your GitHub repo. When you set `remote add origin`, you're telling Git where your GitHub repository is located. Think of **origin** as a bookmark for your GitHub project's URL, so you don't have to type it out every time you want to send your changes there.

- To verify that the remote origin has been set up correctly, run the command:

```bash
git remote -v
```

- This command lists all configured remote repositories!
- You should see `origin` listed, with both `fetch` and `push` URLs pointing to your GitHub repository URL.

Next, we'll save our changes and push them into GitHub.
- Run this command in your terminal:

```bash
git add .
```

`git add .` stages all (marked by the '.') files in nextwork-web-project to be saved in the next version of your project. When you stage changes, you're telling Git to put together all your modified files for a final review before you commit them. This is incredibly handy because you get to see all your edits in one spot, which means its much easier to check if there were are mistakes or unwanted changes before you commit.

- Run this command next in your terminal:

```bash
git commit -m "Updated index.jsp with new content"
```

`git commit -m "Updated index.jsp with new content"` saves the staged changes as a snapshot in your project’s history. This means your project's version control history has just saved your latest changes in a new version. `-m` flag lets you leave a **message** describing what the commit is about, making it easier to review what changed in this version.

- Finally, run this command:

```bash
git push -u origin master
```

`git push -u origin master` uploads i.e. 'pushes' your committed changes to origin, which you've bookmarked as your GitHub repo. 'master' tells Git that these updates should be pushed to the master branch of your GitHub repo. By using `-u` you're also setting an 'upstream' for your local branch, which means you're telling Git to remember to push to master by default. Next time, you can simply run `git push` without needing to define origin and master.

- But Git can't push your work to the Github repository yet. It's now asking for a username!
- Enter your Github username, and press **Enter** on your keyboard.
- Next, enter your password. You'll notice that as you type this out, nothing shows on your terminal. This is totally expected - your terminal is hiding your input for your privacy. Press **Enter** on your keyboard when you've typed out your password, even if you don't see it printed out in your terminal.
- Hmmmm, now Git is letting us know that it can't actually accept our password.

GitHub phased out password authentication to connect with repositories over HTTPS - there are too many security risks and passwords can get intercepted over the internet. You need to use a `personal access token` instead, which is a more secure method for logging in and interacting with your repos. A token in GitHub is a unique string of characters that looks like a random password. For example, a GitHub token might look like **ghp_xHJNmL16GHSZSV88hjP5bQ24PRTg2s3Xk9ll**. As you can imagine, tokens are great for security because they're unique and would be very hard to guess.

Now that we know passwords won't work for authentication, we'll have to find a replacement. Let's generate an authentication token on GitHub!
- Head back into your browser with GitHub open.
- Select your profile icon and select **Settings**.
- Select **Developer settings** at the very bottom of the left hand navigation panel.
- Select **Personal access tokens**.
- Select **Tokens (classic)**.
- Select **Generate new token**.
- Select **Generate new token (classic)**.
- Give your token a descriptive note, like `Generated for EC2 Instance Access. This is a part of NextWork's DevOps Project`.
- Lower the token expiration limit from 30 days to **7 days**.
- Select the checkbox next to **repo**.
- Select **Generate token**.
- Make sure to copy your token now. Keep it safe somewhere else, you won't be able to see your token once you close this tab.
- Head to your VS Code terminal.
- Run `git push -u origin master` again, which will trigger Git to ask for your GitHub username.
-`Enter your username again.
- When Git asks for your password, **paste in your token instead**. Your terminal won't show your password for privacy reasons, so press **Enter** on your keyboard once you've pasted your token (even if you don't see it on screen).
- Looks like Github recognises your token and pushed your changes to your repository.

![image alt](DevOps-24)

This message appears when you successfully push changes to a GitHub repository. It shows the progress of transferring objects (like files and commits), how many objects were processed, and tells you that your local branch is now tracking the remote branch after the push.

- Head to your GitHub repository in your web browser.
- Refresh the page, and you’ll see your web app files in the repository, along with the commit message you wrote.

When you add, commit and push your changes, you might notice the terminal automatically sets two other things - your name and email address - before it asks for your GitHub username.

![image alt](DevOps-25)

Git needs author information for commits to track who made what change. If you don't set it manually, Git uses the system's default username, which might not accurately represent your identity in your project's version history.

- Run `git log` to see your history of commits, which also mentions the commit author's name.
- **EC2 Default User** isn't really your name, and the EC2 instance's IPv4 DNS is not your email. Let's configure your local Git identity so Git isn't using these default values.
- Run these commands in your terminal to manually set your name and email. Don't forget to replace **"Your name"** with your name (keep the quote marks), and **you@nextwork.org** with your email address.

```bash
git config --global user.name "Your Name"
git config --global user.email you@nextwork.org
```

We've set up your local Git identity, which means Git can associate our changes in the local repo to your name and email. This setup is best practice for keeping a clear history of who made which changes.

So we've learnt how to link your EC2 instance's files with a cloud repo, now let's see what happens when you make **new** changes to our web app files. We'll edit **index.jsp** again using VSCode, and run commands that pushes those changes to your GitHub repository too.

- Keep your GitHub page open, and switch back to your EC2 instance's VSCode window.
- Find **index.jsp** in your file navigator on the left hand panel.
- Find the line that says **This is my NextWork web application working!** and add this line below:

```html
<p>If you see this line in Github, that means your latest changes are getting pushed to your cloud repo :o</p>
```

![image alt](DevOps-26)

- Save your changes by pressing **Command/Ctrl + S** on your keyboard while keeping your **index.jsp** editor open.
- Head back to your GitHub tab and click into the **src/main/webapp** folders to find index.jsp.
- Click into **index.jsp** - have there been any updates to index.jsp in your GitHub repo?

![image alt](DevOps-27)

You won't see your changes in GitHub yet, because saving changes in your VSCode environment only updates your **local** repository. Remember that the local repository in VSCode is separate from your GitHub repository in the cloud. To make your changes visible in GitHub, you need to write commands that send (push) them from your local repository into your origin.

- Head back to your VSCode window.
- In the terminal, let's stage our changes: `git add .`
- Ready to see what changes are staged? Run `git diff --staged` next.

`git diff --staged` shows you the exact changes that have been staged compared to the last commit. Now you get to review your modifications in your code that you are about to save into your local repo's version history!

- Nice, these changes are what we want to save and send to GitHub, lets do that with these commands:

```bash
git commit -m "Add new line to index.jsp"
git push
```

- You might need to enter your username and token again to complete your push.

Right after you enter your username and token again, you can run `git config --global credential.helper store` to ask Git to remember your credential details for next time!

- Head back to your GitHub tab and refresh it - do you see your changes now?

![image alt](DevOps-28)

---

## Secure Packages with CodeArtifact
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

**Upstream repositories** are like **backup libraries** that your primary repository can access when it doesn't have what you need. If you didn't set up CodeArtifact or have an upstream repository, your build would fail because a package is missing! When your application looks for a package that isn't in your CodeArtifact repository, CodeArtifact will check its upstream repositories (like Maven Central in our case) to find it. Once found, Maven will then store a copy in your CodeArtifact repository for future use.

**Maven Central** is essentially the App Store of the Java world - it's the most popular public repository where developers publish and share Java libraries. When you're building Java applications, chances are you'll need packages from Maven Central. It contains virtually every popular open-source Java library out there, from database connectors to testing frameworks and UI components.

By connecting our **CodeArtifact repository** to **Maven Central**, we're setting up a system where we get the best of both worlds: access to all these public libraries, but with the added benefits of caching, control, and consistency that come with our private CodeArtifact repository.

- Click **Next**.
- You're now ready to set up your CodeArtifact domain!

CodeArtifact domain is like a folder that holds multiple repositories belonging to the same project or organization. We like using domains because they give you a single place to manage permissions and security settings that apply to all repositories inside it. This is much more convenient than setting up permissions for each repository separately, especially in large companies where many teams need access to different repositories. With domains, you can ensure consistent security controls across all your package repositories in an efficient way.

- Under **Domain selection**, choose **This AWS account**.
- Under **Domain name**, enter `nextwork`.
- Click **Next** to proceed.
- Review the details of your repository configuration, including the package flow at the top.

**Package flow diagram** shows you exactly how dependencies will travel to your application. When your project needs a dependency, it first looks in your CodeArtifact repository. If the package is already there, great! It uses that version. If not, CodeArtifact automatically reaches out to Maven Central to fetch it. The first time you request a package, it might take a moment longer as CodeArtifact fetches it from Maven Central. But every build afterwards will be faster because the package is now cached in your repository.

- Select the **Create repository** button to create your CodeArtifact repository.
- You'll be taken to the repository's details page.
- You should see a success message at the top of the page, telling us that the CodeArtifact repository `nextwork-devops-cicd` has been successfully created.

![image alt](DevOps-29)

**Step - 2 : Create and Attach an IAM Policy for CodeArtifact Access**

For Maven to start working with CodeArtifact, we need to create an **IAM role** that grants our **EC2 instance** the permission it needs to access CodeArtifact. Otherwise, Maven can try all it wants to command your EC2 instance to store and retrieve packages from CodeArtifact, but your EC2 instance simple wouldn't be able to do anything! And going another layer deeper, IAM roles are made of policies; so we need to create policies first before setting up the role.

- On your newly created repository's page, click the **View connection instructions** button at the top right corner.
- In the **Connection instructions** page, we're configuring how Maven will connect to your CodeArtifact repository.
- For **Operating system**, select **Mac and Linux**.

Even if you're doing this project on a Windows computer, don't forget that the EC2 instance we're using was launched with **Amazon Linux 2023** as its AMI! `mvn` is short for Maven, which is the tool we installed to manage the building process for our Java web app. This also makes Maven our package manager, i.e. the tool that helps us install, update and manage the external packages our web app uses.

- For **Package manager client**, select **mvn** (Maven).
- Double check that you're using **Mac and Linux** as the operating system. Even if you're doing this project on a Windows computer, Mac and Linux is the right choice - your **EC2 instance** is an **Amazon Linux 2023** instance!
- Make sure that **Configuration method** is set to **Pull** from your repository.
- Nice! The menu will now show you the steps and commands needed to connect Maven to your CodeArtifact repository.

Export CodeArtifact authorization token
- In the **Connection instructions** dialog, find **Step 3: Export a CodeArtifact authorization token....**
- Copy the entire command in **Step 3**.

![image alt](DevOps-30)

**"Export a CodeArtifact authorization token for authorization to your repository from your preferred shell"** It actually just means you need to run the command in Step 3 to give your terminal a temporary password. That password will grant your development tools (i.e. Maven) access to your repositories in CodeArtifact. Maven uses this token whenever it needs to fetch something from your CodeArtifact repository.

- Go back to your VS Code terminal, which is connected to your EC2 instance.
- Paste the copied command into the terminal and press **Enter** to run it.
- Uhhh... looks like we got an error!

![image alt](DevOps-31)

That `Unable to locate credentials` error is actually a good security feature in action! Your EC2 instance is essentially saying, "I don't know who you are, so I can't let you access CodeArtifact." This happens because, by default, your EC2 instance **doesn't** have permission to access your other AWS services (including CodeArtifact). This is intentional - AWS follows the "principle of least privilege," meaning resources only get the **minimum** permissions they need to function.

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

The `"Resource": "*"` means these permissions apply to all relevant resources, while the Condition narrows the second permission to only work with CodeArtifact. This follows the "principle of least privilege" - granting only the minimum permissions needed to perform the required tasks, enhancing your security posture.

- After pasting the JSON policy document, click the **Next** button at the bottom right.
- On the **Review policy** page, in the **Policy name** field, enter `codeartifact-nextwork-consumer-policy`.
- In the **Description - optional** field, add a description like:
`Provides permissions to read from CodeArtifact. Created as a part of NextWork CICD Pipeline series`.
- Review the **Summary** of your policy to ensure the permissions and details are correct.

![image alt](DevOps-32)

- Click the **Create policy** button to create the IAM policy.
- After clicking **Create policy**, you should see a success message at the top of the IAM Policies page, telling us that the policy `codeartifact-nextwork-consumer-policy` has been successfully created.

Now that we've created the IAM policy for CodeArtifact access, let's attach it to an IAM role and then associate that role with our EC2 instance. This will grant our EC2 instance the permissions it needs to securely access CodeArtifact. Finally, we'll verify the connection to CodeArtifact from our EC2 instance. This is important because attaching the IAM role to our EC2 instance is what actually grants the instance the permissions defined in the policy, enabling secure access to CodeArtifact.

Create a new IAM role for EC2
- In the IAM console, in the left-hand menu, click on **Roles**.
- Click the **Create role** button to start creating a new IAM role.
- For **Select entity type**, choose **AWS service**.
- Under **Choose a use case**, select **EC2** from the list of services.
- Click **Next** to proceed to the **Add permissions** step.
- In the **Add permissions** step, in the **Filter policies** search box, type `codeartifact-nextwork-consumer-policy`.
- Select the checkbox next to the `codeartifact-nextwork-consumer-policy` that you created in the previous step.
- Click **Next** to head to the **Name, review, and create** step.
- In the **Name, review, and create** step:
  - In the **Role name** field, enter `EC2-instance-nextwork-cicd`.
  - In the **Description - optional** field, enter:
`Allows EC2 instances to access services related to the NextWork CI/CD pipeline series.`
- Next, in the review page, click the **Create role** button to create the IAM role.
- After clicking **Create role**, you should see a success message at the top of the IAM Roles page, telling us that the IAM role `EC2-instance-nextwork-cicd` has been successfully created. Your new role will be listed in the roles table.

Attach IAM role to EC2
- Now, we need to associate this IAM role with your EC2 instance.
- Head back to the `EC2` console.
- Head to the `Instances` tab.
- Select your running EC2 instance (`nextwork-devops-yourname`).
- Click on **Actions** in the menu bar, then select **Security**, and then **Modify IAM role**.
- In the **Modify IAM role** dialog box, select **Refresh** to load the IAM roles available for your EC2 instance.
- Under **IAM role**, select the IAM role you just created, `EC2-instance-nextwork-cicd`, from the dropdown menu.
- Select **Update IAM role** to attach the role to your EC2 instance.
- After attaching the IAM role, you should see a green banner at the top of the EC2 Instances dashboard confirming that the IAM role was successfully modified for your instance.

Now that your EC2 instance has the necessary IAM role attached, let's re-run the command to export the CodeArtifact authorization token.
- Head back to your VS Code terminal connected to your EC2 instance.
- Re-run the same export token command from Step 3.
- This command will retrieve a temporary authorization token for CodeArtifact and store it in an environment variable named `CODEARTIFACT_AUTH_TOKEN`.

![image alt](DevOps-33)

**Step -3 : Verify CodeArtifact Connection and See Packages in CodeArtifact**

Let's make sure everything is set up correctly by verifying the connection to our CodeArtifact repository from our EC2 instance. We'll configure Maven to use CodeArtifact and then try to compile our web app, which should now download dependencies from CodeArtifact. This verification is crucial to ensure that our EC2 instance can successfully access and retrieve packages from CodeArtifact, which is a key part of our CI/CD pipeline setup.

- You might notice that we still have a few steps left in CodeArtifact's connection settings panel!
- We'll use the code in each step in a minute, but we'll have to set up a special file called **settings.xml** first.
- In VS Code, in your left hand **file explorer**, head to the root directory of your `nextwork-web-project`.
- Create a new file at the root of your `nextwork-web-project` directory.
- Name the new file `settings.xml`.

**settings.xml** is like a settings page for Maven - it stores all the settings we saw in Steps 4-6 of the connection window. It tells Maven how to behave across all your projects. In our case, we need a settings.xml file to tell Maven where to find the dependencies and how to get access to the right repositories (e.g. the ones in CodeArtifact).

- Open the `settings.xml` file. If you created a new file, it will be empty.
- In your `settings.xml` file, add the `<settings>` root tag if it's not already there:

```xml
<settings>
</settings>
```

![image alt](DevOps-34)

- Go back to the CodeArtifact connection settings panel.
- From the **Connection instructions** dialog, copy the XML code snippet from **Step 4: Add your server to the list of servers in your settings.xml**.

![image alt](DevOps-35)

The **servers** section is where your store your access details to the repositories you're connecting with your web app project. In this example, you've added your authentication token to access your local CodeArtifact repository.

- Paste the code in the **settings.xml** file, in between the `<settings>` tags.
- Let's copy the XML code snippet from **Step 5: Add a profile containing your repository to your settings.xml.**

![image alt](DevOps-36)

The **profiles** section is where you write a rulebook on when Maven should use which repository. We only have one package repository in this project, so our profiles section is more straightforward than other projects that might be pulling from multiple repositories! Our profiles section is telling Maven to go to the nextwork-packages repository to find the tools / packages needed to build your Java web app.

- Paste the code snippet you copied right underneath the `<servers>` tags. Make sure the `<profiles>` tags are also nested inside the `<settings>` tags.
- Finally, paste the XML code snippet from **Step 6: (Optional) Set a mirror in your settings.xml...** right underneath the `<profiles>` tags.

The **mirrors** section sets up backup locations that Maven can check if it can't find what it needs in the first local repository it goes to. The backup location that we'll set by default is... our CodeArtifact repository again. This means that for any repository requests (denoted by the asterisk * in the * line), Maven will redirect those requests to the same CodeArtifact repository since it's our only local repository. It might seem unnecessary now, but mirrors are great in complex scenarios and is a great fallback option to set up from the start!

![image alt](DevOps-37)

Save the `settings.xml` file.

Compile your project and verify the CodeArtifact integration
-  In your VS Code terminal, run `pwd` to check that you're in the root directory of your `nextwork-web-project`
-  If you're not at the root directory, run `cd nextwork-web-project` to get there!
-  Next, we'll **compile** your project.
-  Run the Maven compile command, which uses the `settings.xml` file we just configured:

```bash
mvn -s settings.xml complie
```

-  Press **Enter** to execute the command.
-  As Maven compiles your project, observe the terminal output.
-  You should see messages like `Downloading from nextwork-devops-cicd` telling us that Maven is downloading dependencies from your CodeArtifact repository. This is a good sign that Maven is using CodeArtifact to manage dependencies!
-  If the compilation is successful and dependencies are downloaded from CodeArtifact, you'll see a `BUILD SUCCESS` message at the end of the Maven output.

![image alt](DevOps-38)

When you run `mvn -s settings.xml compile`, Maven first looks at your project's dependencies in the `pom.xml` file. Then, instead of downloading them directly from public repositories, it checks your CodeArtifact repository. If the dependency isn't already in CodeArtifact, it will fetch it from the upstream repository (Maven Central in our case), cache it in CodeArtifact, and then deliver it to your project. This process happens for each required dependency, ensuring that your build process is secure, controlled, and faster for subsequent builds when dependencies are already cached in CodeArtifact.

-  Let's head back to the CodeArtifact console in your browser.
-  Close the connection instructions window.
-  If you don't see any packages in your repository listed yet, click the **refresh** button in the top right corner of the Packages pane.
-  After refreshing, you should now see a list of Maven packages in your CodeArtifact repository.

![image alt](DevOps-39)

Those packages appearing in your CodeArtifact repository are proof that the entire system is working! Here's what happened behind the scenes: when you ran the Maven compile command, Maven checked your project's pom.xml file and determined which dependencies your application needs. It then requested these dependencies through CodeArtifact. Since this was the first time these dependencies were requested, CodeArtifact didn't have them yet. So it reached out to Maven Central (the upstream repository we configured), downloaded the packages, stored copies in your repository, and then provided them to Maven. Now that these packages are stored in your CodeArtifact repository, anyone else in your organization who needs the same dependencies will get them directly from your repository instead of from Maven Central. This gives you faster builds, more reliability, and the ability to control exactly which package versions your organization uses.

---

##  Continuous Integration with CodeBuild

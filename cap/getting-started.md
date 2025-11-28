---
title: Getting Started
layout: home
---

# Objectives
- Initialize a CAP Java project in your IDE (e.g. IntelliJ IDEA)
- Run the application from the command line using the spring-boot maven plugin

# Wait, but why?
CAP Java needs to initialize some files beforehand so that you can start developing. The importing process of those files is automated so that you don't have to do anything but run one command.

# Exercise
## 1 - Create a repository
Create a [new GitHub repository](https://github.com/new). For the name, you can pick `bulletinboard-ads-cap`. We will reference this repository name in the tutorial.

## 2 - Clone and initialize the Maven Project in your IDE
1. Clone the repository in your terminal (insert the git URL to your fork):

```git clone https://github.com/<YOUR USERNAME>/bulletinboard-ads-cap bulletinboard-ads-cap```
2. Import the project into your IDE: 
=== "IntelliJ" 1. From the main menu, choose `File > Open...`. 2. Navigate to the folder where you checked out the project (**bulletinboard-ads-cap**) and click `OK`.
TODO: import for other tools (vscode)
3. Use the CAP Java Maven archetype to bootstrap a new CAP Java project:

```mvn archetype:generate -DarchetypeArtifactId="cds-services-archetype" -DarchetypeGroupId="com.sap.cds" -DarchetypeVersion="RELEASE" -DinteractiveMode=true```

When prompted, specify the group ID (**com.sap.cc**) and artifact ID (**bulletinboard**) of your application. The artifact ID also specifies the name of your projects root folder that is generated in your current working directory. For other values prompted, it's enough to simply confirm the default values.

## 3 - Start your application
=== "Command Line"
- Run the application using the following command:

```mvn spring-boot:run```

## 4 - Set up Bruno
At the end of each exercise we instruct you to di some smoke testing. We recommend you use Bruno to perform these smoke tests.
1. Download and install Bruno [here](https://www.usebruno.com/downloads).
If you haven't worked with Bruno before, don't worry. The tool should be pretty straightforward, as it is very similar to Postman and Insomnia.

// TODO add tests

# Summary
Good job!

You have learned:
- [ ] How to check out a project from git
- [ ] How to initialize a CAP Java project and import it in your IDE
- [ ] How to start a CAP Java project using the command line

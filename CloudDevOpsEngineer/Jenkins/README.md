[Jenkins](https://jenkins.io/doc/book/pipeline/)

# Command for Installing Jenkins on Ubuntu



```bash
# Step 1 - Update existing packages
sudo apt-get update

# Step 2 - Install Java
sudo apt install -y default-jdk

# Step 3 - Download Jenkins package. 
# You can go to http://pkg.jenkins.io/debian/ to see the available commands
# First, add a key to your system
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -

# # Step 4 - Add the following entry in your /etc/apt/sources.list:
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'

# # Step 5 -Update your local package index
sudo apt-get update

# Step 6 - Install Jenkins
sudo apt-get install -y jenkins

# Step 7 - Start the Jenkins server
sudo systemctl start jenkins

# Step 8 - Enable the service to load during boot
sudo systemctl enable jenkins
sudo systemctl status jenkins
```





## What is DevOps?

DevOps is the combination of industry best practices, and set of tools that improve an organization’s ability to: *Increase the speed of software delivery

- Increases the speed of software evolution
- Have better reliability of the software
- Have scalability using automation,
- Improved collaboration among teams. The two most important practices are - **Continuous Integration / Continuous Delivery or Deployment (CI/CD)** and Infrastructure as Code (IaaC).

## What is CI/CD?

CI/CD is a consistent and automated way for a DevOps team to build, package, test, and deploy applications

- **Continuous Integration** means newly developed code changes of a project are periodically built, tested, and integrated into a shared repository like Git. Then, the integrated code is verified and tested using automated tools.
- **Continuous Delivery** is the process of automating the release of the merged and validated code to a repository and finally release a production-ready build to the production environment.



## Pipelines Overview

**A Pipeline is a set of tools and processes to automate the CI/CD.**

- A pipeline contains steps that have different actions performed as part of those steps.

- Written in Groovy code and designed to be reusable.

- Used similarly to how programming languages use “Modules” in a pluggable manner.

- Best Practice 
  - Push config jenkins file to Git.
  - Version,review,pull request and integrating it.

- The value in a Pipeline is 
  - Enables advanced functionality beyond simple Bash scripting
  - Has programmatic control such as try and catch for Error Handling.

`Try/catch block` allows program to respond to errors in code or data for Jenkins functions. Pipelines assist with performing code testing and verification due to their modular nature and try/catch routines.

- Enables advanced error handling which lends itself to complex functionality.



[![CI/CD Pipeline](https://video.udacity-data.com/topher/2020/March/5e727135_images-1/images-1.png)A simple view of CI/CD Pipeline](https://classroom.udacity.com/nanodegrees/nd9991-ent/parts/131268b4-c19b-4f6f-9b02-ee8f5cb6c278/modules/a7fbb2e9-018f-4ad9-b650-dea521df4db8/lessons/e0267414-3b15-4fd5-8993-96f4296bd3d6/concepts/5633f33a-e983-45d5-8182-89ca814a6c0e#)



-  CI/CD pipeline implementation is the backbone of the modern DevOps environment. 
- With pipelines we are able to use code checked into a Git repository to control the execution, linting, security testing, and performance testing of code. 
- We use different environments to perform different functions, such as 
  -  Development environment for building the code,
  - Staging environment for testing
  - Production environment for deployment
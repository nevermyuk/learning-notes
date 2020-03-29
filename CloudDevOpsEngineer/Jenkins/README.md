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
- Improved collaboration among teams. The two most important practices are - **Continuous Integration / Continuous Delivery or Deployment (CI/CD)** and Infrastructure as Code (IaC).

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

## Pipeline Testing



### Key ideas

- **Linting** - It is the process of running a program that checks the Pipeline code for potential syntax errors.
- **Security Testing** - Performed with a variety of software to test for Common Vulnerabilities and Exposures (CVE). There are a variety of [security testing plugins available for Jenkins](https://plugins.jenkins.io/ui/search?sort=relevance&categories=buildManagement&labels=&view=Tiles&page=1&query=security testing). 
  - Aqua - a  Jenkins plugin designed for testing Docker containers. For testing against CVE (Common Vulnerabilities and Exposures).
- **Performance Testing** - By setting up a smaller scale environment as compared to *production*, with Jenkins and running simulated host calls into that environment to determine how the new environment performs under a particular workload. It is a two-stage process -
  - **Stage 1 - Run Apache JMeter** - JMeter is a testing tool used for estimating the performance of the newly created Jenkins environment. [JMeter Tutorial](https://wiki.jenkins.io/display/JENKINS/How+to+run+JMeter+with+Jenkins)
  - **Stage 2 - Capture Reports** - Jenkins has [Performance plugin](https://plugins.jenkins.io/performance/) to capture reports from popular testing tools, such as JMeter, Selenium, and many others in the XML and CSV format. 
- **Integration Testing** - It is testing the Pipeline code from different modules to make sure it all works together. It occurs after unit testing. Jenkins provides [JUnit plugin](https://plugins.jenkins.io/junit/) as *unit-testing framework to write repeatable tests*. Refer [here](https://jenkins.io/doc/pipeline/steps/junit/) for reading the functionality of JUnit plugin using **steps**.




# CI/CD Demo - Café Supremo Microservices

This repository contains the demo script to demonstrate a typical DevOps workflow of a cloud native application using Oracle AppDev Platform. This includes services such as Oracle Developer Cloud Service, Java Cloud Service, Application Container Cloud Service and Database Cloud Service.

This demo will also show how to work with Open Source tools in Oracle Developer Cloud Service for managing your software lifecycle as well as how to build, deploy and promote to different environments. With Continuous Integration and Delivery as core principles, this demo will illustrate how issues are tracked, managed and how to manage releases.

This repository includes the infrastructure and pipeline definition for continuous delivery using Jenkins, Nexus and SonarQube on OpenShift. On every pipeline execution, the code goes through the following steps:


This demo showcases the following:

*	Continuous Integration and Continuous Delivery through automation in Oracle Developer Cloud Service
*	Task creation using Oracle Developer Cloud Service Issue tracking system
*	Working with Open Source tools
*	Pushing the changes from a remote repository
*	Scaling up and scaling out of ACCS and JCS



# Time to Complete

Approximately 30 minutes



# Background

Oracle Developer Cloud Service is a cloud-based hosting environment for software development projects. It is available as a web interface accessible from a web browser. Oracle Developer Cloud Service provides the following features and services:

*	Project creation, configuration, and user management
*	Agile development
*	Integrated issue tracking for tasks, bugs, and enhancements
*	Source code repository through Git to store your application source
*	Maven repositories to store your application dependencies and libraries
*	Code Review enabled with Team Collaboration
*	Continuous software build integration
*	Wiki collaboration
*	Deployment to an Oracle Java Cloud Service and Application Container Cloud Service



# Scenario

You are an application developer who will be developing a brand new cloud native loyalty application for Café Supremo, which will be deployed to the Oracle Cloud. The reason why you want to adopt a cloud native approach has partially been driven by the need to go to market quicker, by delivering new features more frequently, but also more reliably. And you are able to do this by developing Microservices that has less dependencies on other services, as well as the footprint being smaller and easier to deploy. What’s also attractive with Microservices is that you can use the best programming language for the job. So, you could end up with a polyglot application. To be able to deliver this new style of cloud native application approach, you will need to adopt the Agile development practice to continuously integrate and deliver these services and features. The application basically consists of two parts:

1. The user interface that is built using Oracle JET framework, packaged as a WAR file and deployed to a Java Cloud Service instance
2. The Reward Collection backend service, which is a Microservice written in node.js, packaged as a ZIP file and deployed to an Application Container Cloud Service instance



![](images/architecture.png)




You will use DevCS as the core CI/CD software lifecycle management tool for your Agile development and DevOps pipeline. The Café Supremo project has already been created and populated with both the JET UI and the Reward Service. The latest JET UI is also deployed to a previously provisioned JCS instance and the Reward Collection is deployed to a previously provisioned ACCS instance. The customer data has also been uploaded to a DBCS instance.
This demo assumes that you will be logging into an Oracle Developer Cloud Service instance that is populated with the Café Supremo project. This demo also requires a desktop environment with Brackets installed.

A project manager will be logging in to an Oracle Developer Cloud Service and manage the Café Supremo project from issue tracking to tracking the build, deploy and release progress.



![](images/demoflow.png)



# What Do You Need?

*	Access to an Oracle Developer Cloud Service instance
*	Access to an Oracle Application Cloud Service instance
*	Access to an Oracle Java Cloud Service instance
*	Access to a mobile phone
*	One of the following supported browsers:
    *	Firefox 31 or above
    *	Chrome 37 (also for Android)
    *	Safari 7, 8 or 9
    *	Safari Mobile 5 (iOS)
*	An installation of Brackets with Bracket Git by Martin Zagora extension installed
*	An installation to mirror your mobile screen to the projector
*	Some familiarity with Oracle Java Cloud Service
*	Some familiarity with Oracle Application Cloud Service
*	Some familiarity with Brackets
*	Some familiarity with the Git source control system
*	Some familiarity with Hudson Continuous Integration system




# Iteration 1:   Introducing the Café Supremo Loyalty Application and Oracle Cloud

You are an application developer who will be developing a brand new cloud native loyalty application for Café Supremo, which will be deployed to the Oracle Cloud.



# Step 1

Introduce the application. The application basically consists of two parts:

* The user interface that is built using Oracle JET framework, packaged as a WAR file and deployed to a Java Cloud Service instance
* The Reward Service is a Microservice written in node.js, packaged as a ZIP file and deployed to an Application Container Cloud Service instance

The JET UI and the Reward Service are being developed in parallel independent of each other, by two different teams.



![](images/cafehome.png)




* Mirror your mobile phone
* Enter Café Supremo URL - http://xxx.xxx.xxx.xxx/cafesupremo
* Click on the Menu icon



# Step 2


* Show how this JET UI looks like by going into the Discover and Stores options
* Highlight that the Rewards Service option has not been completed and hence it is greyed out
* The Reward Service is being implemented as a Microservice in Node.js
* Since the two services are implemented separately, using different languages and tools, there is very little integration and collaboration between the two teams
* Code are stored in separate Git repositories and they are built differently, but manually.
* We will show how you can improve your team’s productivity and quickly rollout new services by adopting CI/CD in Oracle AppDev Platform



![](images/cafemenu.png)
![](images/cafemap.png)



* Click on each option to show what they do






The application used in this pipeline is a JAX-RS application which is available on GitHub and is imported into Gogs during the setup process:
[https://github.com/OpenShiftDemos/openshift-tasks](https://github.com/OpenShiftDemos/openshift-tasks/tree/eap-7)

# Prerequisites

* 8+ GB memory available on OpenShift nodes
* JBoss EAP 7 imagestreams imported to OpenShift (see Troubleshooting section for details)

# Setup

Follow these [instructions](docs/oc-cluster.md) in order to create a local OpenShift cluster. Otherwise using your current OpenShift cluster, create the following projects for CI/CD components, Dev and Stage environments:

  ```
  oc new-project dev --display-name="Tasks - Dev"
  oc new-project stage --display-name="Tasks - Stage"
  oc new-project cicd --display-name="CI/CD"
  ```

Jenkins needs to access OpenShift API to discover slave images as well accessing container images. Grant Jenkins service account enough privileges to invoke OpenShift API for the created projects:

  ```
  oc policy add-role-to-user edit system:serviceaccount:cicd:default -n dev
  oc policy add-role-to-user edit system:serviceaccount:cicd:default -n stage
  ```
Create the CI/CD components based on the provided template

  ```
  oc process -f cicd-template.yaml | oc create -f -
  ```

To use custom project names, change `cicd`, `dev` and `stage` in the above commands to
your own names and use the following to create the demo:
```
oc process -f cicd-template.yaml -v DEV_PROJECT=dev-project-name -v STAGE_PROJECT=stage-project-name | oc create -f - -n cicd-project-name
```

__Note:__ you need ~8GB memory for running this demo.

# Guide

1. A Jenkins pipeline is pre-configured which clones Tasks application source code from Gogs (running on OpenShift), builds, deploys and promotes the result through the deployment pipeline. In the CI/CD project, click on _Builds_ and then _Pipelines_ to see the list of defined pipelines. If the [tech preview pipeline feature](https://docs.openshift.com/container-platform/3.3/install_config/web_console_customization.html#web-console-enable-tech-preview-feature) is not enabled on your OpenShift cluster, go directly to the pipelines section with this url: https://OPENSHIFT.MASTER/console/project/cicd/browse/pipelines

  Click on _tasks-pipeline_ and _Configuration_ and explore the pipeline definition.

  You can also explore the pipeline job in Jenkins by clicking on the Jenkins route url, logging in with the following credentials, clicking on _tasks-pipeline_ and _Configure_.

  __Jenkins credentials:__ _admin/password_  
  __Gogs credentials:__ _gogs/password_

2. Run an instance of the pipeline by starting the _tasks-pipeline_ in OpenShift or Jenkins.

3. During pipeline execution, verify a new Jenkins slave pod is created within _CI/CD_ project to execute the pipeline.

4. Pipelines pauses at _Deploy STAGE_ for approval in order to promote the build to the STAGE environment. Click on this step on the pipeline and then _Promote_.

5. After pipeline completion, demonstrate the following:
  * Explore the _snapshots_ repository in Nexus and verify _openshift-tasks_ is pushed to the repository
  * Explore SonarQube and verify a project is created with metrics, stats, code coverage, etc
  * Explore _Tasks - Dev_ project in OpenShift console and verify the application is deployed in the DEV environment
  * Explore _Tasks - Stage_ project in OpenShift console and verify the application is deployed in the STAGE environment  


6. Clone and checkout the _eap-7_ branch of the _openshift-tasks_ git repository and using an IDE (e.g. JBoss Developer Studio), remove the ```@Ignore``` annotation from ```src/test/java/org/jboss/as/quickstarts/tasksrs/service/UserResourceTest.java``` test methods to enable the unit tests. Commit and push to the git repo.

7. Check out Jenkins, a pipeline instance is created and is being executed. The pipeline will fail during unit tests due to the enabled unit test.

8. Check out the failed unit and test ```src/test/java/org/jboss/as/quickstarts/tasksrs/service/UserResourceTest.java``` and run it in the IDE.

9. Fix the test by modifying ```src/main/java/org/jboss/as/quickstarts/tasksrs/service/UserResource.java``` and uncommenting the sort function in _getUsers_ method.

10. Run the unit test in the IDE. The unit test runs green. Commit and push the fix to the git repository and verify a pipeline instance is created in Jenkins and executes successfully.

![](images/jenkins-pipeline.png)

# Enabling OpenShift 3.3 Pipelines with "oc cluster"
Pipeline in OpenShift 3.3 is a tech preview feature and disabled by default. The steps required to enable this feature is detailed in [OpenShift documentation](https://access.redhat.com/documentation/en/openshift-container-platform/3.3/paged/installation-and-configuration/chapter-29-customizing-the-web-console#web-console-enable-tech-preview-feature).

# Troubleshoot

* SonarQube sometimes fails to load quality profiles requires for static analysis.
  ```
  [ERROR] Failed to execute goal org.sonarsource.scanner.maven:sonar-maven-plugin:3.0.1:sonar (default-cli) on project jboss-tasks-rs: No quality profiles have been found, you probably don't have any
  ```

  Scale down the SonarQube pod and its PostgreSQL database to 0 and then scale them up to 1 again (first PostgreSQL, then SonarQube) to re-initialize SonarQube.

* Downloading the images might take a while depending on the network. Remove the _install-gogs_ pod and re-create the app to retry Gogs initialization.

  ```
  $ oc delete pod install-gogs
  $ oc delete pods -l app=gogs
  $ oc process -f cicd-template.yaml | oc create -f -

  pod "install-gogs" created
  Error from server: routes "jenkins" already exists
  Error from server: deploymentconfigs "jenkins" already exists
  Error from server: serviceaccounts "jenkins" already exists
  Error from server: rolebinding "jenkins_edit" already exists
  ...
  ```

* If the cicd-pipeline Jenkins job has disappeared, scale Jenkins pod to 0 and up to 1 again to force a job sync with OpenShift pipelines.

* If pipeline execution fails with ```error: no match for "jboss-eap70-openshift"```, import the jboss imagestreams in OpenShift.

  ```
  oc login -u system:admin
  oc create -f https://raw.githubusercontent.com/jboss-openshift/application-templates/master/jboss-image-streams.json -n openshift
  ```

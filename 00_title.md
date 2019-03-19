<!-- markdownlint-disable MD012 MD014 -->

# AWS CodePipeline



## Continuous Integration

* Source Code Repository
* Continuous Integration server
  * For each service a separate build project
    * Two stages: Verify and Package
* Artifact repository


## Continuous Deployment

* Tools like Chef
* Missing orchestration layer
  * [Canary](https://martinfowler.com/bliki/CanaryRelease.html)
  * [Blue/ Green](https://martinfowler.com/bliki/BlueGreenDeployment.html)
  * Rollback



## CodePipeline

Marketing (Part 1)
> ... fully managed continuous delivery service that helps you automate your release pipelines ...


Marketing (Part 2)
> ... automates the build, test, and deploy phases of your release process every time there is a code change, based on the release model you define.


### Components and our task

|  AWS Universe     |
| :-------------:|
|  CodeCommit    |
|  CodeBuild     |
|  S3            |
| CodeDeploy    |
| CodePipeline  |

__Task: Setup a CI/CD pipeline for a [NodeJS application](https://docs.aws.amazon.com/de_de/elasticbeanstalk/latest/dg/samples/nodejs-v1.zip)__



* Login to the [AWS console](https://aws.amazon.com/de/console/)



### Prepare the Elasitc Beanstalk application

* Click on _Create New Application_
* Application name is _NodeJsDemo_
* Click _Action_, _Create environment_
* Select _Web server environment_


* Update environment name
* Find a free domain name
* Select Node.js as platform
* Select _Configure more options_
* Select _High Availability_ from configuration presets
* Click _Create environment_

Open browser at provided URL



### Creating a repository

* Go to _CodeCommit_ service
* Click _Create Repository_
* Provide repository name and (optional) description

~~~~sh
Name: nodejsDemo
Description: Repository storing the code for the NodeJs Demo
~~~~

* Click _Create_


* Click on _Clone URL_
* Click _Clone HTTPS_ to get the endpoint

~~~~sh
https://git-codecommit.eu-west-1.amazonaws.com/v1/repos/nodejsDemo
~~~~


* Switch to _IAM_ service and create a user
* Setup _HTTPS Git credentials for AWS CodeCommit_
* Save username and password.
  
  Both are required when interacting with repository
* Push code to repository



### Creating a CodePipeline

* Go to _CodePipeline_ service
* Click _Create Pipeline_
* Enter the name of the new pipeline
* Select _AWS CodeCommit_ as source provider
* Select repository and branch  


* Add a build stage
* Select _AWS CodeBuild_ as build provider
* Click _Create Project_
  

* Specify project name (_NodeJsDemoVerify_ for first phase)
* Select Operating System, Runtime and Runtime version

~~~~sh
OS: Ubuntu
Runtime: Node.js
Runtime version: Node.js 8.11.0-1.6.0
~~~~



## Code Deploy

* Select _AWS Elastic Beanstalk_
* Select application name _NodeJsDemo_
* Select environment _NodeJsDemo-env_

* Watch the CodePipeline build for the first time


What happened?

~~~~sh
[Container] 2019/03/15 20:04:15 Waiting for agent ping
[Container] 2019/03/15 20:04:18 Waiting for DOWNLOAD_SOURCE
[Container] 2019/03/15 20:04:18 Phase is DOWNLOAD_SOURCE
[Container] 2019/03/15 20:04:18 CODEBUILD_SRC_DIR=/codebuild/output/src416057865/src
[Container] 2019/03/15 20:04:18 Phase complete: DOWNLOAD_SOURCE Success: false
[Container] 2019/03/15 20:04:18 Phase context status code: YAML_FILE_ERROR Message: YAML file does not exist
~~~~

The  missing YAML file is a [build specification](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)


* Add a buildspec.yml to the git repository and commit

~~~~yaml
version: 0.2
phases:
  install:
    commands:
      - echo Entered the install phase...
  pre_build:
    commands:
      - echo Entered the pre_build phase...
    finally:
      - echo This always runs even if the login command fails
  build:
    commands:
      - echo Entered the build phase...
      - echo Build started on `date`
  post_build:
    commands:
      - echo Entered the post_build phase...
      - echo Build completed on `date`
~~~~

* Release change



## Further information

* [CodePipeline](https://aws.amazon.com/codepipeline/)
* [CodeCommit](https://aws.amazon.com/de/codecommit/)
* [CodeBuild](https://aws.amazon.com/de/codebuild/)
* [CodeDeploy](https://aws.amazon.com/de/codedeploy/)
* [ElasticBeanstalk](https://aws.amazon.com/de/elasticbeanstalk/)
* [NodeJS Demo](https://docs.aws.amazon.com/de_de/elasticbeanstalk/latest/dg/RelatedResources.html)


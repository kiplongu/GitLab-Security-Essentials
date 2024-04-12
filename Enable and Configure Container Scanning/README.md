GitLab Security Essentials - Hands-On Lab: Enable and Configure Container Scanning
This Hands-On Guide walks you through the lab exercises used in the GitLab Security Essentials course.
You are viewing the latest Version 16.x instructions. If your group URL starts with https://spt.gitlabtraining.cloud, please use the Version 15.x instructions

Estimated time to complete: 15 to 20 minutes

Objectives
Many projects depend on using containers that might contain vulnerabilities in the container image.

In this lab, you will learn how to scan for vulnerabilities in your containers.

Prerequisites
Open your browser to to the Security Labs project that you created in Lab 1.

If you closed the tab or lost the link, open a browser tab and start typing https://gitlab.com/gitlab-learn-labs in your URL and the group should appear in your history.

Before beginning this lab and all later labs, you should disable any scanners that you enabled in previous labs to speed up pipeline runtime. You should have already completed this in Lab 2.



# Task A. Add a Dockerfile
A Dockerfile is a “recipe” that tells Docker how to assemble your application into a Docker image. You’ll write a Dockerfile that installs your single-file Python application onto an Python 3.4 Docker image, and then packages that whole stack into a new Docker image.

Navigate to Code > Repository.

Above the repository file list, click (+) > This directory > New file.

In the File name field, type Dockerfile.

It is important that the first letter is capitalized for industry standards.

The Dockerfile must specify which Linux image to install your application on. For this lab you’ll use an old version of Python that has security vulnerabilities for the Container Scanner to find. Paste this into Dockerfile:

FROM python:3.4-alpine
The Dockerfile must add your application to the Linux image specified above. Paste this at the bottom of Dockerfile:

ADD main.py .
Your completed Dockerfile should look like this. Make any corrections necessary.

FROM python:3.4-alpine
ADD main.py .
Add a commit message, set the target branch to main and click Commit changes.



# Task B. Build the Docker image
In this section you will define a job that builds a Docker image. To build a Docker image with a CI/CD pipeline job, you must use a GitLab Runner that’s configured to use a Docker executor.

Navigate to Code > Repository and edit .gitlab-ci.yml.

Define a build stage by pasting this in your .gitlab-ci.yml, at the top of the stages list, before the -test stage. Make sure it has the same indentation as the existing - test entry beneath it:

stages:
- build
- test
Name your new job and assign it to the build stage by pasting this at the end of .gitlab-ci.yml:

build-and-push-docker-image:
  stage: build
Your job must run on a Docker image that contains Docker tools. This approach is sometimes called “Docker in Docker” or “dind”. You’ll need to specify a version of the image that we’ve tested and know to work well for this task. Paste this underneath the build-and-push-docker-image job that you added in the previous step:

build-and-push-docker-image:
  stage: build
  image: docker:20.10.17
Your job also needs a second Docker image that enables the Docker in Docker workflow. Specify the second image with the services keyword, by pasting this into your job definition:

build-and-push-docker-image:
  stage: build
  image: docker:20.10.17
  services:
    - docker:20.10.17-dind
It’s helpful to define a variable to hold the full name and version of the Docker image you’re creating, because you’ll need to refer to that information more than once. You can assemble the name and version out of predefined variables that GitLab provides (remember that predefined variables generally start with CI_). Paste this into your job definition:

build-and-push-docker-image:
  stage: build
  image: docker:20.10.17
  services:
    - docker:20.10.17-dind
  variables:
    IMAGE: $CI_REGISTRY_IMAGE/$CI_COMMIT_REF_SLUG:$CI_COMMIT_SHA
If you set a variable telling Docker not to use TLS, you won’t have to worry about setting up security certificates. Add the DOCKER_TLS_CERTDIR variable.

build-and-push-docker-image:
  stage: build
  image: docker:20.10.17
  services:
    - docker:20.10.17-dind
  variables:
    IMAGE: $CI_REGISTRY_IMAGE/$CI_COMMIT_REF_SLUG:$CI_COMMIT_SHA
    DOCKER_TLS_CERTDIR: ""
Tell Docker to build a Docker image using the recipe in Dockerfile. Add the script: and docker-build lines.

build-and-push-docker-image:
  stage: build
  image: docker:20.10.17
  services:
    - docker:20.10.17-dind
  variables:
    IMAGE: $CI_REGISTRY_IMAGE/$CI_COMMIT_REF_SLUG:$CI_COMMIT_SHA
    DOCKER_TLS_CERTDIR: ""
  script:
    - docker build --tag $IMAGE .


# Task C. Push the Docker image to Project Container Registry
Your job needs to log in to the project’s container registry so it can push your image to it. You can log in using a username, password, and registry URL that are stored in predefined variables.

Add the docker login line to the bottom of the script section.

build-and-push-docker-image:
  stage: build
  image: docker:20.10.17
  services:
    - docker:20.10.17-dind
  variables:
    IMAGE: $CI_REGISTRY_IMAGE/$CI_COMMIT_REF_SLUG:$CI_COMMIT_SHA
    DOCKER_TLS_CERTDIR: ""
  script:
    - docker build --tag $IMAGE .
    - docker login --username $CI_REGISTRY_USER --password $CI_REGISTRY_PASSWORD $CI_REGISTRY
Your job can push the image with a single Docker command. Add the docker push line to the bottom of the script section.

build-and-push-docker-image:
  stage: build
  image: docker:20.10.17
  services:
    - docker:20.10.17-dind
  variables:
    IMAGE: $CI_REGISTRY_IMAGE/$CI_COMMIT_REF_SLUG:$CI_COMMIT_SHA
    DOCKER_TLS_CERTDIR: ""
  script:
    - docker build --tag $IMAGE .
    - docker login --username $CI_REGISTRY_USER --password $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker push $IMAGE
Your completed job definition should look like this. Make any corrections necessary to the job definition in your .gitlab-ci.yml.

build-and-push-docker-image:
  stage: build
  image: docker:20.10.17
  services:
    - docker:20.10.17-dind
  variables:
    IMAGE: $CI_REGISTRY_IMAGE/$CI_COMMIT_REF_SLUG:$CI_COMMIT_SHA
    DOCKER_TLS_CERTDIR: ""
  script:
    - docker build --tag $IMAGE .
    - docker login --username $CI_REGISTRY_USER --password $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker push $IMAGE
Commit the changes to the main branch with an appropriate commit message (Adding a docker file definition).

Navigate to Build > Pipelines to watch the progress of the new pipeline. Click on the pipeline to view the CI output for the build job.

When the pipeline finishes running, go to left navigation pane and click Deploy > Container Registry. Verify that your job created a new Docker image and pushed it into the project’s container registry.



# Task D. Enable Container Scanning
Your application’s docker image may contain known vulnerabilities. In order to prevent these vulnerabilities from reaching production, you can detect them with Container Scanning. Now that your Docker image is being built and pushed, you can enable Container Scanning.

Add the Container Scanning template to the existing include: section of .gitlab-ci.yml:

include:
- template: Security/SAST.gitlab-ci.yml
- template: Security/Secret-Detection.gitlab-ci.yml
- template: Security/Container-Scanning.gitlab-ci.yml
This can be added anywhere in the list of templates.

Commit the changes with an appropriate commit message.

Navigate to Build > Pipelines to watch the progress of the new pipeline.

Open the container_scanning job to view the CI output. Wait for the pipeline to finish running.


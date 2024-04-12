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


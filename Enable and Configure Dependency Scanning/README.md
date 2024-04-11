# GitLab Security Essentials - Hands-On Lab: Enable and Configure Dependency Scanning
This Hands-On Guide walks you through the lab exercises used in the GitLab Security Essentials course.
You are viewing the latest Version 16.x instructions. If your group URL starts with https://spt.gitlabtraining.cloud, please use the Version 15.x instructions

Estimated time to complete: 15 to 30 minutes

Objectives
Many projects depend on open source software and libraries. The dependencies that a project uses can also contain security vulnerabilities.

In this lab, you will learn how to scan for vulnerabilities in your project dependencies.

Prerequisites
Before beginning this lab and all later labs, you should disable any scanners that you enabled in previous labs to speed up pipeline runtime.

Open your browser to to the Security Labs project that you created in Lab 1.

If you closed the tab or lost the link, open a browser tab and start typing https://gitlab.com/gitlab-learn-labs in your URL and the group should appear in your history.

To disable a scanner, add a hash before the template that enables it in .gitlab-ci.yml. For example, the DAST scanner takes some time to run, so, to disable the DAST scanner configured in Lab 1, make these edits to your existing .gitlab-ci.yml.

stages:
- test
#- dast

include:
- template: Security/SAST.gitlab-ci.yml
- template: Security/Secret-Detection.gitlab-ci.yml
#- template: DAST.gitlab-ci.yml

variables:
  SAST_EXCLUDED_PATHS: venv/
  #DAST_WEBSITE: https://example.com

secret_detection:
  variables:
    SECRET_DETECTION_EXCLUDED_PATHS: tests/
It is also possible to disable these features by setting CI variables on the project, such as DAST_DISABLE=true and SECRET_DETECTION_DISABLE=true. Learn more in the documentation.

It is important to leave at least one job active in your .gitlab-ci.yml file. If there are no jobs defined in the pipeline, it will fail to run.


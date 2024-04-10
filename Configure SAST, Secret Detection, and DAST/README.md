GitLab Security Essentials - Hands-On Lab: Configure SAST, Secret Detection, and DAST
This Hands-On Guide walks you through the lab exercises used in the GitLab Security Essentials course.
You are viewing the latest Version 16.x instructions. If your group URL starts with https://spt.gitlabtraining.cloud, please use the Version 15.x instructions

Estimated time to complete: 45 to 60 minutes

Objectives
In this lab you’ll enable SAST, Secret Detection, and DAST scans for a GitLab project.

After the scans run in a CI/CD pipeline, you’ll view the results of all 3 scans. You’ll mark a vulnerability for future action, and you’ll dismiss a different vulnerability.

Finally, you’ll fix a vulnerability on a branch and introduce a new vulnerability on that same branch, so you can view the differences in vulnerabilities between default and non-default branches.

Please take time to understand any code that you are asked to copy and paste in any lab. Ask your instructor to explain any code that’s not clear.

# Task A. Access your Training Group and Create a Project
Navigate to https://gitlabdemo.com/invite in a web browser.

In the Invitation Code field, enter the invitation code provided by your instructor or in the LevelUp LMS.

Click Provision Training Environment.

If the system prompts you for your GitLab.com username, enter your GitLab.com user in the field provided. Click Provision Training Environment.

On the confirmation page, locate the Your GitLab Credentials section. Read this section carefully, noting the credential information provided and the expiration date. Your access to this group and all of the projects that you create is ephemeral and will be deleted after the expiration date.

Click the My Group button at the bottom of the page.

Sign in with your existing GitLab.com credentials.

You will be redirected to a My Test Group group that provides a sandbox for you to perform training lab steps in.

This group has a GitLab Ultimate license to see all of the features while your personal username namespace requires a paid subscription or a free trial to access all of the features.

From the My Test Group training subgroup, click the New project button.

Click the Create blank project tile.

In the Project name field, enter Security Labs.

The project slug will automatically populate. You can change this to a shorter string if desired for your own project. Leave it at the default for this lab.

If your lab environment URL starts with https://gitlab.com/gitlab-learn-labs/…, in the Project URL field, click the dropdown for the second half of the URL to make sure it’s pointing to a group name (starts with gitlab-learn-labs/*) and not a username. You should create this project inside a group, not directly in your user’s namespace.

If your group URL starts with https://ilt.gitlabtraining.cloud/..., in the Project URL field, click the dropdown for the second half of the URL to make sure it’s pointing to a group name (starts with training-users/*) and not a username. You should create this project inside a group, not directly in your user’s namespace.

Under Visibility Level, click Private.

Since the parent group above your group is private, all child groups and projects below will be private. You can learn more about project visibility levels in the documentation.

Check Initialize repository with a README.

Ensure that the Enable Static Application Security Testing (SAST) checkbox remains unchecked. For this lab, we will add the SAST functionality manually.

Click Create project.

# Task B. Turn Off Auto DevOps
By default, projects in some GitLab environments will enable Auto DevOps when no CI configuration file is found in a project. To avoid any conflicts between our CI/CD configuration and Auto DevOps, you should confirm that Auto DevOps is disabled in your project.

In the left sidebar, navigate to Settings > CI/CD.

Click on the Expand button next to Auto DevOps.

Ensure that Default to Auto DevOps pipeline is unchecked.

Click the Save changes button.

In the left sidebar, navigate to Code > Repository to return to your code.


# Task C. Enable and Configure SAST
Static Application Security Testing, or SAST, is the process of examining source code for vulnerabilities. You can use a SAST scan to automatically scan a code repository for known vulnerabilities. You can also use a SAST scan to check merge requests for vulnerabilities before merging the request. This process helps ensure that your code stays vulnerability free.

Create a new file in the main branch by clicking (+) > This directory > New file.

In the Filename field, type .gitlab-ci.yml. You will see a template appear automatically that you can leave blank for this task.

Define a single test stage:

stages:
- test
Keep in mind that YAML files should be indented with two spaces. Your web IDE may try to use a tab with 4 spaces. Simply use the backspace to set 2 spaces if you are not copying and pasting the examples.

Enable SAST by pasting the following text after the stages definition in .gitlab-ci.yml:

include:
- template: Security/SAST.gitlab-ci.yml
It is also possible to configure SAST through the GitLab UI by navigating to Secure > Security configuration and clicking the Configure SAST button. We will be configuring it by editing the CI file for this lab to help you learn more about how it works under the hood.

Add a variables section to the end of your .gitlab-ci.yml file and set the SAST_EXCLUDED_PATHS: venv/.

variables:
  SAST_EXCLUDED_PATHS: venv/
You can customize your SAST by adding configurations to the variables section of the .gitlab-ci.yml file. For example, the SAST_EXCLUDED_PATHS variable can exclude project paths from the SAST scan. This option can be set to prevent unnecessary scanning of files.

As an example, Python projects often contain a venv directory that contains packages used by the project. Since this directory does not contain our own source code, we should exclude it from the SAST scan.

A full list of SAST variables can be found in the documentation.

Add an appropriate commit message (ex. Add SAST template to .gitlab-ci.yml), set the Target Branch to main, then click the Commit changes button.

Once complete, you will have a .gitlab-ci.yml file that looks like this:

stages:
- test

include:
- template: Security/SAST.gitlab-ci.yml

variables:
  SAST_EXCLUDED_PATHS: venv/



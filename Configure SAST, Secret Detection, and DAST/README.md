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


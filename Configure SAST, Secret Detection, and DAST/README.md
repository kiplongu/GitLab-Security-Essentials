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



# Task D. Add a Vulnerability to the Application
With SAST scanning in place, you are now able to see security vulnerabilities inside of merge requests. To demonstrate how this SAST scan works, let’s create a branch with some vulnerable code.

Navigate to Code > Repository to see your project source code.

Add a new file by clicking (+) > This directory > New file.

Set the filename to main.py.

Introduce a new vulnerability (pretend this is accidental!) by adding the following code to your main.py file:

import subprocess

in = input("Enter your server ip: ")
subprocess.run(["ping", in])

gitlab_token = 'glpat-hC5G3PrMaZ7UkVZhxhnx'
rsa_data = '''
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: DES-EDE3-CBC,86C3F4011519BFBB
PxyzMAlAmEu/Qkx9nPh696SU7/MjXpCpOnfFiijLhJumNcRlWgsOiI9rfwlkh4aN
-----END RSA PRIVATE KEY-----
'''

print("Attempting to connect to the server")
print("Application authentication was successful")
Add an appropriate commit message (Ex. Add prompt for server authentication), set the target branch to add_auth.

Setting the target branch to add_auth will create a new branch named add_auth, and open a merge request on the branch.

Make sure that the Start a new merge request with these changes checkbox is checked.

Click the Commit changes button.

In the resulting merge request, set the name to Add prompt for server authentication, and leave all the fields as default.

Click the Create merge request button.

On the merge request page, you will see your security pipeline running. Wait for the pipeline to complete.

Once the pipeline completes, a new section will appear in the merge request, showing the results of the security scan.

You may need to refresh the page to see the new security scan section.

In this example, the security scan will show 1 new medium vulnerability. To view the details of the vulnerability report, click the Full report button.



# Task E. Merge Request Vulnerability Report
One of the main goals of security scanning is to prevent insecure code from making it into a repository. You can use the merge request vulnerability report to see all of the vulnerabilities that were detected in a single merge request. Note that this report will only show vulnerabilities that are newly introduced in the current merge request. If a vulnerability already exists in the repository, it will not show here, but will show in the project level vulnerability report.

In the vulnerability column, click the Improper neutralization of special elements used in an OS Command (‘OS Command Injection’) vulnerability.

In the resulting window, you will see details about the OS Command Injection vulnerability. Note that the detection shows the location of the vulnerability, which is line 4 of main.py. Click the X in the top right to close the modal window, or the Cancel button at the bottom.

In the sidebar, navigate to Code > Merge requests.

Click the merge request Add prompt for server authentication.

Click the Changes tab below the merge request title. On the main.py file, click vertical ellipses -> Edit in single-file editor.

The scan showed that line 4 contained a vulnerability.

# main.py Line 4
subprocess.run(["ping", in])
The problem is that this line uses user input to run a system command. In this situation, a user could craft an input that causes the application to execute an unintended command. To resolve this, you can either remove the command, or remove the user input from the command.

Remove lines 1 through 4 from the code. This is what the file should look like now.

gitlab_token = 'glpat-hC5G3PrMaZ7UkVZhxhnx'
rsa_data = '''
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: DES-EDE3-CBC,86C3F4011519BFBB
PxyzMAlAmEu/Qkx9nPh696SU7/MjXpCpOnfFiijLhJumNcRlWgsOiI9rfwlkh4aN
-----END RSA PRIVATE KEY-----
'''

print("Attempting to connect to the server")
print("Application authentication was successful")
Click the Commit changes button.

You may need to refresh the page to see the latest changes.

Click the Overview tab below the merge request title.

In the overview, you will see the security pipeline running. When the pipeline completes, refresh the page to see the security report. The security report will show no new potential vulnerabilities detected. With no new vulnerabilities detected, the branch is now safe to merge into main!

Leave Delete source branch checked and click Merge.



# Task F. Enable and Configure Secret Detection
In the last section, you applied SAST to detect vulnerabilities in your source code. In addition to scanning code for vulnerabilities, GitLab can also scan your code for secrets like keys and API tokens. Adding secret detection to your code will prevent leaking sensitive data in your repositories.

The Secret Detection job belongs to the test stage by default. Since your .gitlab-ci.yml already defines that stage, you don’t need to define it again.

In the Left sidebar, navigate to Code > Repository.

Click the .gitlab-ci.yml file. In the top right above the code, navigate to Edit > Edit single file.

Enable Secret Detection by adding template: Security/Secret-Detection.gitlab-ci.yml at the end of the existing include: section in .gitlab-ci.yml, below the template for SAST. This indent should be at the same level as the previous template.

include:
- template: Security/SAST.gitlab-ci.yml
- template: Security/Secret-Detection.gitlab-ci.yml
It is also possible to configure Secret Detection through the GitLab UI by navigating to Secure > Security configuration and clicking the Configure Secret Detection button. We will be configuring it by editing the CI file for this lab to help you learn more about how it works under the hood.

Configure Secret Detection to ignore the test directory by pasting this job definition at the end of .gitlab-ci.yml. The first line should have no indent.

secret_detection:
  variables:
    SECRET_DETECTION_EXCLUDED_PATHS: tests/
To configure Secret Detection to use non-default behavior, you can override the secret_detection job definition and add variables inside it.

A full list of Secret Detection variables can be found in the documentation.

Your .gitlab-ci.yml file will now look like this.

stages:
- test

include:
- template: Security/SAST.gitlab-ci.yml
- template: Security/Secret-Detection.gitlab-ci.yml

variables:
  SAST_EXCLUDED_PATHS: venv/

secret_detection:
  variables:
    SECRET_DETECTION_EXCLUDED_PATHS: tests/
You have already learned how to commit your changes to a new branch and create a merge request. Commit your changes to the add_secret_detection target branch. The commit message can be left at default or updated to Add Secret Detection to .gitlab-ci.yml. For single commit branches, the commit message is used as the merge request title.

Note: If you look at the security report on this merge request, you will notice that no vulnerabilities have been detected. This occurs because the secrets in main.py already exist in the main branch. The scan that occurs in the merge request will only show vulnerabilities that are newly introduced in the merge request. To see existing vulnerabilities, you will need to look at the project level vulnerability report covered in the next section.

Click the Merge button on your merge request after the pipeline passes.






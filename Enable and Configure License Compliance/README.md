# GitLab Security Essentials - Hands-On Lab: Enable and Configure License Compliance
This Hands-On Guide walks you through the lab exercises used in the GitLab Security Essentials course.
You are viewing the latest Version 16.x instructions. If your group URL starts with https://spt.gitlabtraining.cloud, please use the Version 15.x instructions

Estimated time to complete: 15 minutes

Objectives
When you add new dependencies to a project, you need to carefully consider how the licenses of the dependencies impact the project. To ensure that license requirements are met, you can add a policy to require approval for newly detected licenses.

In addition to scanning dependencies for vulnerabilities, the dependency scanner will also record the license that each dependency uses.

The License Compliance report will generate a list of all of the licenses detected in the project that are out of compliance with the project policies.

Prerequisites
Open your browser to to the Security Labs project that you created in Lab 1.

If you closed the tab or lost the link, open a browser tab and start typing https://gitlab.com/gitlab-learn-labs in your URL and the group should appear in your history.

Before beginning this lab, you should ensure that the dependency scanning template is enabled in your CI/CD pipeline. To verify this, check your .gitlab-ci.yml file. Under include, you should see the template Security/Dependency-Scanning.gitlab-ci.yml:

include:
- template: Security/Dependency-Scanning.gitlab-ci.yml
License scanning is completed by the dependency scanner. This template is required to generate and view license compliance reports in your project.



# Task A. License Compliance Scans
Navigate to Secure > License compliance.

Click any of the licenses to view more details about the license and the compliance requirements.



# Task B. Approve and Deny Licenses
Let’s assume that your team has approved the MIT license. If any license aside from the MIT license exists in a dependency, it must be approved before the merge request can be complete.

Navigate to Secure > Policies.

Click the New policy button.

Click Merge request approval policy > Select policy.

Input any name (ex. ScanApprovedPolicy) and description for the policy.

Set the Policy status to Enabled.

In Rules, set the Select scan type dropdown menu to License Scan. Ensure that all protected branches with No exceptions is selected for the merge request target.

Set the Status is dropdown menu to Newly Detected.

Set the first dropdown in the License is section to Except.

To exit the multi-select dropdown, click anywhere outside of it.

In the Select license types dropdown, click MIT License. There are several licenses with similar names, so be sure to pick the right one.

In the Actions section, require 1 approval from an Individual user. Click your username as the approval user.

Leave the Override project approval settings checkboxes selected and click Configure with a merge request.

In the resulting merge request, click the Merge button.

When you create a policy, GitLab will create a new project for managing the policies. After you click Merge, you will need to navigate back to your original project.



# Task C. View the License Compliance report with categorized licenses
Click Secure > Licenses compliance.

Notice that GitLab now describes every license except the MIT License as a policy violation. This report helps to flag existing dependencies which may contain licenses that you have not approved.



# Task D. Create an MR that adds a dependency with a denied license
Create a new branch named add-dnspython-dependency

Add a new dependency to the add-dnspython-dependency branch (not the main branch!) by pasting dnspython==2.1.0 as a new line at the end of requirements.txt.

Click the Create merge request button.

Leave all fields as their default values and click Create merge request.

Wait for the pipeline for the merge request to complete.

Once the pipeline completes, refresh the page. A license compliance scan will now display, showing 1 new license detected. Click Full report.

The license compliance report will list all of the licenses that are no longer in compliance with the project policy.

Return to the merge request overview.

Note that the merge status will appear stating Requires 1 approval from policy.

A new comment is also added to the activity of the merge request, stating that a policy violation was detected.

This merge request now requires additional approval because dnspython does not use an approved license. A reviewer can now verify if the license is ok to proceed with before the merge is complete.



# Task E. Lab Policy Cleanup
To ensure that you can merge branches into main without approval, it is best to disable the policy.

Navigate to Secure > Policies.

Click on the policy you created. Click Edit policy.

Click Delete Policy.

Click Merge on the resulting merge request.


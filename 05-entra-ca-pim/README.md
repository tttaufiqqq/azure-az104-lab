# 05 – Entra Conditional Access and PIM

Conditional Access with MFA, Privileged Identity Management with approval-based activation, and an access review on the lab groups.

## What I built
- Entra ID P2 trial activated on the tenant
- Conditional Access policy `CA-require-mfa-azure-mgmt` requiring MFA for Azure management
- PIM: `lab.webdev` eligible for the Security Reader role, activation approved by `lab.admin`
- Access review `AR-grp-vm-operators-quarterly` on `grp-vm-operators`, decided and completed
- Sign-in logs checked for the MFA-challenged sign-in

## Steps
1. Started the Entra ID P2 trial and assigned licenses
2. Created the Conditional Access policy and tested it by signing in as `lab.vmadmin`
3. Checked the sign-in logs
4. Made `lab.webdev` eligible for Security Reader in PIM
5. Requested activation as `lab.webdev`, approved it as `lab.admin`
6. Created the quarterly access review on `grp-vm-operators`
7. Completed the review decision in My Access and verified the result

## Evidence
![Licenses: all products](screenshots/01-entra-licenses-products.png)
Licenses > All products in the Entra admin center; Try/Buy options available.

![M365 admin error](screenshots/02-m365-admin-error.png)
Microsoft 365 admin center rejects the consumer account: `UnAuth` (error 100014).

![M365 admin home](screenshots/03-m365-admin-home.png)
Microsoft 365 admin center homepage signed in as `Lab.Admin`.

![Licenses menu](screenshots/04-entra-licenses-menu.png)
Licenses blade in the Entra admin center.

![Licenses trial](screenshots/05-licenses-trial.png)
Licenses page with the Entra ID P2 trial on the tenant.

![Assign license to users](screenshots/06-assign-license-users.png)
Active users in the Microsoft 365 admin center for license assignment.

![Entra overview](screenshots/07-entra-overview.png)
Entra admin center overview.

![Entra overview menu](screenshots/08-entra-overview-menu.png)
Entra admin center navigation before creating the policy.

![Create CA policy](screenshots/09-create-ca-policy.png)
New Conditional Access policy `CA-require-mfa-azure-mgmt`.

![CA policy assignments](screenshots/10-ca-policy-assignments.png)
Policy assignments: users and target resources.

![CA policy config](screenshots/11-ca-policy-config.png)
Conditional Access policy creation page.

![Entra menu](screenshots/12-entra-menu.png)
Entra admin center menu with Conditional Access.

![CA policies list](screenshots/13-ca-policies-list.png)
Conditional Access policies list showing the new policy.

![MFA prompt](screenshots/14-mfa-authenticator-prompt.png)
Sign-in as `lab.vmadmin` requires Microsoft Authenticator approval.

![CA overview after MFA](screenshots/15-ca-overview-after-mfa.png)
Conditional Access overview after the MFA test.

![Sign-in logs](screenshots/16-sign-in-logs.png)
Sign-in logs showing the MFA-challenged sign-in.

![PIM menu](screenshots/17-pim-menu.png)
Privileged Identity Management section of the Entra admin center.

![PIM menu roles](screenshots/18-pim-menu-roles.png)
PIM roles section in the Entra admin center.

![PIM Entra roles overview](screenshots/19-pim-entra-roles-overview.png)
PIM Microsoft Entra roles overview.

![PIM Security Reader assignment](screenshots/20-pim-security-reader-assignment.png)
Security Reader role assignments; `lab.webdev` added as eligible.

![PIM My roles](screenshots/21-pim-my-roles.png)
PIM My roles as `lab.webdev`.

![Activation scheduled](screenshots/22-pim-activation-scheduled.png)
Activation request scheduled: `lab.webdev`, Security Reader, Default Directory.

![Navigate access reviews](screenshots/23-navigate-access-reviews.png)
Navigating to access reviews in Identity Governance.

![PIM approve request](screenshots/24-pim-approve-request.png)
PIM Approve requests; `lab.admin` approves the activation.

![PIM My roles active](screenshots/25-pim-my-roles-active.png)
My roles showing the Security Reader activation.

![Create access review basics](screenshots/26-create-access-review-basics.png)
New access review: name and review type.

![Create access review scope](screenshots/27-create-access-review-scope.png)
Access review scope: group and app membership.

![Create access review reviewers](screenshots/28-create-access-review-reviewers.png)
Access review reviewers configuration.

![Create access review settings](screenshots/29-create-access-review-settings.png)
Access review settings: schedule and recurrence.

![Access reviews dashboard](screenshots/30-access-reviews-dashboard.png)
`AR-grp-vm-operators-quarterly` created in the access reviews dashboard.

![My Access overview](screenshots/31-my-access-overview.png)
My Access overview for `lab.admin`: 1 active access review.

![My Access reviews list](screenshots/32-my-access-reviews-list.png)
My Access shows the review due Nov 11, 2026.

![Access review decision](screenshots/33-access-review-decision.png)
Review decision: Approve `lab.vmadmin` in `grp-vm-operators`.

![Access review completed](screenshots/34-access-review-completed.png)
Access review progress: Completed.

![Access review overview](screenshots/35-access-review-overview.png)
Review overview: 1 user reviewed.

![Group members after review](screenshots/36-group-members-after-review.png)
`grp-vm-operators` members after the review.

## Exam notes
- Conditional Access policies are evaluated on every sign-in: signals, conditions, then grant (MFA) or block
- MFA registration and the Authenticator app approval flow are part of the same MFA grant
- PIM separates eligible from active: activation needs approval when an approver is configured
- PIM activation is time-bound and ends automatically
- Access reviews run on groups or apps; reviewers decide Approve/Deny in My Access
- Sign-in logs show the CA policy and MFA outcome per sign-in
- PIM and access reviews need Entra ID P2

## Used later
- `grp-vm-operators` keeps its VM access after the access review
- 06 reuses the same identity model for App Service role assignment

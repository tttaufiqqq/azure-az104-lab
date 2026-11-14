# 05 – Entra Conditional Access and PIM

Conditional Access with MFA, Privileged Identity Management with approval-based activation, and an access review on the lab groups.

## What I built
- Security defaults disabled (required before Conditional Access can be used)
- Entra ID P2 trial activated on the tenant (ends 12/8/2026)
- Conditional Access policy `CA-require-mfa-azure-mgmt`, On:
  - Users: includes `grp-vm-operators`; excludes `lab.admin` and the personal account
  - Target: Azure management, shown as Azure Resource Manager (app ID `797f4846-ba00-4fd7-ba43-dac1f8f63013`)
  - Grant: require MFA
- PIM Security Reader role:
  - `lab.webdev` eligible until 1/1/2027
  - Activation maximum 1 hour, justification required
  - Approval required, approver `lab.admin`
  - "On activation, require" left as None
- Access review `AR-grp-vm-operators-quarterly` on `grp-vm-operators`:
  - Quarterly, 3-day duration, 2 occurrences
  - Auto-apply enabled, "No change" if reviewers don't respond
  - Reviewer decision: Approve `lab.vmadmin`
- Sign-in logs checked for the MFA-challenged sign-in

## Steps
1. Disabled security defaults
2. Started the Entra ID P2 trial and assigned licenses
3. Created the Conditional Access policy and tested it by signing in as `lab.vmadmin`
4. Checked the sign-in logs
5. Configured the PIM Security Reader role and made `lab.webdev` eligible
6. Requested activation as `lab.webdev`, approved it as `lab.admin`
7. Created the quarterly access review on `grp-vm-operators`
8. Submitted the review decision (Approve) in My Access and checked the review status

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

![Entra navigation](screenshots/23-entra-navigation.png)
Entra admin center navigation after scheduling the activation.

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
My Access shows the reviewer's progress as Completed; the review itself stays Active.

![Access review overview](screenshots/35-access-review-overview.png)
Review overview: still Active, 1 user approved.

![Group members after review](screenshots/36-group-members-after-review.png)
`grp-vm-operators` members after the review.

## Troubleshooting
- **Consumer account blocked (error 100014):** the personal Gmail account is an external user and cannot manage the tenant. `lab.admin` is a native Global Administrator created for the admin work (screenshots 02 and 03).
- **P2 trial checkout:** the SST (sales and service tax) ID box was left unticked and empty at checkout.
- **PIM navigation:** PIM for Microsoft Entra roles is reached through search; "Azure resources" opens the wrong PIM side.
- **Shared incognito session:** admin and test accounts share one incognito browser session, so sign out and back in carefully between roles.
- **Approver without mailbox:** `lab.admin` has no mailbox, so activation approvals are completed in the portal, not by email.

## Exam notes
- Conditional Access policies are evaluated on every sign-in: signals, conditions, then grant (MFA) or block
- Conditional Access needs Entra ID P1
- Security defaults and Conditional Access cannot coexist; disabling security defaults is a prerequisite
- MFA registration and the Authenticator app approval flow are part of the same MFA grant
- PIM separates eligible from active: activation needs approval when an approver is configured
- PIM approvers are configured per role
- PIM activation is time-bound and ends automatically
- Access reviews run on groups or apps; reviewers decide Approve/Deny in My Access
- Sign-in logs show the CA policy and MFA outcome per sign-in
- PIM and access reviews need Entra ID P2

## Used later
- `grp-vm-operators` keeps its VM access after the access review

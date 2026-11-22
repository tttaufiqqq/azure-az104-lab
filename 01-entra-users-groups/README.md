# 01 – Entra Users and Groups

Test identities and security groups used for RBAC, PIM, and access reviews in later projects.

## 1. What I built
- 3 test users in the default Entra ID tenant
- 3 security groups (assigned membership), one user in each

| User | Group | Used for |
|---|---|---|
| `lab.vmadmin` | `grp-vm-operators` | VM access (03) |
| `lab.webdev` | `grp-webapp-contributors` | App Service access (06) |
| `lab.reader` | `grp-lab-readers` | Read-only and denied-access tests |

## 2. Steps
1. Signed in to the Entra admin center
2. Created the three users with temporary passwords
3. Created the three security groups
4. Added each user to its group
5. Verified membership
6. Signed in as a test user to verify the account works

## 3. Evidence
<img src="screenshots/01-create-users.png" alt="Create user" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Creating one of the test users with a temporary password.*

<img src="screenshots/02-users-list.png" alt="Users list" style="border: 1px solid #d0d7de; border-radius: 6px;">
*All three test users created in the tenant.*

<img src="screenshots/03-create-group.png" alt="Create group" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Creating a security group with assigned membership.*

<img src="screenshots/04-groups-list.png" alt="Groups list" style="border: 1px solid #d0d7de; border-radius: 6px;">
*All three security groups created.*

<img src="screenshots/05-group-vm-members.png" alt="VM operators group" style="border: 1px solid #d0d7de; border-radius: 6px;">
*`grp-vm-operators` contains `lab.vmadmin`.*

<img src="screenshots/06-group-webdev-members.png" alt="Web app contributors group" style="border: 1px solid #d0d7de; border-radius: 6px;">
*`grp-webapp-contributors` contains `lab.webdev`.*

<img src="screenshots/07-group-readers-members.png" alt="Readers group" style="border: 1px solid #d0d7de; border-radius: 6px;">
*`grp-lab-readers` contains `lab.reader`.*

## 4. Exam notes
- Assign roles to groups, not individual users
- Assigned membership: admins add members manually
- Dynamic membership needs Entra ID P1
- Security groups can hold RBAC roles; Microsoft 365 groups are for collaboration

## 5. Used later
- 03: custom RBAC role for `grp-vm-operators`
- 05: PIM eligibility and access reviews on these groups; `lab.admin` needed for PIM approvals and the P2 trial
- 06: Website Contributor role for `grp-webapp-contributors`

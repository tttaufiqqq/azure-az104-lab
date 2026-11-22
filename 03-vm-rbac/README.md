# 03 – VM and RBAC

Secured Linux VM in the lab network, with least-privilege access, tag enforcement, data disk encryption, and a cost alert.

## 1. What I built
- VM `vm-lab-01`: Ubuntu 24.04, Standard_B2als_v2, Malaysia West, in `snet-resources` of `vnet-lab`
- Custom RBAC role `VM Operator Custom`, assigned to `grp-vm-operators` at VM scope
- Azure Policy `require-env-tag` on `rg-lab-vm` (Deny)
- Azure Disk Encryption on the data disk, with Key Vault `kv-lab-03`
- Budget `budget-lab-monthly` with email alerts
- Role definition: [role-definition.json](role-definition.json)

## 2. Steps
1. Created the VM with a data disk, tags `env=lab` and `project=az104`, and no public inbound ports
2. Added an NSG rule for SSH from my IP only
3. Assigned the tag policy and tested it with an untagged public IP
4. Created the custom role and assigned it to the group at VM scope
5. Tested access as `lab.vmadmin`
6. Created the Key Vault, formatted and mounted the data disk, and enabled ADE
7. Created the budget with actual and forecasted alerts

## 3. Evidence
<img src="screenshots/01-create-data-disk.png" alt="Create data disk" style="border: 1px solid #d0d7de; border-radius: 6px;">
*New data disk attached during VM creation.*

<img src="screenshots/02-create-vm.png" alt="Create VM" style="border: 1px solid #d0d7de; border-radius: 6px;">
*VM creation wizard.*

<img src="screenshots/03-vm-overview.png" alt="VM overview" style="border: 1px solid #d0d7de; border-radius: 6px;">
*VM running with tags `env=lab` and `project=az104`.*

<img src="screenshots/04-nsg-ssh-rule.png" alt="NSG SSH rule" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Inbound SSH rule allowing only my IP.*

<img src="screenshots/05-ssh-session.png" alt="SSH session" style="border: 1px solid #d0d7de; border-radius: 6px;">
*SSH into the VM; private IP 10.0.1.4 confirms it sits in `snet-resources`.*

<img src="screenshots/06-policy-assignment.png" alt="Policy assignment" style="border: 1px solid #d0d7de; border-radius: 6px;">
*`require-env-tag` assigned to `rg-lab-vm`.*

<img src="screenshots/07-policy-deny-public-ip.png" alt="Policy deny" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Untagged public IP creation denied by policy.*

<img src="screenshots/08-create-custom-role.png" alt="Create custom role" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Custom role `VM Operator Custom` with six actions.*

<img src="screenshots/09-role-assignments-iam.png" alt="Role assignments" style="border: 1px solid #d0d7de; border-radius: 6px;">
*IAM on the VM: group assigned the custom role at VM scope.*

<img src="screenshots/10-access-test-stop-vm.png" alt="Access test: stop" style="border: 1px solid #d0d7de; border-radius: 6px;">
*`lab.vmadmin` can stop the VM.*

<img src="screenshots/11-access-test-delete-denied.png" alt="Access test: delete denied" style="border: 1px solid #d0d7de; border-radius: 6px;">
*`lab.vmadmin` cannot delete the VM.*

<img src="screenshots/12-access-test-denied-notification.png" alt="Access test: notification" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Deletion failure notification.*

<img src="screenshots/13-lsblk-before-encryption.png" alt="lsblk before encryption" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Data disk `sda` (4 GiB) and OS disk `sdb` (30 GiB) with its partitions.*

<img src="screenshots/14-format-mount-disk.png" alt="Format and mount" style="border: 1px solid #d0d7de; border-radius: 6px;">
*`parted`, `mkfs.ext4`, and `mount` on the data disk.*

<img src="screenshots/15-ade-disk-settings.png" alt="ADE settings" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Azure Disk Encryption settings with Key Vault `kv-lab-03`.*

<img src="screenshots/16-ade-deploy-failed.png" alt="ADE deployment failed" style="border: 1px solid #d0d7de; border-radius: 6px;">
*ADE extension deployment failed in the activity log.*

<img src="screenshots/17-ade-policy-violation.png" alt="ADE policy violation" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Failure detail: `RequestDisallowedByPolicy` from `require-env-tag`.*

<img src="screenshots/18-ade-extension.png" alt="ADE extension" style="border: 1px solid #d0d7de; border-radius: 6px;">
*`AzureDiskEncryptionForLinux` extension on the VM.*

<img src="screenshots/19-lsblk-encrypted.png" alt="lsblk encrypted" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Data partition shows `crypto_LUKS`; BEK volume holds the unlock key.*

<img src="screenshots/20-budget-alert.png" alt="Budget" style="border: 1px solid #d0d7de; border-radius: 6px;">
*`budget-lab-monthly` with alert thresholds configured.*

## 4. Troubleshooting
- **Problem:** ADE failed with `InvalidTemplateDeployment` and `RequestDisallowedByPolicy`
- **Cause:** the ADE extension deployed without an `env` tag, so `require-env-tag` denied it
- **Fix:** set the policy to Do not enforce, enabled ADE, then restored enforcement

## 5. Design notes
- Encrypted the data disk only: Linux OS disk encryption needs more memory than a 4 GiB VM has
- Data disk added to `/etc/fstab` with `nofail` before encryption
- ADE retires on 15 September 2028; encryption at host is the replacement
- Budget scope is the subscription; alerts only notify and do not stop spending
- VM and its NIC, public IP, and disks sit in different resource groups from the VNet

## 6. Exam notes
- Custom role: `Actions`, `NotActions`, `DataActions`, `AssignableScopes`
- Assignments are inherited by child scopes; assign roles to groups
- Policy effects: Deny blocks, Audit flags, Modify can add tags
- Deny policies also block extensions and template deployments
- Budgets alert on actual or forecasted cost and can trigger action groups

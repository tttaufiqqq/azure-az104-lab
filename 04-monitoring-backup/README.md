# 04 – Monitoring and Backup

Monitoring, alerting, and backup for `vm-lab-01`, using one Log Analytics workspace and one Recovery Services vault.

## 1. What I built
- Log Analytics workspace `law-lab`
- Azure Monitor Agent on the VM, with data collection rule `dcr-lab-vm` (performance counters and auth syslog to `law-lab`)
- Action group `ag-lab-email`
- Metric alert `alert-vm-cpu-high` (average CPU above 70%)
- Log alert `alert-ssh-failed-login` (KQL on failed SSH logins)
- KQL queries for heartbeat, failed logins, and CPU trend
- Recovery Services vault `rsv-lab`, policy `bp-lab-daily`, a completed backup, and a disk restore

## 2. Steps
1. Created the workspace
2. Created the DCR with Performance Counters and Linux Syslog (auth, authpriv), destination `law-lab`
3. Installed the Azure Monitor Agent and confirmed data in the workspace
4. Created the action group and both alert rules
5. Tested the alerts: CPU load with `yes`, wrong SSH passwords
6. Created the vault and policy (daily, 7-day retention) and enabled backup
7. Ran Backup now, then restored the disks to a new resource group

## 3. Evidence
<img src="screenshots/01-workspace-overview.png" alt="Workspace" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Log Analytics workspace `law-lab`.*

<img src="screenshots/02-dcr-add-data-source.png" alt="DCR data source" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Adding a data source to the DCR.*

<img src="screenshots/03-dcr-syslog-source.png" alt="DCR syslog source" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Syslog facilities selected (LOG_AUTH, LOG_AUTHPRIV).*

<img src="screenshots/04-dcr-perf-destination.png" alt="DCR perf destination" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Performance counters sent to the Log Analytics workspace.*

<img src="screenshots/05-create-dcr.png" alt="Create DCR" style="border: 1px solid #d0d7de; border-radius: 6px;">
*DCR creation basics.*

<img src="screenshots/06-dcr-overview.png" alt="DCR overview" style="border: 1px solid #d0d7de; border-radius: 6px;">
*`dcr-lab-vm` with its data sources and destination.*

<img src="screenshots/07-ama-installed.png" alt="AMA installed" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Azure Monitor Agent service running on the VM.*

<img src="screenshots/08-kql-syslog.png" alt="KQL syslog" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Syslog rows from the VM in the workspace.*

<img src="screenshots/09-kql-failed-logins.png" alt="KQL failed logins" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Failed SSH logins found with KQL.*

<img src="screenshots/10-kql-perf.png" alt="KQL perf" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Performance data queried from the `Perf` table.*

<img src="screenshots/11-action-group.png" alt="Action group" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Action group `ag-lab-email` with email notification.*

<img src="screenshots/12-metric-alert-condition.png" alt="Metric alert condition" style="border: 1px solid #d0d7de; border-radius: 6px;">
*CPU metric alert: average CPU above 70%.*

<img src="screenshots/13-log-alert-condition.png" alt="Log alert condition" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Log alert with a KQL condition.*

<img src="screenshots/14-alert-actions.png" alt="Alert actions" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Action group attached to the alert rule.*

<img src="screenshots/15-alert-dimensions.png" alt="Alert dimensions" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Alert splitting by dimensions.*

<img src="screenshots/16-log-alert-details.png" alt="Log alert details" style="border: 1px solid #d0d7de; border-radius: 6px;">
*`alert-ssh-failed-login` rule details.*

<img src="screenshots/17-alert-rules-list.png" alt="Alert rules list" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Both alert rules created and enabled.*

<img src="screenshots/18-cpu-load-test.png" alt="CPU load test" style="border: 1px solid #d0d7de; border-radius: 6px;">
*`top` on the VM showing ~98% CPU from the load test.*

<img src="screenshots/19-log-alert-fired.png" alt="Log alert fired" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Failed-login alert email fired.*

<img src="screenshots/20-metric-alert-fired.png" alt="Metric alert fired" style="border: 1px solid #d0d7de; border-radius: 6px;">
*CPU alert email: severity 2, triggered for `vm-lab-01`.*

<img src="screenshots/21-recovery-vault.png" alt="Recovery vault" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Recovery Services vault `rsv-lab`.*

<img src="screenshots/22-backup-failure-alerts.png" alt="Backup failure alerts" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Backup failure alerts on the vault.*

<img src="screenshots/23-keyvault-access-policies.png" alt="Key Vault access policies" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Backup Management Service permissions on `kv-lab-03`.*

<img src="screenshots/24-backup-trigger-notification.png" alt="Backup trigger" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Backup now triggered, updating the Key Vault.*

<img src="screenshots/25-backup-job-completed.png" alt="Backup job completed" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Backup job completed: snapshot and transfer done.*

<img src="screenshots/26-create-staging-storage.png" alt="Staging storage" style="border: 1px solid #d0d7de; border-radius: 6px;">
*New storage account for the restore staging location.*

<img src="screenshots/27-restore-configuration.png" alt="Restore configuration" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Restore disks configuration with staging location.*

<img src="screenshots/28-restore-job-completed.png" alt="Restore completed" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Restore job completed; disks restored to a separate resource group.*

## 4. Troubleshooting
**1. Agent install failed**
- Error: `OperationNotAllowed`, cannot modify extensions when the VM is not running
- Cause: the VM was deallocated
- Fix: started the VM and reinstalled the agent

**2. `Perf` table empty**
- Cause: the performance counter data source only had Azure Monitor Metrics as its destination
- Fix: added `law-lab` as a Log Analytics destination on the DCR
- Result: `Perf` rows appeared within minutes

**3. Backup failed on the encrypted VM**
- Symptom: 3 Backup Failure alerts; pre-check Critical
- Error: `UserErrorKeyVaultPermissionsNotConfigured`
- Cause: Backup Management Service had Backup permission on certificates only, not on keys or secrets in `kv-lab-03`
- Fix: added Backup (plus Get, List) to key and secret permissions, then ran Backup now
- Lesson: encrypted VM backups need Key Vault permissions on keys and secrets; alert and pre-check status only clear after a new backup runs

## 5. Design notes
- One workspace and one action group are reused by every alert
- Restore used a new staging storage account, because `stlablab` has public access disabled
- Backup now uses its own retention (30 days), separate from the policy
- Log alerts cost more per evaluation than metric alerts

## 6. Exam notes
- Azure Monitor is the platform; Log Analytics is where log data is stored and queried with KQL
- Metric alerts fire on a numeric measurement; log alerts fire on a KQL query result
- Metrics and guest logs are different stores: a DCR needs a Log Analytics destination to fill `Perf` and `Syslog`
- Azure Monitor Agent replaces the old Log Analytics agent
- Restore options: create new VM, restore disks, replace existing, file recovery
- The vault and the VM must be in the same region
- Backup vaults and Recovery Services vaults are different resource types

# 04 – Monitoring and Backup

Monitoring, alerting, and backup for `vm-lab-01`, using one Log Analytics workspace and one Recovery Services vault.

## What I built
- Log Analytics workspace `law-lab`
- Azure Monitor Agent on the VM, with data collection rule `dcr-lab-vm` (performance counters and auth syslog to `law-lab`)
- Action group `ag-lab-email`
- Metric alert `alert-vm-cpu-high` (average CPU above 70%)
- Log alert `alert-ssh-failed-login` (KQL on failed SSH logins)
- KQL queries for syslog, failed logins, and performance data
- Recovery Services vault `rsv-lab`, policy `bp-lab-daily`, a completed backup, and a disk restore

## Steps
1. Created the workspace
2. Created the DCR with Performance Counters and Linux Syslog (auth, authpriv), destination `law-lab`
3. Installed the Azure Monitor Agent and confirmed data in the workspace
4. Created the action group and both alert rules
5. Tested the alerts: CPU load with `yes`, wrong SSH passwords
6. Created the vault and policy (daily, 7-day retention) and enabled backup
7. Ran Backup now, then restored the disks to a new resource group

## Evidence
![Workspace](screenshots/01-workspace-overview.png)
Log Analytics workspace `law-lab`.

![DCR data source](screenshots/02-dcr-add-data-source.png)
Adding a data source to the DCR.

![DCR syslog source](screenshots/03-dcr-syslog-source.png)
Syslog facilities selected (LOG_AUTH, LOG_AUTHPRIV).

![DCR perf destination](screenshots/04-dcr-perf-destination.png)
Performance counters sent to the Log Analytics workspace.

![Create DCR](screenshots/05-create-dcr.png)
DCR creation basics.

![DCR overview](screenshots/06-dcr-overview.png)
`dcr-lab-vm` with its data sources and destination.

![AMA installed](screenshots/07-ama-installed.png)
Azure Monitor Agent service running on the VM.

![KQL syslog](screenshots/08-kql-syslog.png)
Syslog rows from the VM in the workspace.

![KQL failed logins](screenshots/09-kql-failed-logins.png)
Failed SSH logins found with KQL.

![KQL perf](screenshots/10-kql-perf.png)
Performance data queried from the `Perf` table.

![Action group](screenshots/11-action-group.png)
Action group `ag-lab-email` with email notification.

![Metric alert condition](screenshots/12-metric-alert-condition.png)
CPU metric alert: average CPU above 70%.

![Log alert condition](screenshots/13-log-alert-condition.png)
Log alert with a KQL condition.

![Alert actions](screenshots/14-alert-actions.png)
Action group attached to the alert rule.

![Alert dimensions](screenshots/15-alert-dimensions.png)
Alert splitting by dimensions.

![Log alert details](screenshots/16-log-alert-details.png)
`alert-ssh-failed-login` rule details.

![Alert rules list](screenshots/17-alert-rules-list.png)
Both alert rules created and enabled.

![CPU load test](screenshots/18-cpu-load-test.png)
`top` on the VM showing ~98% CPU from the load test.

![Log alert fired](screenshots/19-log-alert-fired.png)
Failed-login alert email fired.

![Metric alert fired](screenshots/20-metric-alert-fired.png)
CPU alert email: severity 2, triggered for `vm-lab-01`.

![Recovery vault](screenshots/21-recovery-vault.png)
Recovery Services vault `rsv-lab`.

![Backup failure alerts](screenshots/22-backup-failure-alerts.png)
Backup failure alerts on the vault.

![Key Vault access policies](screenshots/23-keyvault-access-policies.png)
Backup Management Service permissions on `kv-lab-03`.

![Backup trigger](screenshots/24-backup-trigger-notification.png)
Backup now triggered, updating the Key Vault.

![Backup job completed](screenshots/25-backup-job-completed.png)
Backup job completed: snapshot and transfer done.

![Staging storage](screenshots/26-create-staging-storage.png)
New storage account for the restore staging location.

![Restore configuration](screenshots/27-restore-configuration.png)
Restore disks configuration with staging location.

![Restore completed](screenshots/28-restore-job-completed.png)
Restore job completed; disks restored to a separate resource group.

## Troubleshooting
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

## Design notes
- One workspace and one action group are reused by every alert
- Restore used a new staging storage account, because `stlablab` has public access disabled
- Backup now uses its own retention (30 days), separate from the policy
- Log alerts cost more per evaluation than metric alerts

## Exam notes
- Azure Monitor is the platform; Log Analytics is where log data is stored and queried with KQL
- Metric alerts fire on a numeric measurement; log alerts fire on a KQL query result
- Metrics and guest logs are different stores: a DCR needs a Log Analytics destination to fill `Perf` and `Syslog`
- Azure Monitor Agent replaces the old Log Analytics agent
- Restore options: create new VM, restore disks, replace existing, file recovery
- The vault and the VM must be in the same region
- Backup vaults and Recovery Services vaults are different resource types

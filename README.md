\# Azure AZ-104 Lab



Hands-on Azure administration lab built as one connected environment, not six separate demos. Each project reuses what the previous one created, and the full setup can be redeployed from a single template.



\## Goals



\- Practise the AZ-104 exam domains by building and operating real resources

\- Show identity, networking, compute, monitoring, backup, and app hosting working together

\- Document each step with screenshots and short explanations



\## Architecture



!\[Architecture diagram](docs/architecture.png)



\- \*\*Identity:\*\* Entra ID users and groups drive RBAC across every project

\- \*\*Network:\*\* one VNet with a resource subnet, a private endpoint subnet, and an NSG

\- \*\*Compute:\*\* a VM in the resource subnet, plus an App Service with slots

\- \*\*Storage:\*\* private-endpoint storage account with public access disabled

\- \*\*Operations:\*\* one Log Analytics workspace, shared alerts, Recovery Services vault

\- \*\*Governance:\*\* tag policy and budget alert at subscription level



\## Build Order



| Step | Folder | Focus | Status |

|---|---|---|---|

| 1 | \[01-entra-users-groups](01-entra-users-groups/) | Users and groups | ⬜ |

| 2 | \[02-network-storage](02-network-storage/) | VNet, NSG, private endpoint, ARM template | ⬜ |

| 3 | \[03-vm-rbac](03-vm-rbac/) | VM, custom RBAC role, tag policy, encryption, budget alert | ⬜ |

| 4 | \[04-monitoring-backup](04-monitoring-backup/) | Alerts, Log Analytics, KQL, VM backup and restore | ⬜ |

| 5 | \[05-entra-ca-pim](05-entra-ca-pim/) | Conditional Access, PIM, access review, sign-in logs | ⬜ |

| 6 | \[06-app-service](06-app-service/) | Slots, zero-downtime swap, autoscale, backup | ⬜ |



\## Project Summaries



\### 01 – Entra Users and Groups

\- Test users and security groups

\- Groups used later for RBAC, PIM, and access reviews



\### 02 – Network and Storage

\- VNet with two subnets (resources, private endpoints)

\- NSG with explicit allow rules only

\- Storage account with public access off and a private endpoint

\- Private DNS zone linked to the VNet

\- ARM template to redeploy everything



\### 03 – VM and RBAC

\- VM placed in the resource subnet from step 2

\- Custom RBAC role scoped to the VM

\- Azure Policy enforcing required tags

\- Disk encryption configured

\- Budget alert at a set threshold



\### 04 – Monitoring and Backup

\- Metric alert (CPU) and log alert (KQL)

\- Log Analytics workspace with diagnostic settings

\- KQL query for an operational signal

\- Recovery Services vault, backup policy, and a verified test restore



\### 05 – Entra Conditional Access and PIM

\- Conditional Access policy requiring MFA for one app

\- PIM with approval-based activation (eligible vs active)

\- Access review on the lab groups

\- Sign-in log check, also queried in KQL



\### 06 – App Service

\- S1 plan with production and staging slots

\- App deployed from GitHub (\[web app repo](https://github.com/your-username/your-webapp-repo))

\- Slot swap with zero downtime

\- CPU-based and scheduled autoscale rules

\- Backup with retention policy



\## How the Projects Connect



\- Entra groups from step 1 receive roles in steps 3 and 6

\- The VM from step 3 sits in the network from step 2

\- Step 4 monitors and backs up that VM

\- PIM and access reviews in step 5 apply to the same groups and roles

\- App Service diagnostics and alerts reuse the step 4 workspace and action group

\- `templates/` rebuilds the combined environment



\## Repository Structure



```

azure-az104-lab/

├── README.md

├── 01-entra-users-groups/

├── 02-network-storage/

├── 03-vm-rbac/

├── 04-monitoring-backup/

├── 05-entra-ca-pim/

├── 06-app-service/

├── docs/

└── templates/

```



\- Each project folder has a `README.md` and a `screenshots/` folder

\- Each project README covers what was built, steps, and proof screenshots



\## Original Project Mapping



| Folder | Original project |

|---|---|

| 01-entra-users-groups | 4 (part 1) |

| 02-network-storage | 2 |

| 03-vm-rbac | 1 |

| 04-monitoring-backup | 3 |

| 05-entra-ca-pim | 4 (part 2) |

| 06-app-service | 5 |



\## Skills Demonstrated



\- Entra ID, RBAC, Conditional Access, PIM, access reviews

\- Virtual networking, NSGs, private endpoints, private DNS

\- VM deployment, disk encryption, Azure Policy, cost management

\- Azure Monitor, Log Analytics, KQL, alerting

\- Azure Backup and restore

\- App Service, deployment slots, autoscaling

\- Infrastructure as code with ARM/Bicep



\## Notes



\- Entra P2 trial needed for PIM and access reviews (started at step 5)

\- App Service slots, autoscale, and backup need Standard (S1) or higher

\- Resources deleted after each project to control cost

\- Subscription IDs, tenant IDs, emails, and public IPs redacted in screenshots

\- No secrets committed to the repo


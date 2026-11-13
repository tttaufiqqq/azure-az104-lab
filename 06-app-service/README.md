# 06 – App Service and Autoscaling

Linux App Service with deployment slots, a zero-downtime swap, autoscale rules, and scheduled backup. The web app is a minimal Node.js server in a separate repo that shows version, slot, and setting values on a coloured page.

## What I built
- App Service plan `asp-lab`: Premium V3 P0V3, Linux, Malaysia West
- Web app `app-lab-1612`: Node 22 LTS, deployed from GitHub
- Deployment slot `staging`, cloned from production
- Slot settings demo with `SLOT_LABEL` (sticky) and `APP_MESSAGE` (swaps)
- Zero-downtime slot swap: staging ⇄ production
- Autoscale setting `autoscale-asp-lab` on `asp-lab`
- Scheduled daily backup with 7-day retention, storage account `stlabappbackup` in `rg-lab-app`
- Web app repo: [az104-webapp](https://github.com/tttaufiqqq/az104-webapp)

## Plan note
The portal no longer listed Standard S1 as an option. Basic has no deployment slots. Premium V3 P0V3 (about USD 0.08/hr) was the smallest plan that supports slots, autoscale, and backup on this subscription. A larger P1MV3 failed subscription validation on the free trial.

## Steps
1. Created `rg-lab-app`, the plan, and the web app
2. Connected production to the GitHub repo (`main` branch) via Deployment Center
3. Added app settings on production (`SLOT_LABEL` sticky, `APP_MESSAGE` ordinary)
4. Created the `staging` slot and deployed the `staging` branch to it
5. Performed the slot swap
6. Configured autoscale rules on the plan
7. Configured the custom daily backup

## Evidence
![App Service plan](screenshots/01-asp-plan-overview.png)
Plan `asp-lab` overview: CPU, memory, and data metrics.

![Create Web App](screenshots/02-create-web-app.png)
Marketplace: Create Web App.

![Create Web App basics](screenshots/03-create-web-app-basics.png)
Web App creation wizard.

![Web app overview](screenshots/04-web-app-overview.png)
`app-lab-1612` overview after creation.

![Deployment Center](screenshots/05-deployment-center.png)
Deployment Center for the production app.

![Configuration](screenshots/06-configuration.png)
Configuration: stack settings, health check, FTPS.

![GitHub Actions workflow](screenshots/07-github-actions-workflow.png)
Azure-generated workflow: build and deploy Node.js to `app-lab-1612`.

![Production first visit](screenshots/08-production-v1-no-settings.png)
Production before settings: v1, no slot label, no message.

![App settings](screenshots/09-app-settings-production.png)
Environment variables: `APP_MESSAGE = from production`; `SLOT_LABEL` marked as deployment slot setting.

![Production v1](screenshots/10-production-v1.png)
Production before swap: v1, blue, with both settings applied.

![SSH console](screenshots/11-ssh-console.png)
SSH web console on the Linux app.

![printenv](screenshots/12-printenv-slot-vars.png)
`printenv | grep -i slot`: `APPSETTING_SLOT_LABEL=production`; `WEBSITE_SLOT_NAME` is absent.

![Git push rejected](screenshots/13-git-push-rejected.png)
Push rejected: Azure had added a workflow file to `main`.

![Production slot fixed](screenshots/14-production-v1-slot-fixed.png)
After the `server.js` fix, the slot is derived from `WEBSITE_HOSTNAME`: Production.

![Staging commit](screenshots/15-git-staging-commit.png)
Commit `staging v2` on the staging branch.

![Staging Deployment Center](screenshots/16-staging-deployment-center.png)
Deployment Center of the staging slot.

![Push staging](screenshots/17-git-push-staging.png)
Staging branch pushed to GitHub.

![Staging deployment history](screenshots/18-staging-deployment-history.png)
Staging slot deployment history.

![Staging v2](screenshots/19-staging-v2.png)
Staging before swap: v2, green.

![Web app menu](screenshots/20-web-app-menu.png)
Web app menu with Deployment slots.

![Swap notification](screenshots/21-swap-success-notification.png)
Notification: successfully swapped slots `staging` and `production`.

![Production after swap](screenshots/22-production-after-swap.png)
Production after swap: v2, green, `SLOT_LABEL` still `production`, `APP_MESSAGE` now `from staging`.

![Staging after swap](screenshots/23-staging-after-swap.png)
Staging after swap: v1, blue, `SLOT_LABEL` still `staging`, `APP_MESSAGE` now `from production`.

![Autoscale rules](screenshots/24-autoscale-rules.png)
Scale-out at CPU > 70%, scale-in at CPU < 30%.

![Configure custom backup](screenshots/25-configure-custom-backup.png)
Custom backup configuration on `app-lab-1612`.

![Backup list](screenshots/26-backup-list.png)
Backups list: custom and automatic backups together.

![Web app menu backups](screenshots/27-web-app-menu-backups.png)
Web app menu with Backups.

![Backup storage container](screenshots/28-backup-storage-container.png)
`stlabappbackup` storage account with the `appbackups` container.

![Resource group](screenshots/29-rg-lab-app-overview.png)
`rg-lab-app` with the plan, web app, and backup storage.

## The web app
Three files, no `npm install`:
- `server.js`: HTTP server showing version, slot name, and settings
- `package.json`: start script `node server.js`, engine `node >=20`
- `/health`: plain-text health endpoint

`main` branch is `v1` (blue). `staging` branch is `v2` (green).

## Slot settings and the swap

| Setting | Deployment slot setting | Production | Staging |
|---|---|---|---|
| `SLOT_LABEL` | sticky | `production` | `staging` |
| `APP_MESSAGE` | swaps with the app | `from production` | `from staging` |

**Before the swap:** production = `v1` blue, staging = `v2` green.

**Swap dialog** (source `staging`, target production, no preview): the Config Changes panel listed only `APP_MESSAGE`. `SLOT_LABEL` did not appear, confirming it was sticky.

**After the swap:** production = `v2` green, `SLOT_LABEL` still `production`, `APP_MESSAGE` now `from staging`. Staging = `v1` blue, `SLOT_LABEL` still `staging`, `APP_MESSAGE` now `from production`.

**Two things worth noting:**
- The platform row (`WEBSITE_SLOT_NAME`) showed `staging` on the swapped production page. The swap moved an already-running process, and the platform variable was not refreshed. The app reads `WEBSITE_HOSTNAME` at start for the slot display, so a restart would correct it.
- Deployment slots do not change which Git branch each slot tracks. After the swap, a push to `main` would redeploy `v1` over the swapped `v2`.

## Autoscale
- Setting name `autoscale-asp-lab`, on `asp-lab`
- Instance limits: minimum 1, maximum 2, default 1
- **Scale-out:** average CPU > 70% over 10 minutes ⇄ add 1 instance, cool down 5 minutes
- **Scale-in:** average CPU < 30% ⇄ remove 1 instance
- Metric-based only; no scheduled condition was configured

## Backup
- **Automatic backup:** built-in, every 1 hour, free
- **Custom backup:** configured daily with 7-day retention, stored in `stlabappbackup` / `appbackups` container
- Both appear in the same list on the Backups page
- Backup is per slot; production backup after the swap contains v2
- Backup needs Standard or higher (here Premium V3 P0V3)
- Keep at least one backup is ticked; retention is 7 days

## Troubleshooting
- **GitHub OIDC deployment failed.** The portal generated an OIDC workflow, but the deployment failed with a token verification error. Fix: turned on SCM Basic Auth Publishing Credentials in Configuration > General settings and switched Deployment Center to use Basic authentication.
- **Staging slot workflow failed on first run.** The Azure login step failed, then succeeded on a retry. The exact fix was not identified; the slot worked afterwards.
- **`WEBSITE_SLOT_NAME` not set.** `printenv` via SSH confirmed the platform variable was absent on this Linux app. `server.js` was updated to derive the slot name from `WEBSITE_HOSTNAME` instead.
- **First push rejected.** Azure had added a workflow file to `main` on GitHub. Fix: `git pull --rebase` before pushing.

## Exam notes
- **Deployment slot settings:** a setting marked as a slot setting stays with the slot; it does not move on a swap. Use it for connection strings and settings that differ per environment.
- **Slot swap:** moves the app code and all non-sticky settings. The swap is atomic and reversible by swapping back.
- **The exam question:** "what happens to sticky settings on a swap?" - they stay with the slot.
- **Autoscale vs manual scale:** autoscale changes instance count automatically on rules; manual scale sets a fixed count.
- **Backup limits:** one storage account per app, up to about 10 GB, same subscription.
- **Deployment slots need Standard or higher.** Slots, autoscale, and backup are not available on Free or Basic.

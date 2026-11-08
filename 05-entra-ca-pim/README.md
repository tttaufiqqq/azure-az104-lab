# 05 – Entra Conditional Access and PIM

Conditional Access, Privileged Identity Management, and access reviews on the lab groups.

> Status: in progress. So far only the Entra ID P2 trial license setup is done.

## Planned
- Conditional Access policy requiring MFA for one app
- PIM with approval-based activation (eligible vs active)
- Access review on the lab groups
- Sign-in log check, also queried in KQL

## Steps so far
1. Checked the Entra ID licenses page for P2 availability
2. Tried the Microsoft 365 admin center with a consumer account (blocked)
3. Signed in to the Microsoft 365 admin center as `Lab.Admin`
4. Reopened the licenses blade in the Entra admin center to start the P2 trial

## Evidence
![Licenses: all products](screenshots/01-entra-licenses-products.png)
Licenses > All products in the Entra admin center; Try/Buy options available.

![M365 admin error](screenshots/02-m365-admin-error.png)
Microsoft 365 admin center rejects the consumer account: `UnAuth` (error 100014).

![M365 admin home](screenshots/03-m365-admin-home.png)
Microsoft 365 admin center homepage signed in as `Lab.Admin`.

![Licenses menu](screenshots/04-entra-licenses-menu.png)
Licenses blade in the Entra admin center.

## Notes
- Entra ID P2 is required for PIM and access reviews (trial started here)
- Consumer accounts cannot manage the tenant; the admin work uses `Lab.Admin`

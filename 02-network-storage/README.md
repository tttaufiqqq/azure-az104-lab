# 02 – Network and Storage

Private network with a locked-down storage account reachable only through a private endpoint.

## What I built
- VNet `vnet-lab` (10.0.0.0/16) in Malaysia West
- Subnets: `snet-resources` (10.0.1.0/24), `snet-privateendpoints` (10.0.2.0/24)
- NSG `nsg-resources` attached to `snet-resources`
- Storage account with public network access disabled
- Private endpoint for Blob, with private DNS zone `privatelink.blob.core.windows.net`
- Exported ARM template: [template.json](template.json)

## Steps
1. Created resource group `rg-lab-network`
2. Created the VNet with two subnets
3. Created the NSG with an RDP rule (my IP only) and a VNet outbound rule, then attached it to the resources subnet
4. Created the storage account with public access disabled
5. Added the private endpoint with DNS integration
6. Verified access from outside the VNet is blocked
7. Exported the ARM template

## Evidence
![Resource group](screenshots/01-create-resource-group.png)
Resource group `rg-lab-network` for the network resources.

![VNet deployed](screenshots/02-vnet-deployed.png)
`vnet-lab` deployment completed.

![Address space](screenshots/03-vnet-address-space.png)
Address space 10.0.0.0/16 with the two subnets.

![VNet overview](screenshots/04-vnet-overview.png)
VNet overview showing both subnets.

![Create NSG](screenshots/05-create-nsg.png)
Creating `nsg-resources`.

![NSG inbound](screenshots/06-nsg-inbound-rdp.png)
Inbound RDP allowed from one IP only.

![NSG outbound](screenshots/07-nsg-outbound-vnet.png)
Outbound allowed within the VNet.

![NSG association](screenshots/08-nsg-subnet-association.png)
NSG attached to `snet-resources`.

![Storage basics](screenshots/09-storage-basics.png)
Storage account basics: standard, LRS.

![Storage networking](screenshots/10-storage-networking-disabled.png)
Public network access disabled.

![Private endpoint](screenshots/11-create-private-endpoint.png)
Private endpoint targeting Blob in the endpoints subnet.

![Endpoint overview](screenshots/12-private-endpoint-overview.png)
Private endpoint connection approved.

![Endpoint DNS](screenshots/13-private-endpoint-dns.png)
DNS configuration with the storage name mapping to a 10.0.2.x IP.

![Test blob](screenshots/14-test-blob-created.png)
Test blob created in the storage account.

![Blocked from outside](screenshots/15-public-access-blocked.png)
Access from outside the VNet is not authorized.

## Exam notes
- NSG rules: lowest priority number is evaluated first
- NSGs are stateful: replies to allowed traffic are allowed automatically
- A private endpoint needs a private DNS zone linked to the VNet, or the name still resolves to the public IP
- Management-plane actions (list containers) still work with public access disabled; data-plane actions (read blobs) do not
- NSGs attach to subnets or NICs; with both, inbound traffic is checked at the subnet first

## Redeploying
`template.json` is an exported template. Replace `YOUR_PUBLIC_IP` in the RDP rule with your own IP before deploying.

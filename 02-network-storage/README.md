# 02 – Network and Storage

Private network with a locked-down storage account reachable only through a private endpoint.

## 1. What I built
- VNet `vnet-lab` (10.0.0.0/16) in Malaysia West
- Subnets: `snet-resources` (10.0.1.0/24), `snet-privateendpoints` (10.0.2.0/24)
- NSG `nsg-resources` attached to `snet-resources`
- Storage account with public network access disabled
- Private endpoint for Blob, with private DNS zone `privatelink.blob.core.windows.net`
- Exported ARM template: [template.json](template.json)

## 2. Steps
1. Created resource group `rg-lab-network`
2. Created the VNet with two subnets
3. Created the NSG with an RDP rule (my IP only) and a VNet outbound rule, then attached it to the resources subnet
4. Created the storage account with public access disabled
5. Added the private endpoint with DNS integration
6. Verified access from outside the VNet is blocked
7. Exported the ARM template

## 3. Evidence
<img src="screenshots/01-create-resource-group.png" alt="Resource group" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Resource group `rg-lab-network` for the network resources.*

<img src="screenshots/02-vnet-deployed.png" alt="VNet deployed" style="border: 1px solid #d0d7de; border-radius: 6px;">
*`vnet-lab` deployment completed.*

<img src="screenshots/03-vnet-address-space.png" alt="Address space" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Address space 10.0.0.0/16 with the two subnets.*

<img src="screenshots/04-vnet-overview.png" alt="VNet overview" style="border: 1px solid #d0d7de; border-radius: 6px;">
*VNet overview showing both subnets.*

<img src="screenshots/05-create-nsg.png" alt="Create NSG" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Creating `nsg-resources`.*

<img src="screenshots/06-nsg-inbound-rdp.png" alt="NSG inbound" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Inbound RDP allowed from one IP only.*

<img src="screenshots/07-nsg-outbound-vnet.png" alt="NSG outbound" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Outbound allowed within the VNet.*

<img src="screenshots/08-nsg-subnet-association.png" alt="NSG association" style="border: 1px solid #d0d7de; border-radius: 6px;">
*NSG attached to `snet-resources`.*

<img src="screenshots/09-storage-basics.png" alt="Storage basics" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Storage account basics: standard, LRS.*

<img src="screenshots/10-storage-networking-disabled.png" alt="Storage networking" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Public network access disabled.*

<img src="screenshots/11-create-private-endpoint.png" alt="Private endpoint" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Private endpoint targeting Blob in the endpoints subnet.*

<img src="screenshots/12-private-endpoint-overview.png" alt="Endpoint overview" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Private endpoint connection approved.*

<img src="screenshots/13-private-endpoint-dns.png" alt="Endpoint DNS" style="border: 1px solid #d0d7de; border-radius: 6px;">
*DNS configuration with the storage name mapping to a 10.0.2.x IP.*

<img src="screenshots/14-test-blob-created.png" alt="Test blob" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Test blob created in the storage account.*

<img src="screenshots/15-public-access-blocked.png" alt="Blocked from outside" style="border: 1px solid #d0d7de; border-radius: 6px;">
*Access from outside the VNet is not authorized.*

## 4. Exam notes
- NSG rules: lowest priority number is evaluated first
- NSGs are stateful: replies to allowed traffic are allowed automatically
- A private endpoint needs a private DNS zone linked to the VNet, or the name still resolves to the public IP
- Management-plane actions (list containers) still work with public access disabled; data-plane actions (read blobs) do not
- NSGs attach to subnets or NICs; with both, inbound traffic is checked at the subnet first

## 5. Redeploying
`template.json` is an exported template. Replace `YOUR_PUBLIC_IP` with your own IP before deploying: it appears in both the RDP rule source (priority 100) and the SSH rule source (priority 110).

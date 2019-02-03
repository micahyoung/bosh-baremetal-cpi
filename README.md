# bosh-baremetal-cpi
BOSH CPI using PXE and WiFi Smart Plugs to manage bare metal machines

## Prerequistes
* Existing network with DHCP server
* Dedicated machine to host IaaS web app with dedicated URL or IP address
* Optional: compatible network accessible power strip for hard stop, stop, restart commands

## Design
1) Push/run IaaS web-app to known, reachable domain name or IP address
1) Download USB image from web-app and flash to USB
1) Boot baremetal machine from USB
1) Set IaaS app server URL in bosh manifest
1) Run bosh create-env to boot the director machine

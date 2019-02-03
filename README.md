# bosh-baremetal-cpi
BOSH CPI using PXE and WiFi Smart Plugs to manage bare metal machines

## Prerequistes
* Existing private network with DHCP server (NAT optional)
* Dedicated machine to host IaaS web app with dedicated URL or IP address
* Optional: compatible network accessible power strip for hard stop, stop, restart commands

## Design

### User workflow
1) Push/run IaaS web-app to known, reachable domain name or IP address
1) Download USB image from web-app and flash to USB
1) Boot baremetal machine from USB
1) Set IaaS app server URL in bosh manifest
1) Run bosh create-env to boot the director machine

### BOSH CPI
`create_stemcell`
* uploads stemcell to to IaaS
* uploads alternate kernel/initrd to IaaS if needed

`create_vm`
* sends desired-VM metadata to IaaS
  * stemcell
  * cloud properties for initial agent settings
    * hardware specs are ignored
  * desired matching MAC address (potential)
* IaaS app preparse customized payload for next available machine
  * responds with ipxe config file with desired:
    * kernel/initrd
    * root disk from stemcell
    * initial agent settings
    * script to merge agent settings into the root disk
* IaaS app record VM ID status to `booting`
* next machine to poll IaaS app gets the ipxe config and boots
* machine is configured successfully
* machine's bosh agent connects to bosh registry

`update_vm_metadata`
* calls IaaS app to update status
* IaaS app records VM ID status as `running`

`delete_vm`
* (needs research)
* (?) does bosh agent receive `delete_vm` notice?
* (?) assuming power strip, how did we capture what VM ID lives on which strip outlet?
  * assume strip order based order of VM creation?
  * correlate based on controlled booting?
  * require MAC/Power port mapping in BOSH manifest?

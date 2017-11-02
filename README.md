# Tutorial: VIC Installation



## Introduction

 

This blog covers VIC 1.2.1 Installation for lab or production environment.

Following aspects of the installation will be detailed in this tutorial:

* VIC – Product versions & Pre-Requisites
* VIC - Deployment Options
* VIC – Installation and Consumption
* Install VIC VM (by deploying VIC OVA)
* Provision a VCH Instance
* Delete a VCH Instance

---


## Terminology


VIC uses several terminologies which are important to understand from the beginning.

**VIC** (vSphere Integrated Container) will be deployed as a VM from an OVA.
VIC VM will host essentials services like the Harbor private registry and Admiral container management.
VIC VM will host also the vic-machine utility that enables administrators to deploy **VCH** (Virtual Container Hosts).
VCH will be instantiated as a VM under an automatically created resource pool in the Compute cluster.
VCH behaves as a Docker endpoint where developer can target their system to in order to run Containers as VM (C-VM) on the ESXi cluster, up to the limit imposed by the resource pool.

Multiple VCH Instances can be deployed in the same vSphere environment.

---


## VIC – Product versions & Pre-Requisites

 

__Product versions__:

* ESXi: 6.0 or 6.5 (6.5 recommended)
* vCenter: 6.0 or 6.5 (6.5 recommended)
* VIC: 1.2.1

ESXi, vCenter and VIC are all available for download on vmware.com.

In term of storage, VIC can operate with any type of datastore: VMFS, shared storage like NFS or iSCSI and vSAN.

 

 

__Environment Pre-Requisites__:

* vSphere Enterprise Plus License
* User with administrative credentials to vCenter
* At least one Compute cluster with DRS enabled
* Internet Access for VCH to download images from Docker Hub
* Shared datastores to store VCH and containers (recommended minimum of 200Gb)
* Allow communication on port 443 and 2377 between VCH and ESXi hosts
* VCH needs to be reachable by Docker users for containers creation
* VIC VM needs to be reachable by any admin user who needs to download the vic-machine utility
* VIC VM needs to be reachable by Docker users for image upload and download (VIC VM hosts container registry)
* VIC VM needs to be reachable by VCH for container instantiation using a local image
* DVS switch with at least 2 Port Groups:
        1 for for public communication (VCH to external world)
        1 for for inter containers communication (recommended dedicated port group for each VCH)
        Note: If DHCP is not available on these segments, please, request a range of free IP Addresses.

 

DVS configuration used for this lab looks like this:


<img src="images/image1.png" width="100%">



VM-RegionA01-vDS-COMP Port-Group will be used for public communication (to connect VCH to external world). 
This Port-Group hosts a DHCP server that will dynamically allocate IP address to VCH.

Bridge01-RegionA01-vDS-COMP will be used for inter containers communication.


---


# VIC  Deployment Options


## __Option 1__:
<img src="images/image2.png" width="80%">
 
 2 types of cluster will be deployed:
 * Management Cluster: hosts management plane components (like vCenter and VIC VM).
 * Compute Cluster: hosts compute workloads (especially VCH – Virtual Container Host and Containers as VM).
 
 DVS switch will be used as virtual networking component to switch C-VM (Container as VM) and VCH traffic.
 DRS and HA will be activated on these clusters.
 
 
## __Option 2__:
<img src="images/image3.png" width="80%">
 
 2 types of cluster will be deployed:
 * Management Cluster: hosts management plane components (like vCenter and VIC VM).
 * Compute Cluster: hosts compute workloads (especially VCH – Virtual Container Host and Containers as VM).
 
 NSX will be used as virtual networking component to switch C-VM (Container as VM) and VCH traffic.
 DRS and HA will be activated on these clusters.
 
 
## __Option 3__:
 <img src="images/image4.png" width="80%">
 
 1 type of cluster will be deployed:
 * Collapsed Compute & Management Cluster:
  hosts management plane components (like vCenter and VIC VM).
  hosts compute workloads (especially VCH – Virtual Container Host and Containers as VM).
 
 DVS switch will be used as virtual networking component to switch C-VM (Container as VM) and VCH traffic.
 DRS and HA will be activated on the collapsed cluster.


 For the purpose of this blog, we are going to use option 3 as deployment example.


---


# VIC – Installation and Consumption


__Install VIC VM (by deploying VIC OVA)__

 

Download VIC 1.2.1 from vmware.com (file should be named 'vic-v1.2.1-4104e5f9.ova')

on vCenter, select the compute cluster → Actions → Deploy OVF Template:

<img src="images/image5.png" width="70%">
 
Note: as you can see on this ESXi cluster, we have already deployed a VM named 'docker client photon OS'.
This VM is intended to run the vic-machine utility and to run docker commands against VCH.

 

Select Local file and click on Browse. Select the VIC OVA from the local directory:

<img src="images/image6.png" width="70%">
 
Click on Next.


Rename the VIC VM if needed:

<img src="images/image7.png" width="70%">
 
Click on Next.

 
Select the cluster where VIC VM will be installed:

<img src="images/image8.png" width="70%">
 
Click on Next.

 
Review details:

<img src="images/image9.png" width="70%">

Click on Next.

 

Accept license agreements:

<img src="images/image10.png" width="70%">

Click on Next.

 
Select disk format and datastore:

<img src="images/image11.png" width="70%">

Click on Next.

 
Select networks Port-Group:

<img src="images/image12.png" width="70%">


Click on Next.

 
Enter root password.

Specify VIC VM network attributes:

<img src="images/image13.png" width="70%">


use default parameters for Registry configuration:

<img src="images/image14.png" width="70%">


use default parameters for Management Portal configuration:

<img src="images/image15.png" width="70%">


use default parameters for File Server configuration:

<img src="images/image16.png" width="70%">


use default parameters for Demo VCH installer wizard configuration:

<img src="images/image17.png" width="70%">


use default parameters for Example Users configuration:

<img src="images/image18.png" width="70%">


Click on Next.

 
Review all parameters:

<img src="images/image19.png" width="70%">


Click on Finish to deploy VIC VM.

 
At the end of the deployment, you should be able to see VIC VM in the Hosts and Cluster frame as shown below:

<img src="images/image20.png" width="70%">


Power on the VM. The console window of the VM should display this output:

<img src="images/image21.png" width="70%">


Open a web browser and go to the URL https://<VIC VM IP>:9443 (https://192.168.100.21:9443/ for this lab)

 
The following window will appear. Fill the fields (vCenter information and credentials) as required:

<img src="images/image22.png" width="30%">

Click on Continue.

 
A message will appear with information about the installation:

<img src="images/image23.png" width="30%">


Once completed, you should be able to see this window:

<img src="images/image24.png" width="100%">

This confirms the installation of VIC is successful!



__Download vic-machine utility__

 

On the VM dedicated to host the vic-machine utility (docker client photon OS VM in our case), create a directory for this purpose.

 
```
root@docker-client [ ~/ ]# mkdir -p DATA/VIC
root@docker-client [ ~/ ]# cd DATA/VIC
root@docker-client [ ~/DATA/VIC ]# pwd
/root/DATA/VIC
```

 


```
root@docker-client [ ~/DATA/VIC ]# wget https://192.168.100.21:9443/files/vic_1.2.1.tar.gz --no-check-certificate

root@docker-client [ ~/DATA/VIC ]# ls
vic_1.2.1.tar.gz
```
 

```
root@docker-client [ ~/DATA/VIC ]# gunzip vic_1.2.1.tar.gz
root@docker-client [ ~/DATA/VIC ]# tar xvf vic_1.2.1.tar
root@docker-client [ ~/DATA/VIC ]# ls
vic  vic_1.2.1.tar
```
 
```
root@docker-client [ ~/DATA/VIC ]# cd vic
root@docker-client [ ~/DATA/VIC/vic ]# ls
LICENSE        bootstrap.iso       vic-machine-linux        vic-ui-linux
README         ui                  vic-machine-windows.exe  vic-ui-windows.exe
appliance.iso  vic-machine-darwin  vic-ui-darwin
```
 

__Update ESXi FW rules to allow VCH to communicate with ESXi hosts__:

 

Issue this first command to retrieve vCenter thumbprint:

```
root@docker-client [ ~/DATA/VIC/vic ]# ./vic-machine-linux update firewall --target vcsa-01a.corp.local --user administrator@vsphere.local --password VMware1! --compute-resource RegionA01-COMP01  --allow

    Oct 30 2017 20:09:10.027Z INFO  ### Updating Firewall ####
    Oct 30 2017 20:09:10.095Z ERROR Failed to verify certificate for target=vcsa-01a.corp.local (thumbprint=F7:68:F0:93:F4:EC:B7:FE:C1:02:3F:F3:AB:62:1A:50:E8:9A:0E:85)
    Oct 30 2017 20:09:10.095Z ERROR Update cannot continue - failed to create validator: x509: certificate signed by unknown authority
    Oct 30 2017 20:09:10.095Z ERROR --------------------
    Oct 30 2017 20:09:10.095Z ERROR vic-machine-linux update firewall failed: update firewall failed
```
 

 

Re-issue the command now adding vCenter thumbprint:

```
root@docker-client [ ~/DATA/VIC/vic ]# ./vic-machine-linux update firewall --target vcsa-01a.corp.local --user administrator@vsphere.local --password VMware1! --compute-resource RegionA01-COMP01  --allow --thumbprint F7:68:F0:93:F4:EC:B7:FE:C1:02:3F:F3:AB:62:1A:50:E8:9A:0E:85

    Oct 30 2017 20:11:10.527Z INFO  ### Updating Firewall ####
    Oct 30 2017 20:11:10.749Z INFO  Validating target
    Oct 30 2017 20:11:10.749Z INFO  Validating compute resource
    Oct 30 2017 20:11:10.776Z INFO
    Oct 30 2017 20:11:10.777Z WARN  ### WARNING ###
    Oct 30 2017 20:11:10.777Z WARN          This command modifies the host firewall on the target machine or cluster
    Oct 30 2017 20:11:10.777Z WARN          The ruleset "vSPC" will be enabled
    Oct 30 2017 20:11:10.777Z WARN          This allows all outbound TCP traffic from the target
    Oct 30 2017 20:11:10.778Z WARN          To undo this modification use --deny
    Oct 30 2017 20:11:10.778Z INFO
    Oct 30 2017 20:11:10.930Z INFO  Ruleset "vSPC" enabled on host "HostSystem:host-29 @ /RegionA01/host/RegionA01-COMP01/esx-01a.corp.local"
    Oct 30 2017 20:11:11.046Z INFO  Ruleset "vSPC" enabled on host "HostSystem:host-31 @ /RegionA01/host/RegionA01-COMP01/esx-02a.corp.local"
    Oct 30 2017 20:11:11.170Z INFO  Ruleset "vSPC" enabled on host "HostSystem:host-32 @ /RegionA01/host/RegionA01-COMP01/esx-03a.corp.local"
    Oct 30 2017 20:11:11.170Z INFO
    Oct 30 2017 20:11:11.170Z INFO  Firewall changes complete
    Oct 30 2017 20:11:11.171Z INFO  Command completed successfully
```

 

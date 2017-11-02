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

Go into the directory and issue the following commands:

 
```
root@docker-client [ ~/DATA/VIC ]# pwd
/root/DATA/VIC
```

 


```
root@docker-client [ ~/DATA/VIC ]# wget https://192.168.100.21:9443/files/vic_1.2.1.tar.gz --no-check-certificate
--2017-10-30 19:53:29--  https://192.168.100.21:9443/files/vic_1.2.1.tar.gz
Connecting to 192.168.100.21:9443... connected.
WARNING: cannot verify 192.168.100.21's certificate, issued by 'CN=Self-signed by VMware\\, Inc.,OU=Containers on vSphere,O=VMware\\, Inc.,L=Palo Alto,ST=California,C=US':
Unable to locally verify the issuer's authority.
HTTP request sent, awaiting response... 200 OK
Length: 247859076 (236M) [application/x-gzip]
Saving to: 'vic_1.2.1.tar.gz'

vic_1.2.1.tar.gz    100%[===================>] 236.38M  81.5MB/s    in 2.9s

2017-10-30 19:53:32 (81.5 MB/s) - 'vic_1.2.1.tar.gz' saved [247859076/247859076]
```
 

 
```
root@docker-client [ ~/DATA/VIC ]# ls
vic_1.2.1.tar.gz
```
 

root@docker-client [ ~/DATA/VIC ]# gunzip vic_1.2.1.tar.gz


root@docker-client [ ~/DATA/VIC ]# ls

vic_1.2.1.tar

 

 

root@docker-client [ ~/DATA/VIC ]# tar xvf vic_1.2.1.tar

vic/

vic/README

vic/vic-machine-darwin

vic/appliance.iso

<SNIP>

 

root@docker-client [ ~/DATA/VIC ]# ls

vic  vic_1.2.1.tar

 

root@docker-client [ ~/DATA/VIC ]# cd vic

root@docker-client [ ~/DATA/VIC/vic ]# ls

LICENSE        bootstrap.iso       vic-machine-linux        vic-ui-linux

README         ui                  vic-machine-windows.exe  vic-ui-windows.exe

appliance.iso  vic-machine-darwin  vic-ui-darwin

 

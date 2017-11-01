# VIC-TUTORIAL



## Introduction

 

This blog covers VIC 1.2.1 Installation for lab or production environment.

Following aspects of the installation will be detailed in this tutorial:

* VIC – Product versions & Pre-Requisites
* VIC - Deployment Options
* VIC – Installation and Consumption
* Install VIC VM (by deploying VIC OVA)
* Provision a VCH Instance
* Delete a VCH Instance

 

## Terminology


VIC uses several terminologies which are important to understand from the beginning.

**VIC** (vSphere Integrated Container) will be deployed as a VM from an OVA.
VIC VM will host essentials services like the Harbor private registry and Admiral container management.
VIC VM will host also the vic-machine utility that enables administrators to deploy **VCH** (Virtual Container Hosts).
VCH will be instantiated as a VM under an automatically created resource pool in the Compute cluster.
VCH behaves as a Docker endpoint where developer can target their system to in order to run Containers as VM (C-VM) on the ESXi cluster, up to the limit imposed by the resource pool.

Multiple VCH Instances can be deployed in the same vSphere environment.

 

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



VM-RegionA01-vDS-COMP Port-Group will be used for public communication (to connect VCH to external world). This Port-Group hosts a DHCP server that will dynamically allocate IP address to VCH.

Bridge01-RegionA01-vDS-COMP will be used for inter containers communication.


# VIC  Deployment Options



 * __Option 1__
 
<img src="images/image2.png" width="80%">
 
 2 types of cluster will be deployed:

    Management Cluster: hosts management plane components (like vCenter and VIC VM).
    Compute Cluster: hosts compute workloads (especially VCH – Virtual Container Host and Containers as VM).

DVS switch will be used as virtual networking component to switch C-VM (Container as VM) and VCH traffic.

DRS and HA will be activated on these clusters.



* __Option 2__

<img src="images/image3.png" width="80%">
 
 2 types of cluster will be deployed:

    Management Cluster: hosts management plane components (like vCenter and VIC VM).
    Compute Cluster: hosts compute workloads (especially VCH – Virtual Container Host and Containers as VM).

NSX will be used as virtual networking component to switch C-VM (Container as VM) and VCH traffic.

DRS and HA will be activated on these clusters.



* __Option 3__

<img src="images/image4.png" width="80%">
 1 type of cluster will be deployed:

    Collapsed Compute & Management Cluster:
        hosts management plane components (like vCenter and VIC VM).
        hosts compute workloads (especially VCH – Virtual Container Host and Containers as VM).

DVS switch will be used as virtual networking component to switch C-VM (Container as VM) and VCH traffic.

DRS and HA will be activated on the collapsed cluster.

 

For the purpose of this blog, we are going to use option 3 as deployment example.



# VIC – Installation and Consumption


__Install VIC VM (by deploying VIC OVA)__

 

Download VIC 1.2.1 from vmware.com (file should be named 'vic-v1.2.1-4104e5f9.ova')

on vCenter, select the compute cluster → Actions → Deploy OVF Template:
 ![](images/image5.png)
 
Note: as you can see on this ESXi cluster, we have already deployed a VM named 'docker client photon OS'.

The purposes of this VM are:

* run the vic-machine utility
* run docker commands against VCH

 

Select Local file and click on Browse. Select the VIC OVA from the local directory.
 ![](images/image6.png)
 
 Click on Next.

 

Rename the VIC VM if needed.
 ![](images/image7.png)
 
 Click on Next.

 

Select the cluster where VIC VM will be installed.
 ![](images/image8.png)
 
 
 Click on Next.

 

Review details.
![](images/image9.png)

Click on Next.

 

Accept license agreements.
![](images/image10.png)


Click on Next.

 

Select disk format and datastore.
![](images/image11.png)

Click on Next.

 

Select networks Port-Group.
![](images/image12.png)


Click on Next.

 

Enter root password.

Specify VIC VM network attributes.
![](images/image13.png)


use default parameters for Registry configuration:
![](images/image14.png)


use default parameters for Management Portal configuration:
![](images/image15.png)


use default parameters for File Server configuration:
![](images/image16.png)


use default parameters for Demo VCH installer wizard configuration:
![](images/image17.png)


use default parameters for Example Users configuration:
![](images/image18.png)


Click on Next.

 

Review all parameters:
![](images/image19.png)


Click on Finish to deploy VIC VM.

 

 

At the end of the deployment, you should be able to see VIC VM in the Hosts and Cluster frame as shown below:
![](images/image20.png)


Power on the VM. The console window of the VM should display this output:
![](images/image21.png)


Open a web browser and go to the URL https://<VIC VM IP>:9443 (https://192.168.100.21:9443/ for this lab)

 

The following window will appear. Fill the fields (vCenter information and credentials) as required.
![](images/image22.png)


Click on Continue.

 

A message will appear with information about the installation:
![](images/image23.png)


Once completed, you should be able to see this window:
![](images/image24.png)

It confirms the installation of VIC is successful!





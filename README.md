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

![](images/image1.png)


VM-RegionA01-vDS-COMP Port-Group will be used for public communication (to connect VCH to external world). This Port-Group hosts a DHCP server that will dynamically allocate IP address to VCH.

Bridge01-RegionA01-vDS-COMP will be used for inter containers communication.


# VIC  Deployment Options



 * __Option 1__
 
 ![](images/image2.png)
 
 2 types of cluster will be deployed:

    Management Cluster: hosts management plane components (like vCenter and VIC VM).
    Compute Cluster: hosts compute workloads (especially VCH – Virtual Container Host and Containers as VM).

DVS switch will be used as virtual networking component to switch C-VM (Container as VM) and VCH traffic.

DRS and HA will be activated on these clusters.



* __Option 2__

 ![](images/image3.png)
 
 2 types of cluster will be deployed:

    Management Cluster: hosts management plane components (like vCenter and VIC VM).
    Compute Cluster: hosts compute workloads (especially VCH – Virtual Container Host and Containers as VM).

NSX will be used as virtual networking component to switch C-VM (Container as VM) and VCH traffic.

DRS and HA will be activated on these clusters.



* __Option 3__

 ![](images/image4.png)
 1 type of cluster will be deployed:

    Collapsed Compute & Management Cluster:
        hosts management plane components (like vCenter and VIC VM).
        hosts compute workloads (especially VCH – Virtual Container Host and Containers as VM).

DVS switch will be used as virtual networking component to switch C-VM (Container as VM) and VCH traffic.

DRS and HA will be activated on the collapsed cluster.

 

For the purpose of this blog, we are going to use option 3 as deployment example.


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

VIC (vSphere Integrated Container) will be deployed as a VM from an OVA.
VIC VM will host essentials services like the Harbor private registry and Admiral container management.
VIC VM will host also the vic-machine utility that enables administrators to deploy VCH (Virtual Container Hosts).
VCH will be instantiated as a VM under an automatically created resource pool in the Compute cluster.
VCH behaves as a Docker endpoint where developer can target their system to in order to run Containers as VM (C-VM) on the ESXi cluster, up to the limit imposed by the resource pool.

Multiple VCH Instances can be deployed in the same vSphere environment.

 

## VIC – Product versions & Pre-Requisites

 

Product versions:

* ESXi: 6.0 or 6.5 (6.5 recommended)
* vCenter: 6.0 or 6.5 (6.5 recommended)
* VIC: 1.2.1

ESXi, vCenter and VIC are all available for download on vmware.com.

In term of storage, VIC can operate with any type of datastore: VMFS, shared storage like NFS or iSCSI and vSAN.

 

 

Environment Pre-Requisites:

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

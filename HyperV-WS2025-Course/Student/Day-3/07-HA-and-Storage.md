# Module 07 — High Availability and Advanced Storage

## Introduction

Recovery and availability depend heavily on the storage architecture underneath Hyper-V. This module broadens the view from local VHDX files to shared and enterprise designs, and separates migration, replication, and clustering roles.

## Goal

Compare DR, migration, HA and storage architectures.

## Concepts to keep in mind

Live Migration moves compute, Storage Migration moves files, Replica maintains a recovery copy, and Failover Clustering provides coordinated high availability. Local, SMB, SAN, CSV, and S2D designs have different failure domains and operational models.

## What you will do

Compare each technology against maintenance, DR, HA, performance, and storage-failure scenarios and map where VM configuration and disks live in each design.

## Hands-on / detailed content

| Technology | Typical use |
|---|---|
| Live Migration | Move running compute |
| Storage Migration | Move VM files |
| Hyper-V Replica | Maintain a DR copy |
| Failover Clustering | High availability |

## Storage architectures

Discuss:

- local host storage;
- SMB 3;
- SAN/block storage;
- Cluster Shared Volumes;
- Storage Spaces Direct.

Evaluate capacity, latency, throughput, resiliency, backup, growth, failure domains and operational complexity.

## Validation

Explain why the simple local storage used in this lab is useful for training but does not provide clustered high availability.

## Mini-lab — HA readiness assessment

This is a hands-on **readiness** lab, not a cluster-build lab.

Microsoft documents that nested virtualization is not suitable for Windows Server Failover Clustering, so the course deliberately stops before `New-Cluster`.

### 1. Install the feature for inspection

On HV01 and HV02:

~~~powershell
Install-WindowsFeature Failover-Clustering -IncludeManagementTools
Get-WindowsFeature Failover-Clustering
Get-Command -Module FailoverClusters | Select-Object -First 10 Name
~~~

Installing the feature does not create a cluster.

### 2. Verify host identity and reachability

Confirm the full computer names are `hv01.lab.local` and `hv02.lab.local`, with membership remaining WORKGROUP.

From HV01:

~~~powershell
Test-Connection hv02.lab.local -Count 2
~~~

From HV02:

~~~powershell
Test-Connection hv01.lab.local -Count 2
~~~

### 3. Compare Hyper-V host configuration

~~~powershell
Get-VMHost | Select-Object VirtualMachinePath,VirtualHardDiskPath,LogicalProcessorCount
Get-VMSwitch | Select-Object Name,SwitchType
~~~

Confirm that course conventions are consistent across the two hosts.

### 4. Inspect storage

~~~powershell
Get-Disk
Get-Volume
~~~

Both hosts may have `D:\Hyper-V`, but those D: volumes are separate local virtual disks. Matching paths do **not** make storage shared.

### 5. Complete the requirement gap table

| Requirement | Current lab | Result |
|---|---|---|
| Two Hyper-V hosts | HV01/HV02 | Present |
| Stable FQDNs | hv01.lab.local / hv02.lab.local | Present |
| Failover Clustering feature | Installed for inspection | Present |
| Node connectivity | Outer management network | Lab-level |
| Matching switch convention | vSW-Lab | Present |
| Shared/coordinated VM storage | Separate local D: disks | Missing |
| Quorum/witness | Not configured | Missing |
| Full cluster validation | Not performed | Missing |
| Suitable underlying platform for WSFC | Nested Hyper-V | Missing |

### 6. Short student conclusion

Write five to ten lines explaining what would have to change before this topology could become a real highly available Hyper-V design.

Your answer should mention supported infrastructure, shared/coordinated storage, cluster validation, quorum/witness, network design, and operational monitoring.

### Hard stop

Do **not** run `New-Cluster` in this course environment.

## Future lab reference — supported workgroup-cluster progression

> **Reference only for a future supported lab. Do not run this sequence on the current nested HV01/HV02 course machines.**

When you later have two supported non-nested Windows Server 2025 Hyper-V hosts plus supported shared/coordinated storage, use this progression as a starting baseline.

### 1. Prepare workgroup-cluster identity and trust

- same Windows Server version;
- both nodes in WORKGROUP;
- common primary DNS suffix;
- working forward name resolution;
- same local administrator username/password on both nodes;
- appropriate WinRM TrustedHosts configuration.

Microsoft also documents `LocalAccountTokenFilterPolicy` when a nonbuilt-in local administrator is used for remote administration.

### 2. Install Failover Clustering

~~~powershell
Install-WindowsFeature Failover-Clustering -IncludeManagementTools
~~~

### 3. Provide supported shared/coordinated storage

Use SAN/shared block, SMB 3, or Storage Spaces Direct when its hardware requirements are met.

The current course's separate local D: disks are not sufficient.

### 4. Validate the future hosts

~~~powershell
Test-Cluster -Node hv01.lab.local,hv02.lab.local
~~~

Review the validation report before cluster creation.

### 5. Create the workgroup cluster

Example baseline:

~~~powershell
$Cred1 = Get-Credential -UserName "HV01\ClusterAdmin"
$Cred2 = Get-Credential -UserName "HV02\ClusterAdmin"

New-WorkgroupCluster `
    -Name "HVCL01" `
    -Node "hv01.lab.local","hv02.lab.local" `
    -Credentials $Cred1,$Cred2 `
    -StaticAddress "<cluster-management-IP>" `
    -NoStorage
~~~

### 6. Verify cluster state

~~~powershell
Get-Cluster
Get-ClusterNode
Get-ClusterNetwork
Get-ClusterQuorum
~~~

### 7. Configure a witness

A two-node cluster should have a witness appropriate to the design. Use a supported cloud, disk, or file-share witness and verify with:

~~~powershell
Get-ClusterQuorum
~~~

### 8. Add shared storage / CSV where appropriate

~~~powershell
Get-ClusterAvailableDisk
Add-ClusterDisk -Name "<Available Cluster Disk Name>"
Add-ClusterSharedVolume -Name "<Cluster Disk Name>"
Get-ClusterSharedVolume
~~~

### 9. Put the VM on cluster-accessible storage

For CSV-backed storage, VM files normally live below a path such as:

~~~text
C:\ClusterStorage\Volume1\VMs\SRV01
~~~

### 10. Make the VM highly available

~~~powershell
Add-ClusterVirtualMachineRole -VMName "SRV01"
~~~

Then verify clustered roles/resources and perform controlled movement/failover testing only after the future cluster has passed validation.

### Why this stays reference-only today

The current course topology lacks the supported physical/non-nested platform, shared/coordinated VM storage, quorum/witness design, full cluster validation, and cluster management configuration required for a real HA implementation.
## What you should observe

A technology can solve one problem while leaving another untouched: for example, Live Migration does not create a DR copy and local storage does not provide clustered resiliency.

## Validation checkpoint

Given a customer requirement, choose an appropriate migration/recovery/storage approach and state its main limitation. Also explain at least three reasons the current nested HV01/HV02 topology is not a production HA cluster.

## Expected end state

You can reason about Hyper-V availability and storage designs, identify the missing prerequisites for true HA, and avoid treating a nested training topology as a supported production failover cluster.

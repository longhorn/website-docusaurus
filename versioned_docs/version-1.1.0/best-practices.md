---
title: Best Practices
sidebar_position: 5
---

We recommend the following setup for deploying Longhorn in production.


## Minimum Recommended Hardware

- 3 nodes
- 4 vCPUs per node
- 4 GiB per node
- SSD/NVMe or similar performance block device on the node for storage
    - We don't recommend using spinning disks with Longhorn, due to low IOPS.

## Operating System

The below Linux OS distributions and versions have been verified during the v[[< current-version >]] release testing, but it does not mean Longhorn only supports them. Basically,
Longhorn should work well on any certified Kubernetes cluster running on Linux nodes with a most general-purpose operating system as below examples.

1. Ubuntu 18.04
1. CentOS 7/8

### Unsupported Operating System

Non-General Purpose OS or Container-Optimized OS due to lacking package manager or immutable system limitation.

## Node and Disk Setup

We recommend the following setup for nodes and disks.

### Use a Dedicated Disk

It's recommended to dedicate a disk for Longhorn storage for production, instead of using the root disk.

### Minimal Available Storage and Over-provisioning

If you need to use the root disk, use the default `minimal available storage percentage` setup which is 25%, and set `overprovisioning percentage` to 200% to minimize the chance of DiskPressure.

If you're using a dedicated disk for Longhorn, you can lower the setting `minimal available storage percentage` to 10%.

For the Over-provisioning percentage, it depends on how much space your volume uses on average. For example, if your workload only uses half of the available volume size, you can set the Over-provisioning percentage to `200`, which means Longhorn will consider the disk to have twice the schedulable size as its full size minus the reserved space.

### Disk Space Management

Since Longhorn doesn't currently support sharding between the different disks, we recommend using [LVM](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)) to aggregate all the disks for Longhorn into a single partition, so it can be easily extended in the future.

### Setting up Extra Disks

Any extra disks must be written in the `/etc/fstab` file to allow automatic mounting after the machine reboots.

Don't use a symbolic link for the extra disks. Use `mount --bind` instead of `ln -s` and make sure it's in the `fstab` file. For details, see [the section about multiple disk support.](./volumes-and-nodes/multidisk#use-an-alternative-path-for-a-disk-on-the-node)

## Configuring Default Disks Before and After Installation

To use a directory other than the default `/var/lib/longhorn` for storage, the `Default Data Path` setting can be changed before installing the system. For details on changing pre-installation settings, refer to [this section.](./advanced-resources/deploy/customizing-default-settings)

The [Default node/disk configuration](./advanced-resources/default-disk-and-node-config) feature can be used to customize the default disk after installation. Customizing the default configurations for disks and nodes is useful for scaling the cluster because it eliminates the need to configure Longhorn manually for each new node if the node contains more than one disk, or if the disk configuration is different for new nodes. Remember to enable `Create default disk only on labeled node` if applicable.

## Deploying Workloads

If you're using `ext4` as the filesystem of the volume, we recommend adding a liveness check to workloads to help automatically recover from a network-caused interruption, a node reboot, or a Docker restart. See [this section](./high-availability/recover-volume/) for details.

## Volume Maintenance

We highly recommend using the built-in backup feature of Longhorn.

For each volume, schedule at least one recurring backup. If you must run Longhorn in production without a backupstore, then schedule at least one recurring snapshot for each volume.

Longhorn system will create snapshots automatically when rebuilding a replica. Recurring snapshots or backups can also automatically clean up the system-generated snapshot.

## Guaranteed Engine CPU

We recommend allowing Longhorn Engine to have guaranteed CPU allocation. The value is how many CPUs should be reserved for each Engine/Replica Instance Manager Pod created by Longhorn. By default, the value is 0.25 CPUs.

In order to prevent unexpected volume crash, you can use the following formula to calculate an appropriate value for this setting:
```
Number of vCPUs = The estimated max Longhorn volume/replica count on a node * 0.1
```
The result of above calculation doesn't mean that's the maximum CPU resources the Longhorn workloads require. To fully exploit the Longhorn volume I/O performance, you can allocate/guarantee more CPU resources via this setting.

If it's hard to estimate the volume/replica count now, you can leave it with the default value, or allocate 1/8 of total CPU of a node. Then you can tune it when there is no running workload using Longhorn volumes.

For details, refer to the [settings reference.](./references/settings#guaranteed-engine-cpu)

## StorageClass

We don't recommend modifying the default StorageClass named `longhorn`, since the change of parameters might cause issues during an upgrade later. If you want to change the parameters set in the StorageClass, you can create a new StorageClass by referring to the [StorageClass examples](./references/examples#storageclass).

## Scheduling Settings

### Replica Node Level Soft Anti-Affinity
> Recommend: `false`

This setting should be set to `false` in production environment to ensure the best availability of the volume. Otherwise, one node down event may bring down more than one replicas of a volume.

### Allow Volume Creation with Degraded Availability
> Recommend: `false`

This setting should be set to `false` in production environment to ensure every volume have the best availability when created. Because with the setting set to `true`, the volume creation won't error out even there is only enough room to schedule one replica. So there is a risk that the cluster is running out of the spaces but the user won't be made aware immediately.

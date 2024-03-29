---
title: The Longhorn Documentation
description: Cloud native distributed block storage for Kubernetes
sidebar_position: 1
---

**Longhorn** is a lightweight, reliable, and powerful distributed [block storage](https://cloudacademy.com/blog/object-storage-block-storage/) system for Kubernetes.

Longhorn implements distributed block storage using containers and microservices. Longhorn creates a dedicated storage controller for each block device volume and synchronously replicates the volume across multiple replicas stored on multiple nodes. The storage controller and replicas are themselves orchestrated using Kubernetes.

## Features

* Enterprise-grade distributed block storage with no single point of failure
* Incremental snapshot of block storage
* Backup to secondary storage ([NFS](https://www.extrahop.com/resources/protocols/nfs/) or [S3](https://aws.amazon.com/s3/)-compatible object storage) built on efficient change block detection
* Recurring [snapshot and backup](concepts#24-snapshots)
* Automated, non-disruptive [upgrades](install/upgrades). You can upgrade the entire Longhorn software stack without disrupting running storage volumes.]
* An intuitive GUI dashboard

## Current status

Longhorn is beta-quality software. We appreciate your willingness to deploy Longhorn and provide feedback.

The latest release of Longhorn is **v0.8.0**.

## Source code

Longhorn is 100% open source software under the auspices of the [Cloud Native Computing Foundation](https://cncf.io). The project's source code is spread across a number of repos:

| Component                 | What it does                                                           | GitHub repo                                                                                 |
| :------------------------ | :--------------------------------------------------------------------- | :------------------------------------------------------------------------------------------ |
| Longhorn Engine           | Core controller/replica logic                                          | [longhorn/longhorn-engine](https://github.com/longhorn/longhorn-engine)                     |
| Longhorn Instance Manager | Controller/replica instance lifecycle management                       | [longhorn/longhorn-instance-manager](https://github.com/longhorn/longhorn-instance-manager) |
| Longhorn Share Manager    | NFS provisioner that exposes Longhorn volumes as ReadWriteMany volumes | [longhorn/longhorn-share-manager](https://github.com/longhorn/longhorn-share-manager)       |
| Longhorn Manager          | Longhorn orchestration, includes CSI driver for Kubernetes             | [longhorn/longhorn-manager](https://github.com/longhorn/longhorn-manager)                   |
| Longhorn UI               | The Longhorn dashboard                                                 | [longhorn/longhorn-ui](https://github.com/longhorn/longhorn-ui)                             |

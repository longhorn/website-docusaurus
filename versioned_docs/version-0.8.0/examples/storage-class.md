---
title: StorageClass
sidebar_position: 8
---


```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: longhorn
provisioner: driver.longhorn.io
allowVolumeExpansion: true
parameters:
  numberOfReplicas: "3"
  staleReplicaTimeout: "2880" # 48 hours in minutes
  fromBackup: ""
#  diskSelector: "ssd,fast"
#  nodeSelector: "storage,fast"
#  recurringJobs: '[{"name":"snap", "task":"snapshot", "cron":"*/1 * * * *", "retain":1},
#                   {"name":"backup", "task":"backup", "cron":"*/2 * * * *", "retain":1,
#                    "labels": {"interval":"2m"}}]'
```
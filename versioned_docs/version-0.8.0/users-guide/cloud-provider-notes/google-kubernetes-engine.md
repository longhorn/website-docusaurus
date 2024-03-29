---
title: Google Kubernetes Engine
description: Additional configuration required to operate Longhorn in Google Kubernetes Engine.
sidebar_position: 37
---

1. GKE clusters must use `Ubuntu` OS instead of `Container-Optimized` OS, in order to satisfy Longhorn `open-iscsi` dependency.

2. GKE requires a user to manually claim themselves as cluster admin to enable RBAC. Before installing Longhorn, run the following command:

```shell
kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=<name@example.com>
```

where `name@example.com` is the user's account name in GCE.  It's case sensitive. See [this document](https://cloud.google.com/kubernetes-engine/docs/how-to/role-based-access-control) for more information.

---
title: Troubleshooting
sidebar_position: 58
---

You can click `Generate Support Bundle` link at the bottom of the UI to download a zip file contains Longhorn related configuration and logs.

## Common issues
### Volume can be attached/detached from UI, but Kubernetes Pod/StatefulSet etc cannot use it

#### Using with Flexvolume Plugin
Check if volume plugin directory has been set correctly. This is automatically detected unless user explicitly set it. *NOTE* The Flexvoume driver is deprecated as of 0.8.0 and the CSI driver should be used instead (see below)

By default, Kubernetes uses `/usr/libexec/kubernetes/kubelet-plugins/volume/exec/`, as stated in the [official document](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-storage/flexvolume.md#prerequisites).

Some vendors choose to change the directory for various reasons. For example, GKE uses `/home/kubernetes/flexvolume` instead.

User can find the correct directory by running `ps aux|grep kubelet` on the host and check the `--volume-plugin-dir` parameter. If there is none, the default `/usr/libexec/kubernetes/kubelet-plugins/volume/exec/` will be used.

## Troubleshooting guide

There are a few components in the Longhorn. Manager, Engine, Driver and UI. All of those components runnings as pods in the `longhorn-system` namespace by default inside the Kubernetes cluster.

Most of the logs are included in the Support Bundle. You can click Generate Support Bundle link at the bottom of the UI to download a zip file contains Longhorn related configuration and logs.

One exception is the `dmesg`, which need to retrieve by the user on each node.

### UI
Making use of the Longhorn UI is a good place to start troubleshooting. For example, if Kubernetes cannot mount one volume correctly, after stop the workload, try to attach and mount that volume manually on one node and access the content to check if volume is intact.

Also, the event logs in the UI dashboard provides some information of probably issues. Check for the event logs in `Warning` level.

### Rancher UI
Longhorn is made up of a series of microservices, deployed as Kubernetes workloads. If you used the Rancher UI to deploy, you can use the Rancher UI to verify that all the containers for Longhorn are currently deployed. If Kubernetes is having difficulty starting or scheduling these containers, Longhorn may not function properly. You can see the status of all the containers by going to the "Apps" page in the project where you deployed Longorn, and clicking on the "longhorn-system" app. Scroll down to the workloads section to see their status.

If you used another deployment method, you can use `kubectl` to get the status of all the deployed pods.

### Manager and engines
You can get the logs from Longhorn Manager and Engines to help with the troubleshooting. The most useful logs are from `longhorn-manager-xxx`, and the log inside Longhorn instance managers, e.g. `instance-manager-e-xxxx` and `instance-manager-r-xxxx`.

Since normally there are multiple Longhorn Manager running at the same time, we recommend using [kubetail](https://github.com/johanhaleby/kubetail) which is a great tool to keep track of the logs of multiple pods. You can use:
```
kubetail longhorn-manager -n longhorn-system
```
To track the manager logs in real time.

### Linux System Level
Check to make sure the expected storage is available at the longhorn data path (e.g /data/longhorn). If an additional (non-root) volume is used at this location, make sure it is still mounted. Volumes that aren't in `/etc/fstab` will need to be re-mounted after system restart

### CSI driver

For CSI driver, check the logs for `csi-attacher-0` and `csi-provisioner-0`, as well as containers in `longhorn-csi-plugin-xxx`.

### Flexvolume driver

The FlexVolume driver is deprecated as of Longhorn v0.8.0 and should no longer be used.

First check where the driver has been installed on the node. Check the log of `longhorn-driver-deployer-xxxx` for that information.

Then check the kubelet logs. Flexvolume driver itself doesn't run inside the container. It would run along with the kubelet process.

If kubelet is running natively on the node, you can use the following command to get the log:
```
journalctl -u kubelet
```

Or if kubelet is running as a container (e.g. in RKE), use the following command instead:
```
docker logs kubelet
```

For even more detail logs of Longhorn Flexvolume, run following command on the node or inside the container (if kubelet is running as a container, e.g. in RKE):
```
touch /var/log/longhorn_driver.log
```

---
title: Restore to File
sidebar_position: 4
---

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: restore-to-file
  namespace: longhorn-system
spec:
  nodeName: <NODE_NAME>
  containers:
  - name: restore-to-file
    command:
    # set restore-to-file arguments here
    - /bin/sh
    - -c
    - longhorn backup restore-to-file
      '<BACKUP_URL>'
      --output-file '/tmp/restore/<OUTPUT_FILE>'
      --output-format <OUTPUT_FORMAT>
    # the version of longhorn engine should be v0.4.1 or higher
    image: longhorn/longhorn-engine:v0.4.1
    imagePullPolicy: IfNotPresent
    securityContext:
      privileged: true
    volumeMounts:
    - name: disk-directory
      mountPath: /tmp/restore  # the argument <output-file> should be in this directory
    env:
    # set Backup Target Credential Secret here.
    - name: AWS_ACCESS_KEY_ID
      valueFrom:
        secretKeyRef:
          name: <S3_SECRET_NAME>
          key: AWS_ACCESS_KEY_ID
    - name: AWS_SECRET_ACCESS_KEY
      valueFrom:
        secretKeyRef:
          name: <S3_SECRET_NAME>
          key: AWS_SECRET_ACCESS_KEY
    - name: AWS_ENDPOINTS
      valueFrom:
        secretKeyRef:
          name: <S3_SECRET_NAME>
          key: AWS_ENDPOINTS
  volumes:
    # the output file can be found on this host path
    - name: disk-directory
      hostPath:
        path: /tmp/restore
  restartPolicy: Never
```
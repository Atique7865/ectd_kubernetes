# etcd Backup and Version Management Guide

This guide explains how to check the etcd version in your Kubernetes cluster, install etcd locally, create a snapshot backup, and check the snapshot status. Each step includes the necessary bash commands and Bengali explanations.

---

## ✅ Step 1: Check Running etcd Version in Kubernetes Cluster

```bash
kubectl get pods -n kube-system
```

কার্য: Shows all running pods in the kube-system namespace.

```bash
kubectl exec -n kube-system etcd-<your-node-name> -- etcdctl version
```

কার্য: Executes etcdctl version inside the etcd pod.

**Example:**

```bash
kubectl exec -n kube-system etcd-minikube -- etcdctl version
```

---

## ✅ Step 2: Install etcd Locally (If Not Installed)

```bash
wget https://github.com/etcd-io/etcd/releases/download/v3.5.9/etcd-v3.5.9-linux-amd64.tar.gz
```

কার্য: Downloads etcd release package.

```bash
tar -xvf etcd-v3.5.9-linux-amd64.tar.gz
```

কার্য: Extracts the downloaded tar.gz file.

```bash
sudo cp etcd-v3.5.9-linux-amd64/etcd* /usr/local/bin/
```

কার্য: Copies etcd and etcdctl binaries to /usr/local/bin.

---

## ✅ Step 3: Confirm Installation

```bash
etcd --version
etcdctl version
```

কার্য: Confirms the installed version of etcd and etcdctl.

---

## ✅ Step 4: List etcd Cluster Members

```bash
export ENDPOINT=https://192.168.0.200:2380

ETCDCTL_API=3 etcdctl \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--write-out=table member list
```

কার্য: Lists all members of the etcd cluster in table format.

---

## ✅ Step 5: Take etcd Snapshot (Backup)

```bash
sudo ETCDCTL_API=3 etcdctl snapshot save snapshot.db \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key
```

কার্য: Creates a snapshot backup named snapshot.db.

---

## ✅ Step 6: Check Snapshot Status

```bash
sudo ETCDCTL_API=3 etcdctl snapshot status snapshot.db
```

কার্য: Checks the status of the snapshot file.

---

## ⚠ If etcdctl Not Found (Optional Fix)

```bash
sudo cp etcd-v3.5.9-linux-amd64/etcdctl /usr/local/bin/
```

কার্য: Copies etcdctl binary to /usr/local/bin if not already in PATH.

---

## ✅ Step 7: View Snapshot Status in Table Format

```bash
sudo ETCDCTL_API=3 etcdctl --write-out=table snapshot status snapshot.db
```

কার্য: Shows snapshot details (hash, revision, total keys, size) in table format.

---

## 🎉 You're Done!

You have successfully:

* Checked etcd version
* Installed etcd locally
* Created a backup
* Verified backup status

For etcd snapshot restore steps, ask your assistant for the next guide!

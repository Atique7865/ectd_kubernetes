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

## ✅ Step 7: View Snapshot Status in Table Format....

```bash
sudo ETCDCTL_API=3 etcdctl --write-out=table snapshot status snapshot.db
```

কার্য: Shows snapshot details (hash, revision, total keys, size) in table format.

---

# etcd Snapshot Restore Guide (Bangla)

এই গাইডে আপনি শিখবেন কীভাবে একটি etcd snapshot (ব্যাকআপ ফাইল) তৈরি ও তা থেকে Kubernetes cluster পুনরুদ্ধার (restore) করতে হয়।

---

## ✅ Step 0: Take etcd Snapshot (Backup)

```bash
sudo ETCDCTL_API=3 etcdctl snapshot save snapshot.db \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
```

🔹 **কার্য:** বর্তমান ইটিসিডি ডেটার একটি ব্যাকআপ `snapshot.db` নামে তৈরি হয়।

---

## ✅ Step 1: Purge Existing etcd Data

```bash
rm -rf /var/lib/etcd
```

🔹 **কার্য:** পুরনো etcd ডেটা ডিরেক্টরি মুছে ফেলুন, যাতে নতুন snapshot থেকে পরিষ্কারভাবে restore করা যায়।

---

## ✅ Step 2: Restore etcd from Snapshot

```bash
ETCDCTL_API=3 etcdctl snapshot restore snapshot.db \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --name=k8sCluster \
  --data-dir=/var/lib/etcd \
  --initial-cluster-token=etcd-cluster-1 \
  --initial-cluster=k8sCluster=https://192.168.0.200:2380 \
  --initial-advertise-peer-urls=https://192.168.0.200:2380
```

🔹 **কার্য:** `snapshot.db` ফাইল থেকে etcd ডেটা `/var/lib/etcd` এ restore করা হচ্ছে। এতে cluster configuration নতুন করে rebuild হয়।

---

## ✅ Step 3: Update etcd Static Pod Manifest (if necessary)

```bash
sudo nano /etc/kubernetes/manifests/etcd.yaml
```

🔹 **কার্য:** `--data-dir=/var/lib/etcd` লাইনটি নিশ্চিত করুন restored data path অনুযায়ী।

📌 Save করে বের হোন, kubelet এটি detect করে etcd pod আবার চালাবে।

---

## ✅ Step 4: Verify etcd and Kubernetes Recovery

```bash
kubectl get all -A
```



---



---

## 🎉 শেষ কথা:

আপনি সফলভাবে ইটিসিডি স্ন্যাপশট থেকে একটি ক্লাস্টার স্টেট পুনরুদ্ধার করতে শিখলেন। এখন চাইলে আপনি এটি আপনার cluster setup automation বা GitHub backup strategy-তে অন্তর্ভুক্ত করতে পারেন।

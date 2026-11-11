I’ve written two manifest files:

01-jenkins-storage.yaml has the namespace, a local-storage StorageClass, and a 10Gi local PV.
02-jenkins-statefulset.yaml has the ServiceAccount, a headless Service, a ClusterIP Service, and the Jenkins StatefulSet.

A StatefulSet creates its own PVC through volumeClaimTemplates, so there is no separate PVC file. The PV pre-binds to the PVC it will create, jenkins-home-jenkins-0, through claimRef.

Deploy

1. Prepare the directory on the worker node. Local PVs don’t create the directory for you.

```bash
kubectl get nodes                       # note the node name to use
# on that node:
sudo mkdir -p /data/jenkins
sudo chown -R 1000:1000 /data/jenkins   # Jenkins runs as uid 1000
```

2. Set the node name in 01-jenkins-storage.yaml by replacing <NODE_NAME> with the node from step 1.

3. Apply and check

```bash
kubectl apply -f 01-jenkins-storage.yaml
kubectl apply -f 02-jenkins-statefulset.yaml

kubectl -n jenkins get pv,pvc,pods,svc
kubectl -n jenkins get pod jenkins-0 -w      # wait for 1/1 Running
```
The PVC will show Pending until the pod is scheduled. That is expected with WaitForFirstConsumer.

Test locally with port-forward
```bash
kubectl -n jenkins port-forward svc/jenkins 8080:8080
```
Open http://localhost:8080.

If you’re on a remote control-plane box and browsing from your laptop, bind to all interfaces and use the node’s IP:

```bash
kubectl -n jenkins port-forward --address 0.0.0.0 svc/jenkins 8080:8080
# then open http://<controlplane-ip>:8080
```

Get the initial admin password:

```bash
kubectl -n jenkins exec jenkins-0 -- cat /var/jenkins_home/secrets/initialAdminPassword
```
Data lives on one node, so if that node fails, Jenkins can’t be rescheduled elsewhere until the disk is available again. That’s fine for a lab, but for production use a network-backed storage class (EBS, Ceph, NFS with care) and back up JENKINS_HOME.

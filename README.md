# kubernetes-learning
kubernetes learning the hard way

# Persistent Volumes
## Details
```
 A PersistentVolume (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes. 
 It is a resource in the cluster just like a node is a cluster resource. PVs are volume plugins like Volumes, but have a lifecycle independent of any 
 individual Pod that uses the PV. This API object captures the details of the implementation of the storage, be that NFS, iSCSI, or a 
 cloud-provider-specific storage system.
 ```

1)  To list persistent volume in a cluster, get environment pv name and sort 
+ k get pv --sort-by=.metadata.name

2) List the specified pod Error log status line and record in the designated file
+ kubectl logs <podname> | grep bash > /opt/test.txt
+ k logs spin-clouddriver-ro-deck-58 -c clouddriver-ro-deck -n spin | grep bash > /tmp/tt.txt

3) List of nodes k8s available, and does not contain non-scheduling node NoReachable and writes to the digital file
+ kubectl get nodes
+ k get nodes

4. Create a pod name nginx, and scheduling on the node disk = ssd

pods/pod-nginx.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    disktype: ssd
```
5. Provide yaml a pod, requiring add Init Container, Init Container role is to create an empty file, if the file exists pod of Containers judgment, there is no exit

pods/init-containers.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: init-demo
spec:
  containers:
  - name:  nginx
    image:  busybox:1.28
    ports:
    - containerPort: 80
    command：['sh', '-c', 'if [ ! -e "/opt/myfile"]; then exit;fi;']
    volumeMounts:
    - name: workdir
      mountPath: /opt/
  # These containers are run during pod initialization
  initContainers:
  - name: install
    image: busybox
    command: ['sh', '-c', 'touch -p /opt/myfile']
    volumeMounts:
    - name: workdir
      mountPath: /opt/
  volumes:
  - name: workdir
    emptyDir: {}

```
Reference: Init Container

https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-initialization/#creating-a-pod-that-has-an-init-container

https://kubernetes.io/docs/concepts/workloads/pods/init-containers/

6. Specify the pod to create a name in the namespace test, containing four specified mirror nginx, redis, memcached, busybox

7. Create a pod name Test, the mirror is Nginx, Volume name cache-volume is hanging in / data directory, and Volume is the non-Persistent

```
apiVersion: v1
kind: Pod
metadata:
  name: test-pod 
spec:
  containers:
  - name: test
    image: nginx
    volumeMounts:
    - mountPath: /data
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir: {}

```
https://kubernetes.io/docs/concepts/storage/volumes/#local

8. List pod in the Service named test and find out using one of the highest CPU utilization, the pod written to the file name

#use-o wide acquisition SELECTOR service test of

+ kubectl get svc test -o wide

## get the results I casually made the

NAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGE SELECTOR

test ClusterIP None <none> 3306/TCP 50d app=wordpress,tier=mysql

# SELECTOR corresponds to the acquisition pod usage, find the maximum that written documents

+ kubectl top pods -l 'app=wordpress,tier=mysql'
+ k top pods -l 'app=spin' -n spin
+ k top pods -n spin | sort -k2 -n -r





.List the nodes in your cluster, along with their labels:
+ k get nodes --show-labels

Chose one of your nodes, and add a label to it:
+ k label nodes <your-node-name> disktype=ssd

Verify that your chosen node has a disktype=ssd label:
+ k get nodes --show-labels





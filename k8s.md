
## Get the nodes that are part of the cluster

```bash
kubectl get nodes
```

## Get teh k8s version

```bash
$: kubectl version
Client Version: v1.30.0+k3s1
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
Server Version: v1.30.0+k3s1
```

## Get OS on which k8s is running

```bash
$: kubectl get nodes -o wide
NAME           STATUS   ROLES                  AGE     VERSION        INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
controlplane   Ready    control-plane,master   8m40s   v1.30.0+k3s1   192.10.229.9   <none>        Alpine Linux v3.16   5.4.0-1106-gcp   containerd://1.7.15-k3s1
```

## Start a pod

```bash
$: kubectl run nginx --image=nginx
```

## Check status of the pod in default namespace

```bash
$: kubectl get po
```

## Get details on the pod

```bash
$: kubectl describe po pod_name
```

## Get details on pod using `-o wide`

```bash
$: kubectl describe po pod_name -o wide
```

This displays `node` on which the pod is running.

## Delete a pod

```bash
$: kubectl delete po pod_name
```

## Delete all pods

```bash
kubectl delete po --all
```

## Editing a pod

```bash
kubectl edit po redis
```

## Creating resource file using `--dry-run`

```bash
$: kubectl run redis --image=redis --dry-run -o yaml > redis.yml
```

This will create a yaml config file that can be used to run the pod.

## Running a resource from file

```bash
$: kubectl create -f file_name.yml
```

## Running a resource file after update

```bash
$: kubectl apply -f file_name.yml
```

## Get all replicaset 

```bash
kubectl get rs
```

## Describe replicaset

```bash
kubectl describe rs replica_set_name
```

## Delete replicaset

```bash
kubectl delete rs replica_set_name
```

## Sample Re


## After updating the ReplicaSet resource, following commands can be used to apply the updated configuration:

```bash
kubectl replace -f file_name.yml

# the following two commands doesn't require to update the replica count in `yaml` file.

kubectl scale --replicas=6 -f file_name.yml 

kubectl scale --replicas=6 rs replica_name
```

## View all deployment, replicaset and pods

```bash
kubectl get all
```

## Create deployment imperatively

```bash
kubectl create deploy httpd-frontend --image=httpd:2.4-alpine --replicas=3
```


## View the status of the deployment

```bash
kubectl rollout status deploy deploy_name
```

## View rollout history

```bash
kubectl rollout history deploy deploy_name
```

## Undo rollout

```bash
kubectl rollout undo deploy deploy_name
```


## Record deployment

```bash
kubectl create -f deploy.yml --record # record change while creating
kubectl edit deploy deploy_name  --record  # record change while editing
```

##  Change image of existing deployment 

```bash
kubectl set image deploy deploy_name nginx=nginx:1.18-perl --record
kubectl edit deploy deploy_name --record
```

We can use any one of the above command.





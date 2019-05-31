# K8S 101

## Useful commands

```shell
$ minikube service tomcat-deployment --url
http://127.0.0.1:32123

```

**List Pods**

`Lists all pods in all namespaces

```shell
$ kubectl get pods
NAME                                 READY   STATUS    RESTARTS   AGE
tomcat-deployment-75d7c68c7b-bj9zj   1/1     Running   1          40m
```

**Describe Pod**

Describes detailed information about all pods or a specified pod

```shell
$ kubectl describe pod tomcat-deployment-75d7c68c7b-bj9zj
Name:               tomcat-deployment-75d7c68c7b-bj9zj
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               minikube/10.0.2.15
Start Time:         Sun, 26 May 2019 03:18:43 +0000
Labels:             app=tomcat
                    pod-template-hash=75d7c68c7b
Annotations:        <none>
Status:             Running
IP:                 172.17.0.10
Controlled By:      ReplicaSet/tomcat-deployment-75d7c68c7b
Containers:
  tomcat:
    Container ID:   docker://ef3d1f2fef1705f2541e829f284115d5c51fdb1a6f271843a3224ec01488ed5b
    Image:          tomcat:9.0
    Image ID:       docker-pullable://tomcat@sha256:3f4b2548996ffd6d7984f76557fc4db75f92e155340191f7a7325b1f751d10ac
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sun, 26 May 2019 03:57:40 +0000
    Last State:     Terminated
      Reason:       Error
      Exit Code:    143
      Started:      Sun, 26 May 2019 03:24:22 +0000
      Finished:     Sun, 26 May 2019 03:34:24 +0000
    Ready:          True
    Restart Count:  1
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-9tn2f (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-9tn2f:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-9tn2f
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason          Age    From               Message
  ----    ------          ----   ----               -------
  Normal  Scheduled       41m    default-scheduler  Successfully assigned default/tomcat-deployment-75d7c68c7b-bj9zj to minikube
  Normal  Pulling         41m    kubelet, minikube  pulling image "tomcat:9.0"
  Normal  Pulled          36m    kubelet, minikube  Successfully pulled image "tomcat:9.0"
  Normal  Created         36m    kubelet, minikube  Created container
  Normal  Started         36m    kubelet, minikube  Started container
  Normal  SandboxChanged  4m8s   kubelet, minikube  Pod sandbox changed, it will be killed and re-created.
  Normal  Pulled          3m25s  kubelet, minikube  Container image "tomcat:9.0" already present on machine
  Normal  Created         3m15s  kubelet, minikube  Created container
  Normal  Started         2m56s  kubelet, minikube  Started container
```

**Expose port**

Exposes a port (TCP or UDP) for a given deployment, pod, or other resource

```shell
$ kubectl expose deployment tomcat-deployment --type=NodePort
service "tomcat-deployment" exposed
```

Exposes a port using the LoadBalancer type

```shell
$ kubectl expose deployment tomcat-deployment --type=LoadBalancer --port=8080 --target-port=8080 --name=tomcat-load-balancer
service/tomcat-load-balancer exposed

$ kubectl describe services tomcat-load-balancer
Name:                     tomcat-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=tomcat
Type:                     LoadBalancer
IP:                       10.106.93.33
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  30298/TCP
Endpoints:                172.17.0.13:8080,172.17.0.14:8080,172.17.0.5:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

```


**Port Forward** 

Forwards one or more local ports to a pod

```shell
$ kubectl port-forward tomcat-deployment-75d7c68c7b-bj9zj 5000:6000
Forwarding from 127.0.0.1:5000 -> 6000
```

**Attach**

Attaches to a process that is already running inside an existing container

```shell
$ kubectl attach tomcat-deployment-75d7c68c7b-bj9zj
If you don't see a command prompt, try pressing enter.
```

**Exec**

Execute a command in a container

* -i option will pass stdin to the container
* -t option will specify stdin is a TTY

```shell
$ kubectl exec -it tomcat-deployment-75d7c68c7b-bj9zj bash
root@tomcat-deployment-75d7c68c7b-bj9zj:/usr/local/tomcat# whoami
root
```

**Label Pods**

Updates the labels on a resource

```
$ kubectl label pods tomcat-deployment-75d7c68c7b-bj9zj healthy=false
pod/tomcat-deployment-75d7c68c7b-bj9zj labeled

$ kubectl label node minikube storageType=ssd
node/minikube labeled

$ kubectl describe node
```

**Run**

Run a particular image on the cluster

```shell
$ kubectl run --generator=run-pod/v1 hazelcast --image=hazelcast/hazelcast --port=5701
```

**Scale command**

```
$ kubectl scale --replicas=3 deployment/tomcat-deployment
deployment.extensions/tomcat-deployment scaled

$ kubectl describe deployment tomcat-deployment
Name:                   tomcat-deployment
Namespace:              default
CreationTimestamp:      Sun, 26 May 2019 03:18:42 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1beta2","kind":"Deployment","metadata":{"annotations":{},"name":"tomcat-deployment","namespace":"default"},"spec":{"r...
Selector:               app=tomcat
Replicas:               3 desired | 3 updated | 3 total | 1 available | 2 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=tomcat
  Containers:
   tomcat:
    Image:        tomcat:9.0
    Port:         8080/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      False   MinimumReplicasUnavailable
OldReplicaSets:  <none>
NewReplicaSet:   tomcat-deployment-75d7c68c7b (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  2m17s  deployment-controller  Scaled up replica set tomcat-deployment-75d7c68c7b to 3
```

**Rollout command**

```
$ kubectl rollout status deployment tomcat-deployment
deployment "tomcat-deployment" successfully rolled out

$ kubectl rollout history deployment/tomcat-deployment
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

$ kubectl rollout history deployment/tomcat-deployment --revision=2
deployment.extensions/tomcat-deployment with revision #2
Pod Template:
  Labels:       app=tomcat
        pod-template-hash=55947d7787
  Containers:
   tomcat:
    Image:      tomcat:9.0.1
    Port:       8080/TCP
    Host Port:  0/TCP
    Environment:        <none>
    Mounts:     <none>
  Volumes:      <none>

```

**Set Image command**

```
$ kubectl set image deployment/<deployment-name> <container-name>=<image-name>:<image-tag>
$ kubectl set image deployment/tomcat-deployment tomcat=tomcat:9.0.1
deployment.extensions/tomcat-deployment image updated
```

**Get Nodes**

```
$ kubectl get nodes
NAME       STATUS   ROLES    AGE     VERSION
minikube   Ready    master   2d15h   v1.12.4
```

**Creating a secret**

```
$ kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt
$ kubectl create secret generic mysql-pass --from-literal=password=MY_PASSWORD
```


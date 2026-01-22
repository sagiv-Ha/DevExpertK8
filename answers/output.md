# **Part 1 – Setup Minikube:**

PS C:\Users\sagiv\Devops_Course\k8> kubectl version
Client Version: v1.34.1
Kustomize Version: v5.7.1
Server Version: v1.34.0
PS C:\Users\sagiv\Devops_Course\k8> minikube version
minikube version: v1.37.0


#  **Part 2 – Start & Explore the Cluster**

PS C:\Users\sagiv\Devops_Course\k8> minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured

PS C:\Users\sagiv\Devops_Course\k8> kubectl cluster-info
Kubernetes control plane is running at https://127.0.0.1:4886
CoreDNS is running at https://127.0.0.1:4886/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

PS C:\Users\sagiv\Devops_Course\k8> kubectl get node
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   15m   v1.34.0
PS C:\Users\sagiv\Devops_Course\k8> kubectl get ns
NAME              STATUS   AGE
default           Active   15m
kube-node-lease   Active   15m
kube-public       Active   15m
kube-system       Active   15m
PS C:\Users\sagiv\Devops_Course\k8> kubectl get po -n kube-system
NAME                               READY   STATUS    RESTARTS      AGE
coredns-66bc5c9577-rdmn6           1/1     Running   0             15m
etcd-minikube                      1/1     Running   0             15m
kube-apiserver-minikube            1/1     Running   0             15m
kube-controller-manager-minikube   1/1     Running   0             15m
kube-proxy-h65nt                   1/1     Running   0             15m
kube-scheduler-minikube            1/1     Running   0             15m
storage-provisioner                1/1     Running   1 (14m ago)   15m

# **Part 3 – Explore Services**

kubectl get services -A
NAMESPACE     NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
default       kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP                  17m
kube-system   kube-dns     ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   17m

# **kubernetes service explanation:**
A Kubernetes Service provides a stable IP/DNS name to access a set of Pods, even if the Pods change or restart. It also load-balances traffic between multiple Pods of the same application.

 # **Part 4 – Deploy the Applications**

PS C:\Users\sagiv\Devops_Course\DevExpertK8\k8> kubectl apply -f .\
deployment.apps/backend created
service/backend created
deployment.apps/frontend created
service/frontend created

PS C:\Users\sagiv\Devops_Course\DevExpertK8\k8> kubectl get deployments
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
backend    1/1     1            1           2m4s
frontend   1/1     1            1           2m4s

PS C:\Users\sagiv\Devops_Course\DevExpertK8\k8> kubectl get svc        
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
backend      ClusterIP   10.109.210.137   <none>        80/TCP         2m8s
frontend     NodePort    10.107.31.77     <none>        80:32355/TCP   2m8s
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        25m

PS C:\Users\sagiv\Devops_Course\DevExpertK8\k8> kubectl get po
NAME                        READY   STATUS    RESTARTS   AGE
backend-576ccdb8d-d5xrx     1/1     Running   0          2m19s
frontend-7b9bcbbfc4-n4n9x   1/1     Running   0          2m19s

# **Part 6 – Access the Application**
minikube service frontend

#  **Part 7 – Inspect Resources**

kubectl describe deployment frontend
Name:                   frontend
Namespace:              default
CreationTimestamp:      Thu, 22 Jan 2026 17:20:18 +0200
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=frontend
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=frontend
  Containers:
   frontend:
    Image:         nginx:alpine
    Port:          80/TCP
    Host Port:     0/TCP
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   frontend-7b9bcbbfc4 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  11m   deployment-controller  Scaled up replica set frontend-7b9bcbbfc4 from 0 to 1



# describe pod:
kubectl describe pod backend-576ccdb8d-d5xrx
Name:             backend-576ccdb8d-d5xrx
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Thu, 22 Jan 2026 17:20:18 +0200
Labels:           app=backend
                  pod-template-hash=576ccdb8d
Annotations:      <none>
Status:           Running
IP:               10.244.0.3
IPs:
  IP:           10.244.0.3
Controlled By:  ReplicaSet/backend-576ccdb8d
Containers:
  backend:
    Container ID:   docker://39a9faed8ae8c5f5c942bbdc18e8a4c337ed8f2ea0271d0df7a34facee5d76b5
    Image:          nginxdemos/hello
    Image ID:       docker-pullable://nginxdemos/hello@sha256:b28d1e16c676896aaa98ed9c36d406cc4f4289558487f15e37e04354720f807a
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Thu, 22 Jan 2026 17:20:28 +0200
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-mmkwq (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       True
  ContainersReady             True
  PodScheduled                True
Volumes:
  kube-api-access-mmkwq:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  13m   default-scheduler  Successfully assigned default/backend-576ccdb8d-d5xrx to minikube
  Normal  Pulling    13m   kubelet            Pulling image "nginxdemos/hello"
  Normal  Pulled     12m   kubelet            Successfully pulled image "nginxdemos/hello" in 8.098s (8.098s including waiting). Image size: 52556834 bytes.
  Normal  Created    12m   kubelet            Created container: backend
  Normal  Started    12m   kubelet            Started container backend

  # kubectl logs

ubectl logs backend-576ccdb8d-d5xrx        
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf is not a file or does not exist
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/01/22 15:20:28 [notice] 1#1: using the "epoll" event method
2026/01/22 15:20:28 [notice] 1#1: nginx/1.29.1
2026/01/22 15:20:28 [notice] 1#1: built by gcc 14.2.0 (Alpine 14.2.0)
2026/01/22 15:20:28 [notice] 1#1: OS: Linux 6.6.87.2-microsoft-standard-WSL2
2026/01/22 15:20:28 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2026/01/22 15:20:28 [notice] 1#1: start worker processes
2026/01/22 15:20:28 [notice] 1#1: start worker process 20
2026/01/22 15:20:28 [notice] 1#1: start worker process 21
2026/01/22 15:20:28 [notice] 1#1: start worker process 22
2026/01/22 15:20:28 [notice] 1#1: start worker process 23
2026/01/22 15:20:28 [notice] 1#1: start worker process 24
2026/01/22 15:20:28 [notice] 1#1: start worker process 25
2026/01/22 15:20:28 [notice] 1#1: start worker process 26
2026/01/22 15:20:28 [notice] 1#1: start worker process 27



# **Part 8 – Undeploy the Applications**

kubectl delete -f .
deployment.apps "backend" deleted from default namespace
service "backend" deleted from default namespace
deployment.apps "frontend" deleted from default namespace
service "frontend" deleted from default namespace
PS C:\Users\sagiv\Devops_Course\DevExpertK8\k8> kubectl get po
No resources found in default namespace.
PS C:\Users\sagiv\Devops_Course\DevExpertK8\k8> kubectl get deployments
No resources found in default namespace.
PS C:\Users\sagiv\Devops_Course\DevExpertK8\k8> kubectl get svc        
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   38m

# ** Bonus **
**Scale Deployment**

PS C:\Users\sagiv\Devops_Course\DevExpertK8\k8> kubectl get replicaset
NAME                  DESIRED   CURRENT   READY   AGE
backend-576ccdb8d     1         1         1       34s
frontend-7b9bcbbfc4   1         1         1       33s
PS C:\Users\sagiv\Devops_Course\DevExpertK8\k8> kubectl scale deployment backend --replicas=2
deployment.apps/backend scaled
PS C:\Users\sagiv\Devops_Course\DevExpertK8\k8> kubectl get replicaset
NAME                  DESIRED   CURRENT   READY   AGE
backend-576ccdb8d     2         2         1       78s
frontend-7b9bcbbfc4   1         1         1       77s


**Explain frontend accessibility**
The frontend is exposed externally via a NodePort service or minikube service frontend), so it can be opened from the browser. The backend is usually a ClusterIP service, which is only reachable from the Kubernetes cluster.

# **Deployment vs Pod:**
A Pod is the smallest runnable unit in Kubernetes that runs one or more containers. A Deployment manages Pods by keeping the desired number of replicas running and handling updates/rollbacks.

# **Service vs Container**

A Container is the application process running from an image inside a Pod. A Service provides a stable network endpoint to access and load-balance traffic to Pods running those containers.
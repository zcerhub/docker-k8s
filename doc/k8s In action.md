```
apiVersion: v1  #Deployment属于apps API组，版本为v1beta1
kind: ReplicationController  
metadata:
  name: kubia
spec:
  replicas: 3
  selector:
    app: kubia
  template:
    metadata:
      name: kubia
      labels:
        app: kubia
    spec:
      containers:
      - image: luksa/kubia
        name: nodejs
```





```
[root@localhost ~]# kubectl get po
NAME          READY   STATUS    RESTARTS   AGE
kubia-5wvqr   1/1     Running   0          2m19s
kubia-p88cr   1/1     Running   0          2m19s
kubia-twrzs   1/1     Running   0          2m19s
[root@localhost ~]# kubectl label pod kubia-5wvqr type=special
pod/kubia-5wvqr labeled
[root@localhost ~]# kubectl get pods --show-labels 
NAME          READY   STATUS    RESTARTS   AGE     LABELS
kubia-5wvqr   1/1     Running   0          2m59s   app=kubia,type=special
kubia-p88cr   1/1     Running   0          2m59s   app=kubia
kubia-twrzs   1/1     Running   0          2m59s   app=kubia
[root@localhost ~]# kubectl label pod kubia-5wvqr app=foo --overwrite 
pod/kubia-5wvqr labeled
[root@localhost ~]# kubectl get pods -L app
NAME          READY   STATUS    RESTARTS   AGE     APP
kubia-5wvqr   1/1     Running   0          4m18s   foo
kubia-756gv   1/1     Running   0          18s     kubia
kubia-p88cr   1/1     Running   0          4m18s   kubia
kubia-twrzs   1/1     Running   0          4m18s   kubia
```





```
[root@localhost ~]# kubectl  scale  rc kubia --replicas=10
replicationcontroller/kubia scaled

缩容
```



删rc但是不删除pod

```
[root@localhost ~]# kubectl delete rc kubia --cascade=false
replicationcontroller "kubia" deleted
[root@localhost ~]# kubectl get rc
No resources found in default namespace.
[root@localhost ~]# kubectl get pod
NAME          READY   STATUS    RESTARTS   AGE
kubia-5wvqr   1/1     Running   0          9m36s
kubia-756gv   1/1     Running   0          5m36s
kubia-p88cr   1/1     Running   0          9m36s
kubia-twrzs   1/1     Running   0          9m36s
```





```
apiVersion: apps/v1  #Deployment属于apps API组，版本为v1beta1
kind: ReplicaSet  
metadata:
  name: kubia
spec:
  replicas: 3
  selector:
    matchLabels:
      app: kubia
  template:
    metadata:
      name: kubia
      labels:
        app: kubia
    spec:
      containers:
      - image: luksa/kubia
        name: nodejs
```





使用强大的集合选择器

```
apiVersion: apps/v1  #Deployment属于apps API组，版本为v1beta1
kind: ReplicaSet  
metadata:
  name: kubia
spec:
  replicas: 3
  selector:
    matchExpressions:
      - key: app
        operator: In
        values:
          - kubia
  template:
    metadata:
      name: kubia
      labels:
        app: kubia
    spec:
      containers:
      - image: luksa/kubia
        name: nodejs
```



```
[root@localhost ~]# kubectl delete rs kubia 
replicaset.apps "kubia" deleted
```



```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: ssd-monitor
spec:
  selector:
    matchLabels:
      apps: ssd-monitor
  template:
    metadata:
      labels:
        apps: ssd-monitor
    spec:
      containers:
      - name: main
        image: luksa/ssd-monitor
```





```
[root@localhost ~]# kubectl get ds
NAME          DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
ssd-monitor   1         1         0       1            0           <none>          14s
```

打上label后进行调度

```
[root@localhost ~]# kubectl label node  localhost.localdomain disk=ssd
node/localhost.localdomain labeled
[root@localhost ~]# kubectl get ds
NAME          DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
ssd-monitor   1         1         1       1            1           <none>          85s
```







## 部署node。js应用

kubectl run kubia  --image=luksa/kubia --port=8080 



kubectl expose pod kubia --name kubia-http





```
apiVersion: apps/v1  #Deployment属于apps API组，版本为v1beta1
kind: ReplicaController  
metadata:
  name: kubia
spec:
  replicas: 3
  selector:
    matchLabels:
      app: kubia
  template:
    metadata:
      name: kubia
      labels:
        app: kubia
    spec:
      containers:
      - image: luksa/kubia
        name: nodejs
```

```
[root@localhost ~]# kubectl get rc
NAME    DESIRED   CURRENT   READY   AGE
kubia   3         3         0       6s
```

```
[root@localhost ~]# kubectl scale rc kubia --replicas=5
replicationcontroller/kubia scaled
[root@localhost ~]# kubectl get po
NAME          READY   STATUS              RESTARTS   AGE
kubia-7k2lm   1/1     Running             0          70s
kubia-dnxkt   0/1     ContainerCreating   0          9s
kubia-gkx9h   0/1     ContainerCreating   0          70s
kubia-hflxd   0/1     ContainerCreating   0          9s
kubia-zs6n9   1/1     Running             0          70s
[root@localhost ~]# 

[root@localhost ~]# kubectl get rc
NAME    DESIRED   CURRENT   READY   AGE
kubia   5         5         3       2m1s

[root@localhost ~]# kubectl get po
NAME          READY   STATUS    RESTARTS   AGE
kubia-7k2lm   1/1     Running   0          2m23s
kubia-dnxkt   1/1     Running   0          82s
kubia-gkx9h   1/1     Running   0          2m23s
kubia-hflxd   1/1     Running   0          82s
kubia-zs6n9   1/1     Running   0          2m23s



[root@localhost ~]# kubectl get pods -o wide
NAME          READY   STATUS    RESTARTS   AGE     IP            NODE                    NOMINATED NODE   READINESS GATES
kubia-7k2lm   1/1     Running   0          3m14s   172.17.0.10   localhost.localdomain   <none>           <none>
kubia-dnxkt   1/1     Running   0          2m13s   172.17.0.2    localhost.localdomain   <none>           <none>
kubia-gkx9h   1/1     Running   0          3m14s   172.17.0.11   localhost.localdomain   <none>           <none>
kubia-hflxd   1/1     Running   0          2m13s   172.17.0.3    localhost.localdomain   <none>           <none>
kubia-zs6n9   1/1     Running   0          3m14s   172.17.0.9    localhost.localdomain   <none>           <none>
```



```
[root@localhost ~]# kubectl explain pod.spec
KIND:     Pod
VERSION:  v1

RESOURCE: spec <Object>

DESCRIPTION:
     Specification of the desired behavior of the pod. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status

     PodSpec is a description of a pod.

FIELDS:
   activeDeadlineSeconds	<integer>
     Optional duration in seconds the pod may be active on the node relative to
     StartTime before the system will actively try to mark it failed and kill
     associated containers. Value must be a positive integer.

   affinity	<Object>
     If specified, the pod's scheduling constraints

   automountServiceAccountToken	<boolean>
     AutomountServiceAccountToken indicates whether a service account token
     should be automatically mounted.

   containers	<[]Object> -required-
     List of containers belonging to the pod. Containers cannot currently be
     added or removed. There must be at least one container in a Pod. Cannot be
     updated.

   dnsConfig	<Object>
     Specifies the DNS parameters of a pod. Parameters specified here will be
     merged to the generated DNS configuration based on DNSPolicy.

   dnsPolicy	<string>
     Set DNS policy for the pod. Defaults to "ClusterFirst". Valid values are
     'ClusterFirstWithHostNet', 'ClusterFirst', 'Default' or 'None'. DNS
     parameters given in DNSConfig will be merged with the policy selected with
     DNSPolicy. To have DNS options set along with hostNetwork, you have to
     specify DNS policy explicitly to 'ClusterFirstWithHostNet'.
```

```
[root@localhost ~]# kubectl label node localhost.localdomain gpu=true
node/localhost.localdomain labeled
[root@localhost ~]# kubectl get nodes -l gpu=true
NAME                    STATUS   ROLES    AGE   VERSION
localhost.localdomain   Ready    master   24h   v1.18.3
```





```
apiVersion: v1  #Deployment属于apps API组，版本为v1beta1
kind: Pod  
metadata:
  name: kubia
spec:
  nodeSelector:
    gpu: "true"
  containers:
    - image: luksa/kubia
      name: nodejs
      
[root@localhost ~]# kubectl create -f pod1.yaml 
pod/kubia created  


[root@localhost ~]# 
[root@localhost ~]# kubectl annotate pod kubia mycomponay.com/someannotation="foo bar"
pod/kubia annotated
[root@localhost ~]# kubectl describe pod kubia
Name:         kubia
Namespace:    default
Priority:     0
Node:         localhost.localdomain/192.168.252.82
Start Time:   Tue, 18 Aug 2020 01:15:51 +0800
Labels:       <none>
Annotations:  mycomponay.com/someannotation: foo bar
```





```
[root@localhost ~]# kubectl delete pod  kubia-7k2lm 
pod "kubia-7k2lm" deleted

[root@localhost ~]# kubectl label po kubia-dnxkt  create_method=manual
pod/kubia-dnxkt labeled
[root@localhost ~]# kubectl label po kubia-hflxd  create_method=manual
pod/kubia-hflxd labeled


[root@localhost ~]# kubectl delete po -l create_method=manual
pod "kubia-dnxkt" deleted
pod "kubia-hflxd" deleted


[root@localhost ~]# kubectl label po kubia-llr4z rel=canary
pod/kubia-llr4z labeled
[root@localhost ~]# kubectl delete po -l rel=canary
pod "kubia-llr4z" deleted



[root@localhost ~]# kubectl label po kubia-jbblj type=special
pod/kubia-jbblj labeled
[root@localhost ~]# kubectl get po --show-labels
NAME          READY   STATUS              RESTARTS   AGE   LABELS
kubia-cfk6b   0/1     ContainerCreating   0          40s   app=kubia
kubia-gtssl   0/1     ContainerCreating   0          40s   app=kubia
kubia-jbblj   0/1     ContainerCreating   0          40s   app=kubia,type=special


[root@localhost ~]# kubectl label pod kubia-cfk6b app=foo --overwrite
pod/kubia-cfk6b labeled



[root@localhost ~]# kubectl get pods -L app
NAME          READY   STATUS    RESTARTS   AGE     APP
kubia-cfk6b   1/1     Running   0          2m16s   foo
kubia-gtssl   1/1     Running   0          2m16s   kubia
kubia-jbblj   1/1     Running   0          2m16s   kubia
kubia-zx7gf   1/1     Running   0          22s     kubia


[root@localhost ~]# kubectl edit rc kubia 
replicationcontroller/kubia edited



[root@localhost ~]# kubectl scale rc kubia --replicas=10
replicationcontroller/kubia scaled
[root@localhost ~]# kubectl get po
NAME          READY   STATUS              RESTARTS   AGE
kubia-2tbc7   0/1     ContainerCreating   0          10s
kubia-64gzt   0/1     ContainerCreating   0          10s
kubia-8ffw5   0/1     ContainerCreating   0          10s
kubia-cfk6b   1/1     Running             0          5m4s
kubia-gtssl   1/1     Running             0          5m4s
kubia-jbblj   1/1     Running             0          5m4s
kubia-r984q   0/1     ContainerCreating   0          10s
kubia-rrtjj   0/1     ContainerCreating   0          10s
kubia-s4q4k   0/1     ContainerCreating   0          10s
kubia-zn69f   0/1     ContainerCreating   0          10s
kubia-zx7gf   1/1     Running             0          3m10s


[root@localhost ~]# kubectl edit rc kubia 
replicationcontroller/kubia edited
[root@localhost ~]# kubectl get rc
NAME    DESIRED   CURRENT   READY   AGE
kubia   4         4         4       5m56s


[root@localhost ~]# kubectl scale rc kubia --replicas=2
replicationcontroller/kubia scaled
[root@localhost ~]# kubectl get rc
NAME    DESIRED   CURRENT   READY   AGE
kubia   2         2         2       6m23s


[root@localhost ~]# kubectl delete rc kubia --cascade=false
replicationcontroller "kubia" deleted
[root@localhost ~]# kubectl get po
NAME          READY   STATUS    RESTARTS   AGE
kubia-cfk6b   1/1     Running   0          7m5s
kubia-gtssl   1/1     Running   0          7m5s
kubia-jbblj   1/1     Running   0          7m5s



[root@localhost ~]# kubectl delete rs kubia 
replicaset.apps "kubia" deleted




apiVersion: v1
kind: Service
metadata:
  name: external-service
spec:
  ports:
    - port: 80
    
apiVersion: v1
kind: Endpoints
metadata:
  name: external-service
subsets:
  - addresses:
    - ip: 172.17.0.3   
    - ip: 172.17.0.2
    ports:
    - port: 8080
    
[root@localhost ~]# kubectl create -f svc1.yaml 
service/external-service created
[root@localhost ~]# kubectl create -f end1.yaml 
endpoints/external-service created



[root@localhost ~]# kubectl get svc
NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
external-service   ClusterIP   10.103.241.107   <none>        80/TCP    57s
kubernetes         ClusterIP   10.96.0.1        <none>        443/TCP   20m
[root@localhost ~]# kubectl get po
NAME          READY   STATUS    RESTARTS   AGE
kubia-cfk6b   1/1     Running   0          19m
kubia-nx6vc   1/1     Running   0          5m44s
kubia-q2cbz   1/1     Running   0          5m44s
kubia-rnx22   1/1     Running   0          5m44s
[root@localhost ~]# kubectl exec -it kubia-cfk6b  /bin/bash
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl kubectl exec [POD] -- [COMMAND] instead.
root@kubia-cfk6b:/# curl http://10.103.241.107:80/
You've hit kubia-rnx22
root@kubia-cfk6b:/# curl http://10.103.241.107:80/
You've hit kubia-rnx22
root@kubia-cfk6b:/# 




apiVersion: v1
kind: Service
metadata:
  name: external-service
spec:
  type: ExternalName
  externalName: someapi.somecompany.com
  ports:
    - port: 80
    
```


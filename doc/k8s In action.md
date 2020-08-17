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




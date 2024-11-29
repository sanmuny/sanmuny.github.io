---
title: Kubernetes复习笔记
tags: [kubernetes, cloud native]
published: true
hideInList: false
feature: 
isTop: false
categories: 云计算
cover: /images/k8s.webp
---

### Kubernetes要解决什么问题？

手动部署容器难以维护，使用容器编排技术可以解决如下问题：

- 容器崩溃的时候可以自动部署新的容器来代替
- 可以自动增加或者减少容器的数量来满足高可用和自动伸缩的需求
- 可以使用负载均衡来将流量分配到不同的容器中
- 不依赖于云服务提供商的通用配置

### Kubernetes核心概念及架构

Kubernetes架构图
<img src="/images/k8s/arch.png" style="display: inline-block" width=500 />

Work node架构图
<img src="/images/k8s/worker-node-arch.png" style="display: inline-block" width=500 />

Master node架构图
<img src="/images/k8s/master-node-arch.png" style="display: inline-block" width=500 />


Pod: kubernetes管理的主要对象，可以由一个或者共享资源的一组容器组成
kubelet: 管理worker node和master node之间的通信
kube-proxy: 运行在work node上，用于管理Node和Pod的网络通信
API Server: 提供API服务
Scheduler: 选择worker node运行Pod
Controller: 监控Pod数量，控制worker node
Worker node: 运行Pod的机器或者虚拟机
Master node: 运行Control Plane的机器或者虚拟机

### Kubernetes核心对象

1. Pod: Kubernetes管理的最小单元

- 包含一个或多个容器
- Pod中的容器共享网络及存储等资源
- 拥有一个cluster内部IP，Pod中的容器可以通过localhost互相访问

2. Deployment: 控制多个Pod

- 用户指定一个状态，Kubernetes负责保持这个状态
- Deployments可以被暂停、删除及回滚
- Deployments可以自动地动态伸缩

``` bash
$ kubectl create deployment web-app --image caddy
$ kubectl get deployment web-app
$ kubectl describe deployment web-app
$ kubectl scale --replicas 2 deployment.apps/web-app
$ kubectl set image deployment/web-app caddy=nginx
$ kubectl rollout status deployment web-app
$ kubectl rollout undo deployment web-app
$ kubectl rollout history deployment web-app
$ kubectl rollout undo deployment web-app --to_revision 1
$ kubectl apply -f deployment.yaml
$ kubectl delete deployments,services -l app=web-app
```

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  selector:
    matchLabels:
      app: web-app
  replicas: 1    
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
        - name: web-app-container
          image: nginx
          livenessProbe:
            httpGet:
              path: /
              port: 80
            periodSeconds: 10
            initialDelaySeconds: 5
```

3. Service: Exposes Pods to the cluster or externally

- Pod拥有一个内部IP，每次被替换的时候都会改变
- Service可以让一组Pods共享一个IP
- Service可以让Pod可以被外部访问

``` bash
$ kubectl expose deployment web-app --type=NodePort --name=web-app-service --port=80
$ kubectl port-forward service/web-app-service 30000:80
$ kubectl apply -f service.yaml
```

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
spec:
  selector:
    app: web-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort

```

### Kubernetes数据管理及存储卷

#### 普通卷
Kubernetes中的volume会随着Pod的结束而结束，而container重启并不影响volume.给Pod添加Volume，需要在pod.spec中添加下面的定义

``` yaml
volumes:
- name: web-app-volume
  emptyDir: {}
```
如果Pod中的container需要使用这个volume需要在container的配置中添加：

```yaml
volumeMounts:
  - mountPath: /usr/share/nginx/html
    name: web-app-volume
```
- emptyDir类型的volume会创建一个空的目录，在Pod中的containers中共享，Pod删除后volume也会被永久删除。
- 如果想要在多个Pod中共享volume，可以创建hostPath类型的volume，类似于容器中的Bind mount。
- csi(Container Storage Interface)类型可以让同一个厂商的不同类型storage都用这个类型，从而减少了类型的定义。
- 更多的volume类型可以参考[这里](https://kubernetes.io/docs/concepts/storage/volumes)。

#### 持久卷
PV(Persistent volumes)持久卷不依赖于Pod和Node，Pod删除后PV也会保持，而且另外一个node上的pod也可以共享这个持久卷。Pod可以通过定义一个PVC(PV Claim)来使用持久卷。

PV的定义：
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: host-pv
spec:
  capacity:
    storage: 4Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  storageClassName: standard
  hostPath:
    path: /data
    type: DirectoryOrCreate
```

PVC的定义：
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: host-pvc
spec:
  volumeName: host-pv
  accessModes:
    - ReadWriteOnce
  storageClassName: standard
  resources:
    requests:
      storage: 1Gi
```

#### Stateful Sets

Stateful Set可以让Pod按顺序启动，Pod的名字也是有固定的编号。可以通过如下的定义创建一个stateful set:
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  selector:
    matchLabels:
      app: nginx # has to match .spec.template.metadata.labels
  serviceName: "nginx"
  replicas: 3 # by default is 1
  minReadySeconds: 10 # by default is 0
  template:
    metadata:
      labels:
        app: nginx # has to match .spec.selector.matchLabels
    spec:
      terminationGracePeriodSeconds: 10
      containers:
      - name: nginx
        image: registry.k8s.io/nginx-slim:0.8
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "my-storage-class"
      resources:
        requests:
          storage: 1Gi
```

##### Headless Services

Service可以让请求load balance到不同的Pod。其它Pod可以通过service的DNS例如`mysql.default.svc.cluster.local`来访问服务，但无法访问具体的Pod，因为Pod的DNS是根据IP来的，但每次Pod的销毁和创建IP地址都会变化，例如`10-40-2-8.default.pod.cluster.local`。如果其它Pod想要直接访问service中的其中一个Pod，例如Mysql的master pod，就需要通过headless service。Headless service可以给Pod赋予固定的DNS`podname.headless-svc-name.namespace.svc.cluster-domain.example`,如`mysql-1.mysql-h-svc.default.svc.cluster.local`。创建headless service也很简单，只需要将clusterIP设置为None即可，例如：
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
```
在stateful set中需要通过serviceName来指定这个headless service.

##### stateful set的存储解决方案

如果stateful set中的每个Pod都需要自己的PV和PVC，可以通过在stateful set的spec指定volumeClaimTemplates的方式实现，stateful set会保证每个Pod在重建后依然挂载原先的PVC：
```yaml
volumeClaimTemplates:
- metadata:
    name: data-volume
  spec:
    accessModes:
      - ReadWriteOnce
    storageClassName: google-storage
    resources:
      requests:
        storage: 500Mi
```


### 环境变量及配置

Dockerfile中的Entrypoint可以在Kubernetes中用`command: []`来设置，而对应的CMD则可以用`args: []`来设置

#### ConfigMap

可以定义ConfigMap来提供容器运行的环境变量：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: web-app-config-map
data:
  folder: story
  user: user
```

然后在容器的定义中使用它:
```yaml
env:
  - name: STORY_FOLDER
    valueFrom:
      configMapKeyRef:
        name: web-app-config-map
        key: folder
```

#### Secret
可以用如下的YAML定义secret:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: web-app-secret
data:
  password: cGFzc3dvcmQ=
```
然后再容器中可以把secrets作为环境变量来使用：
```yaml
envFrom:
  - secretRef:
      name: web-app-secret
```

或者：
```yaml
env:
  - name: STORY_FOLDER
    valueFrom:
      secretKeyRef:
        name: web-app-secret
        key: password
```
在或者把Secret作为存储卷来使用：
```yaml
volumes:
- name: app-secret-volume
  secret:
    secretName: web-app-secret
```

- Secret仅仅编码了，并没有加密
- Secret在ETCD中也没有加密
- Namespace中所有有权限创建pod/deployment的用户都可以访问secret

#### security context
可以在Pod spec或者container中定义securityContext来增强容器的安全性，例如：
```yaml
securityContext:
  runAsUser: 1000
  capabilities:
    add: ["MAC_ADMIN"]
```

#### 请求资源
在容器的定义中可以使用如下yaml来请求CPU和内存资源及设置容器的资源使用限制：
``` yaml
resources:
  requests:
    memory: "4Gi"
    cpu: 2
  limits:
    memory: "8Gi"
    cpu: 4
```

可以创建`LimitRange`对象来设置Pod默认的资源限制：
```yaml
apiVersion: v1
Kind: LimitRange
metadata:
  name: cpu-constraint
spec:
  limits:
  - default:
      cpu: 500m
    defaultRequest:
      cpu: 500m
    max:
      cpu: 1
    min:
      cpu: 100m
    type: Container
```

可以创建`ResourceQuota`来指定namespace的资源限制：
```yaml
apiVersion: v1
Kind: ResourceQuota
metadata:
  name: my-resource-quota
spec:
  hard:
    requests.cpu: 4
    requests.memory: 4Gi
    limits.cpu: 10
    limits.memory: 10Gi
```

#### Taints及Tolerations

Taint可以给一个Node打一个标签，而toleration则可以让Pod运行于taint标记的Node，没有设置toleration的Pod则不能运行于taint过的node。

给一个Node打上taint标记：

```bash
kubectl taint nodes {node-name} {key=value}:{taint-effect}
```

给Pod设置toleration:
```yaml
tolerations:
- key: "app"
  operator: "Equal"
  value: "blue"
  effect: NoSchedule
```

taint effect可以是：

- NoSchedule: 没有设置toleration的Pod不能调度到该node
- PreferNoSchedule: 没有设置toleration的Pod不能调度到该node，但不能保证。
- NoExecute: 没有设置toleration的Pod不能调度到该node运行，已经调度的则停止运行。

#### Node Selectors
可以用下面的命令来给一个node打标签：
```bash
$ kubectl label nodes {node-name} size=Large
```

然后在Pod的定义中，可以使用node selector来选择node:
```yaml
nodeSelector:
  size: Large
```

#### Node Affinity
通过node selectors选择node功能比较简单，Node Affinity则功能更加丰富。在Pod的定义中，可以使用affinity来选择node：

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoreDuringExecution:
    - matchExpression:
      - key: size
        operator: NotIn
        values:
        - Small
```

#### Labels, Selectors以及Annotations

Labels用于给Kubernetes对象打标签，而Selectors可以根据这些标签来快速过滤出来所要查找的对象。例如，给一个Pod打标签只需要在metadata中定义labels即可。

```yaml
metadata:
  name: web-app
  labels:
    app: App1
    function: front-end
```

通过selector来查找对应的Pod：
```bash
$kubectl get pods --selector app=App1
NAME                       READY   STATUS    RESTARTS   AGE
web-app-6cc4887574-pcld4   1/1     Running   0          26s
web-app-6cc4887574-w2mx6   1/1     Running   0          26s
web-app-6cc4887574-xhbkf   1/1     Running   0          26s
```

Annotations则用于给Kubernetes对象打上用于集成的一些信息，如:
- build,release,image信息
- 开发者的email,phone等信息

#### 升级策略

1. 蓝绿升级：先创建一个新的deployment，标记为`version:v2`，将service的selector从`version:v1`改成`version:v2`
2. 金丝雀升级: 创建一个新的deployment, 标记为`version:v2`，service不变，标记依然为两个版本共有的标记，例如`app: web-app`，将新的deployment的replica从小到大，直到升级为需要的值。然后删除原来的deployment。

#### Jobs及CronJobs

可以用如下的YAML定义一个Job：
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: add-job
spec:
  completions: 3
  parallelism: 3
  template:
    spec:
      containers:
      - name: math-add
        image: ubuntu
        command: ['expr', '3', '+', '2']
      restartPolicy: Never
```

可以用如下的YAML定义一个CronJob:
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: add-cron-job
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      completions: 3
      parallelism: 3
      template:
        spec:
          containers:
          - name: math-add
            image: ubuntu
            command: ['expr', '3', '+', '2']
          restartPolicy: Never
```


### 网络

#### Service

1. CluseterIP: 仅提供Cluster内部访问的IP地址
2. NodePort: 将container的port映射到Node上的某一个port，外部应用则可以通过node的IP及该port访问服务。
3. LoadBalancer: 将流量根据一定的策略引入到不同的Pod中

<img src="/images/k8s/service-node-port.png" style="display: inline-block" width=500 />

环境变量SERVICE_NAME_SERVICE_HOST保存Pod的Cluster IP. 在指定环境变量的时候，value中填写"service_name.default"可以指向service的Cluster IP.

#### Ingress

Ingress可以看做是Kubernetes对反向代理的抽象，例如可以把不同Host不同Path的流量导入到不同的service中。

可以通过如下的YAML创建一个Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-wildcard-host
spec:
  rules:
  - host: "foo.bar.com"
    http:
      paths:
      - pathType: Prefix
        path: "/bar"
        backend:
          service:
            name: service1
            port:
              number: 80
  - host: "*.foo.com"
    http:
      paths:
      - pathType: Prefix
        path: "/foo"
        backend:
          service:
            name: service2
            port:
              number: 80
```

#### 网络策略

网络策略(Network Policy)可以用来控制Pod之间的互相访问。例如，我们创建如下的一个Network Policy.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - ipBlock:
            cidr: 172.17.0.0/16
            except:
              - 172.17.1.0/24
        - namespaceSelector:
            matchLabels:
              project: myproject
        - podSelector:
            matchLabels:
              role: frontend
      ports:
        - protocol: TCP
          port: 6379
  egress:
    - to:
        - ipBlock:
            cidr: 10.0.0.0/24
      ports:
        - protocol: TCP
          port: 5978
```

### 安全

#### Kubernetes中的认证

- Kubernetes认证主体是User(Admin, developer)及程序(ServiceAccount)。
- User在访问kubernetes资源之前，需要通过kube-apiserver进行认证
- Kubernetes不保存用户名、密码，而是通过下列外部资源进行认证：
  - 静态密码文件
  - 静态token文件
  - 证书
  - 外部认证服务

在启动Kubernetes API server的时候可以通过`--basic-auth-file`来指定静态密码文件，例如user-details.csv：
```csv
password1, user1, user_id1, group1
password2, user2, user_id2, group2
...
```
也可以通过`--token-auth-file`来指定静态token文件，例如user-tokens.csv
```csv
token1, user1, user_id1, group1
token2, user2, user_id2, group2
...
```

#### 授权

- AlwaysAllow
- AlwaysDeny
- Node
- ABAC
- RBAC
- Webhook

<img src="/images/k8s/cluster-roles.png" style="display: inline-block" width=800 />

#### service account
service account可以看做Kubernetes内部的程序为主体的账户，例如，我们可以通过下面的定义给Kubernetes dashboard来创建一个SA，以及对应的token.
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  namespace: default
  name: dashboard-sa
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: demo-role
rules:
  - apiGroups: ["*"]
    resources: ["*"]
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: cluster-role-binding
  namespace: default
subjects:
  - kind: ServiceAccount
    name: dashboard-sa
roleRef:
  kind: ClusterRole
  name: demo-role
  apiGroup: rbac.authorization.k8s.io
```
创建sa对应的token:
```bash
$kubectl create token dashboard-sa
```

#### KubeConfig
<img src="/images/k8s/kubeconfig.png" style="display: inline-block" width=800 />

#### API group

<img src="/images/k8s/kube-api.png" style="display: inline-block" width=800 />

<img src="/images/k8s/kube-apis.png" style="display: inline-block" width=800 />

#### Admission Controllers
<img src="/images/k8s/admission.png" style="display: inline-block" width=800 />


#### API版本

/v1alpha1 -> /v1beta1 -> v1
<img src="/images/k8s/api-version.png" style="display: inline-block" width=800 />
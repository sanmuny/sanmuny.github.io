---
title: 企业级Kubernetes离线部署指南
date: 2025-03-14 22:44:12
tags: [云原生,云计算]
published: true
hideInList: false
feature: 
isTop: false
categories: 云计算
cover: /images/k8s.webp
---

## 问题场景与需求分析

### 1.1 核心挑战
在隔离网络环境下构建企业级Kubernetes集群时面临三大挑战：
- **资源隔离**：无法直接访问DockerHub、GitHub等公共资源库
- **安全合规**：需通过私有镜像仓库（Harbor）实现镜像生命周期管理
- **生产级要求**：需满足高可用架构、持久化存储、网络策略等企业特性

### 1.2 解决方案要点
- **跳板机设计**：通过双网卡笔记本实现：
  - 外网侧：下载RKE2二进制文件、Harbor安装包、依赖镜像
  - 内网侧：搭建HTTP文件服务器同步资源
- **离线资源池**：
  - RKE2 v1.26.5+ 离线包
  - Harbor v2.7.0+ 离线安装包
  - 预下载Kubernetes组件镜像（约300个）
- **网络规划**：

<img src="/images/k8s/deploy-network.svg" />

---

## 架构设计与组件分布

### 2.1 逻辑架构图
<img src="/images/k8s/deploy-logic.svg" />


### 2.2 节点规格建议
| 角色类型 | CPU  | 内存 | 磁盘 | 数量 | 网络要求 |
|---------|------|-----|-----|-----|---------|
| Server  | 4+ | 8G+ | 100GB+ x3 | 3 | 1Gbps 内网 |
| Agent   | 8+ | 16G+| 200GB+ | N | 10Gbps 内网 |
| Harbor  | 4  | 8G  | 1TB   | 1 | 独立存储网络 |

---

## 关键配置与问题排查

### 3.1 RKE2数据目录迁移
修改`/etc/rancher/rke2/config.yaml`指定数据目录到数据盘：
```yaml
data-dir: /data/rke2
```

### 3.2 指定Ingress节点
1. 给目标节点打标签：
   ```bash
   kubectl label node node01 role=server
   ```
2. 部署定制化Ingress, 创建`/data/rke2/server/manifests/rke2-ingress-nginx-config.yaml`：
   ```yaml
    apiVersion: helm.cattle.io/v1
    kind: HelmChartConfig
    metadata:
    name: rke2-ingress-nginx
    namespace: kube-system
    spec:
    valuesContent: |-
    controller:
      nodeSelector:
        role: server
   ```
3. 重启`rke2-server`服务

### 3.3 NFS StorageClass配置

1. 在K8S所有节点上安装nfs客户端工具：
``` bash
sudo apt-get update
sudo apt-get install nfs-common
```

2. 获取并安装Helm Chart
``` bash
$ helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
$ helm install nfs-subdir-external-provisioner \
  -n storage \
  --create-namespace \
  --set nfs.server=NFS_SERVER_IP \
  --set nfs.path=/data/exports/k8s \
  --set storageClass.name=nfs \
  --set storageClass.defaultClass=true \
  nfs-subdir-external-provisioner/nfs-subdir-external-provisioner
```

3. 验证是否成功
``` yaml
# pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: test-pvc
spec:
  storageClassName: nfs
  accessModes: [ReadWriteMany]
  resources:
    requests:
      storage: 1Gi
```
``` bash
$ kubectl apply -f pvc.yaml
$ ssh root@nfs-server ls /data/exports/k8s
default-test-pvc-pvc-e4c14e5b-e820-4ca0-8bc0-f08d40e333ae
$ kubectl delete -f pvc.yaml
```


---

## 参考链接
1. [RKE2离线安装官方文档](https://docs.rke2.io/install/airgap)
2. [Harbor离线部署指南](https://goharbor.io/docs/2.7.0/install-config/)
3. [NFS Subdir Provisioner项目](https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner)
4. [Kubernetes生产检查清单](https://kubernetes.io/docs/setup/production-environment/)

> 注：所有操作建议先在预发布环境验证，生产部署前做好etcd备份。可通过`rke2 etcd-snapshot save`创建集群快照。
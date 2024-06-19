---
title: MicroK8s에 Worker Node 추가하기
date: 2024-05-01 23:41:11 +0900
author: kkamji
categories: [Kubernetes]
tags: [kubernetes, k8s, k8s-cluster, cluster, microk8s, aws, ec2, tls, ssl, kubeconfig, worker node]     # TAG names should always be lowercase
comments: true
image:
  path: /assets/img/kubernetes/kubernetes.webp
---

> 저번시간에는 [EC2상의 MicroK8s Cluster에 Local에서 명령하기](https://kkamji98.github.io/posts/MicroK8s-Local%EC%97%90%EC%84%9C-%EB%AA%85%EB%A0%B9/) Local에서 EC2위에 있는 MicroK8s의 kubeconfig를 가져와 명령하는 방법을 다뤄보았습니다. 이번 포스트에서는 MicroK8s에 Worker Node를 추가하는 실습을 다뤄보겠습니다.
{: .prompt-info}

## 1. EC2 Worker Node 생성

Spec

- t2.micro
- Ubuntu 24.04 LTS

![image](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/96396a50-9f2e-4def-a734-460e8d28a978)

## 2. Microk8s 설치 ( Worker Node )

```bash
sudo snap install microk8s --classic
```

## 3. 클러스터 조인 명령 생성 ( Master Node )

```bash
root@ip-10-0-0-241:~# microk8s add-node
From the node you wish to join to this cluster, run the following:
microk8s join 10.0.0.241:25000/5da02d8092097265cffdf4e47433bdea/2618bff96bd6

Use the '--worker' flag to join a node as a worker not running the control plane, eg:
microk8s join 10.0.0.241:25000/5da02d8092097265cffdf4e47433bdea/2618bff96bd6 --worker

If the node you are adding is not reachable through the default interface you can use one of the following:
microk8s join 10.0.0.241:25000/5da02d8092097265cffdf4e47433bdea/2618bff96bd6
```

## 4. 클러스터에 조인 ( Worker Node )

> 조인 명령어를 보니 Master Node의 **25000번 포트**가 클러스터 조인에 사용된다는 것을 알 수 있습니다. 따라서 Worker Node가 Master Node의 25000번 포트로 접근할 수 있도록 인바운드 규칙을 수정해줘야 합니다.
{: .prompt-info}

```bash
root@ip-10-0-0-81:~# microk8s join 10.0.0.241:25000/5da02d8092097265cffdf4e47433bdea/2618bff96bd6 --worker
Contacting cluster at 10.0.0.241

The node has joined the cluster and will appear in the nodes list in a few seconds.

This worker node gets automatically configured with the API server endpoints.
If the API servers are behind a loadbalancer please set the '--refresh-interval' to '0s' in:
    /var/snap/microk8s/current/args/apiserver-proxy
and replace the API server endpoints with the one provided by the loadbalancer in:
    /var/snap/microk8s/current/args/traefik/provider.yaml

Successfully joined the cluster.
```

## 5. 확인 ( Master Node )

```bash
root@ip-10-0-0-241:~# microk8s kubectl get nodes
NAME            STATUS   ROLES    AGE    VERSION
ip-10-0-0-241   Ready    <none>   18h    v1.29.4
ip-10-0-0-81    Ready    <none>   3m4s   v1.29.2
```

## 6. Pod가 Worker Node에 생성되는지 확인

```bash
root@ip-10-0-0-241:~/microk8s# kubectl create deployment nginx --image=nginx --replicas=4
deployment.apps/nginx created
root@ip-10-0-0-241:~/microk8s# kubectl get pods -o wide
NAME                     READY   STATUS    RESTARTS   AGE   IP            NODE            NOMINATED NODE   READINESS GATES
nginx-7854ff8877-9m55j   1/1     Running   0          27s   10.1.84.200   ip-10-0-0-241   <none>           <none>
nginx-7854ff8877-flx7p   1/1     Running   0          27s   10.1.41.1     ip-10-0-0-81    <none>           <none>
nginx-7854ff8877-fnmlp   1/1     Running   0          27s   10.1.84.199   ip-10-0-0-241   <none>           <none>
nginx-7854ff8877-sjzkp   1/1     Running   0          27s   10.1.41.2     ip-10-0-0-81    <none>           <none>
```

> pod가 Master Node와 Worker Node에 분산되어 생성된 것을 확인할 수 있습니다.
{: .prompt-info}

### 마무리

> 무심코 25000번 포트를 열어주지 않거나 Worker Node를 Private Subnet에 위치시킬경우 NAT Gateway나 NAT Instance를 사용해 인스턴스가 MicroK8s는 다운 받을 수 있도록 해야합니다. 저 처럼 헤메지 않도록... 하세요  
> **궁금하신점이나 추가해야할 부분은 댓글이나 아래의 링크를 통해 문의해주세요.**  
> **Written with [KKam.\_\.Ji](https://www.instagram.com/kkam._.ji/)**
{: .prompt-tip}

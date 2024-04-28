---
title: EC2위에 Kubernetes Cluster 구축하기 (MicroK8s)
date: 2024-04-27 22:54:32 +0900
author: kkankkandev
categories: [Kubernetes]
tags: [kubernetes, k8s, k8s-cluster, cluster, microk8s, aws, ec2]     # TAG names should always be lowercase
comments: true
# image:
#   path: https://github.com/War-Oxi/war-oxi.github.io/assets/72260110/c39504c6-3de4-4b41-919b-5ef1b132106c
---

> 필자는 맥북과 윈도우 데스크탑 환경을 사용하고 있습니다. EKS Cluster의 context를 가져와 하나의 Kubernetes 클러스터를 두 환경에서 공유해서 사용하고 있었으나….  
> 문제는 EKS를 개인 실습용으로 지속적으로 사용하기에는 비용이.. ㅠㅠ 너무 비쌌습니다.  
> 이를 해결하기 위해 Free-Tier인 t2.micro로 Minikube, K3s, MicroK8s 등을 구축하려했으나..  
> Minikube는 RAM이 4GB이상이 아니면 애초에 설치가 불가능 하였고,, K3s는 실행 도중 credit을 다쓰자 인스턴스가 죽는… 불상사가 발생했습니다.  
> 마지막으로 MicroK8s를 사용해서 구축에 성공하였지만 항상 CPU Utilization이 100%.. 명령어 하나 사용할 때 마다 1~3분정도 기다려야 했습니다..  
> 참다참다 돈을 조금 내더라도 더 좋은 성능을 사용하고자 t4g.small 인스턴스를 선택했습니다.  

시작에 앞서 t4g.small의 스펙입니다. [ 2 vCPU, 2G RAM ]

다음으로 MicroK8s의 권장 스펙입니다.

![pic1](https://github.com/War-Oxi/war-oxi.github.io/assets/72260110/851e5fb2-d785-47fb-9014-22a798aa11e9)

> 4G의 memory가 필요하긴하지만… 일단 해보자

## 1. snap 패키지 매니저 설치

```bash
sudo apt update
sudo install snapd

##test
$ sudo snap install hello-world
hello-world 6.4 from Canonical✓ installed
$ hello-world
Hello World!
```

## 2. MicroK8s 설치

```bash
sudo snap install microk8s --classic --channel=1.29
```

## 3. 유저 권한 부여

```bash
sudo usermod -a -G microk8s $USER
sudo mkdir -p ~/.kube
sudo chown -f -R $USER ~/.kube
```

## 4. MicroK8s 상태 체크

```bash
root@i-0fefad06e169df0c3:~# microk8s status --wait-ready
microk8s is running
high-availability: no
  datastore master nodes: 127.0.0.1:19001
  datastore standby nodes: none
addons:
  enabled:
    dns                  # (core) CoreDNS
    ha-cluster           # (core) Configure high availability on the current node
    helm                 # (core) Helm - the package manager for Kubernetes
    helm3                # (core) Helm 3 - the package manager for Kubernetes
  disabled:
    cert-manager         # (core) Cloud native certificate management
    cis-hardening        # (core) Apply CIS K8s hardening
    community            # (core) The community addons repository
    dashboard            # (core) The Kubernetes dashboard
    host-access          # (core) Allow Pods connecting to Host services smoothly
    hostpath-storage     # (core) Storage class; allocates storage from host directory
    ingress              # (core) Ingress controller for external access
    kube-ovn             # (core) An advanced network fabric for Kubernetes
    mayastor             # (core) OpenEBS MayaStor
    metallb              # (core) Loadbalancer for your Kubernetes cluster
    metrics-server       # (core) K8s Metrics Server for API access to service metrics
    minio                # (core) MinIO object storage
    observability        # (core) A lightweight observability stack for logs, traces and metrics
    prometheus           # (core) Prometheus operator for monitoring and logging
    rbac                 # (core) Role-Based Access Control for authorisation
    registry             # (core) Private image registry exposed on localhost:32000
    rook-ceph            # (core) Distributed Ceph storage using Rook
    storage              # (core) Alias to hostpath-storage add-on, deprecated

```

## 5. Cluster에 접근해보기

```bash
root@i-0fefad06e169df0c3:~# microk8s kubectl get all --all-namespaces
NAMESPACE     NAME                                         READY   STATUS              RESTARTS   AGE
kube-system   pod/calico-kube-controllers-77bd7c5b-fzlmj   0/1     ContainerCreating   0          36s
kube-system   pod/calico-node-7xpv4                        1/1     Running             0          36s
kube-system   pod/coredns-864597b5fd-npczb                 1/1     Running             0          36s

NAMESPACE     NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                  AGE
default       service/kubernetes   ClusterIP   10.152.183.1    <none>        443/TCP                  44s
kube-system   service/kube-dns     ClusterIP   10.152.183.10   <none>        53/UDP,53/TCP,9153/TCP   40s

NAMESPACE     NAME                         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-system   daemonset.apps/calico-node   1         1         1       1            1           kubernetes.io/os=linux   41s

NAMESPACE     NAME                                      READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/calico-kube-controllers   0/1     1            0           41s
kube-system   deployment.apps/coredns                   1/1     1            1           40s

NAMESPACE     NAME                                               DESIRED   CURRENT   READY   AGE
kube-system   replicaset.apps/calico-kube-controllers-77bd7c5b   1         1         0       36s
kube-system   replicaset.apps/coredns-864597b5fd                 1         1         1       36s
```

## 6. 대시보드 확인

```bash
root@i-0fefad06e169df0c3:~# microk8s dashboard-proxy
Checking if Dashboard is running.
Infer repository core for addon dashboard
Infer repository core for addon metrics-server
Waiting for Dashboard to come up.
Trying to get token from microk8s-dashboard-token
Waiting for secret token (attempt 0)
Dashboard will be available at https://127.0.0.1:10443
Use the following token to login:
eyJhbGciOiJSUzI1NiIsImtpZCI6ImVHaExXbGJtQUpjMHc0SFl5SHMtWGxKODQ3LUdOcEZFRnNTSVRaNWYtdVkifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJtaWNyb2s4cy1kYXNoYm9hcmQtdG9rZW4iLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjI1NmM3YTJhLWI3YzUtNGZkMi04MjViLTQ3ZmQyN2YxMGZlOCIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlLXN5c3RlbTpkZWZhdWx0In0.zGwpupWmIilRG19ns6rMJar1ygiSbmgVwd9ejX5iSmZgM5u90zfazuSrKnONXWe05YFiiiOk7DVwj7M8l_6howEkj6VLfTimwrBTbKOCUaL-RQIVaTNlyVH_rubxFfUAPZKZD3uwIMiuqUdcYD4UmOQX3j2y6Gh6JDGbmudm227O_hkodLTWFOSzz9HmPaCiGIc0m8p5km6FhvKMIhjMkxrJGZKQBvVfO_5_cvWe6lh5QuifARwilkOH4iFCTPF7rvc6J1dkpo1outDkigk21vpx6YDjvkn08lAIVO7F_V7WiFCe_JqzNqAeceXRLSwupDV-kRQEtX1iucVxbPjbaQ
```

EC2가 아닌 로컬 컴퓨터에서 확인하기 위해 Security Group에서 10443 포트를 열고 해당 EC2의 10443포트에 브라우저로 접속을 시도했습니다. (맨 아랫줄의 token 입력 필수)

![pic2](https://github.com/War-Oxi/war-oxi.github.io/assets/72260110/b7877cf0-9d05-4a21-87e5-93083080250a)

![pic3](https://github.com/War-Oxi/war-oxi.github.io/assets/72260110/b5751561-ce04-425b-a759-10b496110f07)

## 7. alias

> microk8s kubectl명령어를 계속 사용하려면 번거로움이 있습니다. alias기능을 사용해 microctl로 해당 명령어를 단축시킬 수 있습니다. (kubectl로 하면 기존 kubectl 명령어와 충돌이 일어날 가능성을 염두해 microctl로 이름을 지정했습니다)

```bash
alias microctl='microk8s kubectl'
```

## 8. nginx 배포 테스트

> 간단하게 nginx 서버를 NodePort로 30080포트로 배포해보겠습니다

### 테스트용 nginx menifest 파일 (nginx_test.yml)

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30080
```

### 적용

```bash
root@i-0fefad06e169df0c3:~/microk8s# microctl apply -f nginx_test.yml
deployment.apps/nginx-deployment created
service/nginx-service created
```

### 확인

```bash
root@i-0fefad06e169df0c3:~/microk8s# kubectl get svc
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes      ClusterIP   10.152.183.1    <none>        443/TCP        95m
nginx-service   NodePort    10.152.183.56   <none>        80:30080/TCP   80s
```

![pic4](https://github.com/War-Oxi/war-oxi.github.io/assets/72260110/6b31c129-84cf-4cc4-928a-79dc7b850f86)

### 마무리

> 여러 우여곡절 끝에 구축에 성공했지만.. 내 시간이... ㅠㅠ 다음 포스트에서는 해당 Local에서 MicroK8s에 명령어를 내리는 것을 다뤄보겠습니다. (될지는 모르겠지만..?).  
> **궁금하신점이나 추가해야할 부분은 댓글이나 아래의 링크를 통해 문의해주세요.**  
> **Written with [KKam.\_\.Ji](https://www.instagram.com/kkam._.ji/)**
{: .prompt-tip}

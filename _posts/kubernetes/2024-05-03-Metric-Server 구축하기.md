---
title: Metric-Server란? (구축 방법)
date: 2024-05-03 23:35:15 +0900
author: kkankkandev
categories: [Kubernetes]
tags: [kubernetes, k8s, k8s-cluster, cluster, microk8s, aws, ec2, metric, metric server, worker node, monitoring]     # TAG names should always be lowercase
comments: true
# image:
#   path: https://github.com/War-Oxi/war-oxi.github.io/assets/72260110/c39504c6-3de4-4b41-919b-5ef1b132106c
---

> Kubernetes Cluster를 관리하기 위해서는 성능 metric을 모니터링하고 지표를 저장한 뒤 지표 데이터에 대한 분석을 제공할 수 있는 솔루션이 필요합니다. 오픈 소스 모니터링 솔루션에는 Metric Server, Prometheus, Elastic Stack, DataDog 등이 있지만 이번 시간에는 가장 기본적인 Metric Server를 클러스터에 설치해보도록 하겠습니다.  
{: .prompt-info}

## 1. Metric Server란?

> 클러스터 내의 모든 Node와 Pod에서 리소스 사용 데이터를 수집 후 kube-apiserver에 전달하는 역할을 합니다. 인메모리 솔루션(디스크에 Metric을 저장하지 않음)이며, Metric Data는 Horizontal Pod Autoscaler (HPA) 및 Vertical Pod Autoscaler (VPA)와 같은 기능을 사용할 때 필수적입니다.  
{: .prompt-info}

### 수집절차

1. kubelet이 자체 내장된 cAdvisor를 사용해 Node와 Pod의 리소스 데이터를 수집
2. kubelet이 /metrics/resource API 엔드포인트를 통해 리소스 데이터를 제공
3. Metric Server가 API를 통해 정기적으로 각 노드의 kubelet으로부터 Metric 데이터를 가져옴(Pull)

## 2. Metric Server의 주요 기능

1. 리소스 모니터링: 클러스터의 Node와 Pod에서 CPU와 메모리 사용량과 같은 Metric을 주기적으로 수집합니다.
2. 성능 및 확장성: 경량화된 메모리 사용량으로 설계되어 대규모 클러스터에서도 효과적으로 작동할 수 있도록 최적화되어 있습니다.
3. API 제공: 수집된 메트릭은 Kubernetes API를 통해 다양한 클라이언트와 서비스에서 사용할 수 있습니다.

## 3. Metric Server 설치

```bash
root@ip-10-0-0-241:~# kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
serviceaccount/metrics-server created
clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created
clusterrole.rbac.authorization.k8s.io/system:metrics-server created
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created
service/metrics-server created
deployment.apps/metrics-server created
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
```

## 4. Metric Server 확인

```bash
root@ip-10-0-0-241:~# kubectl get all -n kube-system
NAME                                         READY   STATUS    RESTARTS        AGE
pod/calico-kube-controllers-77bd7c5b-8pjg9   1/1     Running   4 (5m33s ago)   44h
pod/calico-node-rr2vn                        1/1     Running   0               26h
pod/calico-node-v7j2s                        1/1     Running   0               37h
pod/coredns-864597b5fd-dmzbb                 1/1     Running   2 (44h ago)     44h
pod/metrics-server-6d94bc8694-xs9d7          1/1     Running   1               8m35s

NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                  AGE
service/kube-dns         ClusterIP   10.152.183.10    <none>        53/UDP,53/TCP,9153/TCP   44h
service/metrics-server   ClusterIP   10.152.183.116   <none>        443/TCP                  9m26s

NAME                         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
daemonset.apps/calico-node   2         2         2       2            2           kubernetes.io/os=linux   44h

NAME                                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/calico-kube-controllers   1/1     1            1           44h
deployment.apps/coredns                   1/1     1            1           44h
deployment.apps/metrics-server            1/1     1            1           9m27s

NAME                                               DESIRED   CURRENT   READY   AGE
replicaset.apps/calico-kube-controllers-77bd7c5b   1         1         1       44h
replicaset.apps/coredns-864597b5fd                 1         1         1       44h
replicaset.apps/metrics-server-6d94bc8694          1         1         1       8m37s
```

## 5. 클러스터 Metric 확인

```bash
root@ip-10-0-0-241:~# kubectl top nodes
NAME            CPU(cores)   CPU%        MEMORY(bytes)   MEMORY%
ip-10-0-0-241   125m         6%          1136Mi          65%
ip-10-0-0-81    <unknown>    <unknown>   <unknown>       <unknown>
```

> ???? Master Node는 Metric이 확인이 되는데 Worker Node는 확인이 안된다.. 로그를 확인해볼까..?
{: .prompt-info}

### 로그 확인

```bash
root@ip-10-0-0-241:~# kubectl logs metrics-server-6d94bc8694-n5k29 -n kube-system
I0505 10:30:51.556250       1 serving.go:374] Generated self-signed cert (/tmp/apiserver.crt, /tmp/apiserver.key)
I0505 10:30:53.140866       1 handler.go:275] Adding GroupVersion metrics.k8s.io v1beta1 to ResourceManager
I0505 10:30:53.255850       1 secure_serving.go:213] Serving securely on [::]:10250
I0505 10:30:53.255945       1 dynamic_serving_content.go:132] "Starting controller" name="serving-cert::/tmp/apiserver.crt::/tmp/apiserver.key"
I0505 10:30:53.256539       1 tlsconfig.go:240] "Starting DynamicServingCertificateController"
I0505 10:30:53.256674       1 configmap_cafile_content.go:202] "Starting controller" name="client-ca::kube-system::extension-apiserver-authentication::client-ca-file"
I0505 10:30:53.256690       1 shared_informer.go:311] Waiting for caches to sync for client-ca::kube-system::extension-apiserver-authentication::client-ca-file
I0505 10:30:53.256711       1 requestheader_controller.go:169] Starting RequestHeaderAuthRequestController
I0505 10:30:53.256717       1 shared_informer.go:311] Waiting for caches to sync for RequestHeaderAuthRequestController
I0505 10:30:53.256934       1 configmap_cafile_content.go:202] "Starting controller" name="client-ca::kube-system::extension-apiserver-authentication::requestheader-client-ca-file"
I0505 10:30:53.256943       1 shared_informer.go:311] Waiting for caches to sync for client-ca::kube-system::extension-apiserver-authentication::requestheader-client-ca-file
I0505 10:30:53.357432       1 shared_informer.go:318] Caches are synced for client-ca::kube-system::extension-apiserver-authentication::requestheader-client-ca-file
I0505 10:30:53.357538       1 shared_informer.go:318] Caches are synced for client-ca::kube-system::extension-apiserver-authentication::client-ca-file
I0505 10:30:53.357607       1 shared_informer.go:318] Caches are synced for RequestHeaderAuthRequestController
E0505 10:31:03.251258       1 scraper.go:147] "Failed to scrape node, timeout to access kubelet" err="Get \"https://10.0.0.81:10250/metrics/resource\": context deadline exceeded" node="ip-10-0-0-81" timeout="10s"
I0505 10:31:10.252532       1 server.go:191] "Failed probe" probe="metric-storage-ready" err="no metrics to serve"
E0505 10:31:18.254969       1 scraper.go:147] "Failed to scrape node, timeout to access kubelet" err="Get \"https://10.0.0.81:10250/metrics/resource\": context deadline exceeded" node="ip-10-0-0-81" timeout="10s"
E0505 10:31:33.249591       1 scraper.go:147] "Failed to scrape node, timeout to access kubelet" err="Get \"https://10.0.0.81:10250/metrics/resource\": context deadline exceeded" node="ip-10-0-0-81" timeout="10s"
```

> 메트릭을 수집할 때 10250 포트를 사용하는 것 같다. 10250 포트를 열어주고 다시 확인해볼까?
{: .prompt-info}

```bash
root@ip-10-0-0-241:~# kubectl top nodes
NAME            CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
ip-10-0-0-241   135m         6%     1230Mi          70%
ip-10-0-0-81    48m          4%     470Mi           54%

root@ip-10-0-0-241:~# kubectl top pods --all-namespaces
NAMESPACE      NAME                                      CPU(cores)   MEMORY(bytes)
cert-manager   cert-manager-7cf97bbd47-mzdjj             1m           15Mi
cert-manager   cert-manager-cainjector-99677759d-rnlj9   1m           20Mi
cert-manager   cert-manager-webhook-8486cb8479-nmdst     2m           13Mi
ingress        nginx-ingress-microk8s-controller-b9k5x   1m           75Mi
ingress        nginx-ingress-microk8s-controller-z22tp   1m           66Mi
kube-system    calico-kube-controllers-77bd7c5b-8pjg9    1m           19Mi
kube-system    calico-node-rr2vn                         15m          89Mi
kube-system    calico-node-v7j2s                         16m          76Mi
kube-system    coredns-864597b5fd-dmzbb                  1m           17Mi
kube-system    metrics-server-6d94bc8694-n5k29           3m           28Mi
```

> 정상적으로 Node와 Pod들의 Metric을 수집하는 것을 확인할 수 있습니다.  
{: .prompt-info}

### 마무리

> 무심코 25000번 포트를 열어주지 않거나 Worker Node를 Private Subnet에 위치시킬경우 NAT Gateway나 NAT Instance를 사용해 인스턴스가 MicroK8s는 다운 받을 수 있도록 해야합니다. 저 처럼 헤메지 않도록... 하세요  
> **궁금하신점이나 추가해야할 부분은 댓글이나 아래의 링크를 통해 문의해주세요.**  
> **Written with [KKam.\_\.Ji](https://www.instagram.com/kkam._.ji/)**
{: .prompt-tip}

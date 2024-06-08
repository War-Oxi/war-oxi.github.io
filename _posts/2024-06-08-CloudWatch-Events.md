---
title: Horizontal Pod Autoscaling 구성, 원리
date: 2024-06-08 08:31:44 +0900
author: kkamji
categories: [AWS]
tags: [route53, domain, aws, github pages, jekyll, blog, kkamji, cname, a record]     # TAG names should always be lowercase
comments: true
# image:
#   path: https://github.com/kkamji98/kkamji98.github.io/assets/72260110/c39504c6-3de4-4b41-919b-5ef1b132106c
---
<https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/>

Kubernetes의 Horizontal Pod Autoscaler (HPA)는 클러스터에서 실행 중인 애플리케이션의 스케일링을 자동화하는 메커니즘입니다. HPA는 CPU 사용률, 메모리 사용률 등 다양한 메트릭을 기반으로 필요에 따라 Pod의 수를 자동으로 조정합니다. 이를 통해 애플리케이션의 부하를 효과적으로 관리하고 리소스를 최적화할 수 있습니다.

## 작동 원리

Kubernetes에서 HPA는 특정 시간마다 Metrics Server를 통해 매트릭을 확인하고 Pods를 Auto Scaling합니다.  

metrics.k8s.io 또는 custom.metrics.k8s.io 또는 external.metrics.k8s.io

> 실행 주기는 kube-controller-manager의 **--horizontal-pod-autoscaler-sync-period** 파라미터에 의해 설정됩니다. (기본 주기 = 15초)
{. :prompt-info}

## 시나리오

> k8s.ger.io/hpa-example 이미지를 사용해 평균 3개의 Pods를 동작시키는 Deployment를 생성한다.  
> 해당 Deployment의 Pods의 개수를 리소스 사용량에 따라 최소 1개, 최대 5개로 Auto Scaling 되도록 설정 후 테스트
{. :prompt-info}

## 1. Metrics Server 동작 확인

```bash
root@Zest ~# kubectl get pods -n kube-system
NAME                                      READY   STATUS    RESTARTS     AGE
calico-kube-controllers-796fb75cc-lr82z   1/1     Running   2 (9d ago)   9d
calico-node-z98m9                         1/1     Running   2 (9d ago)   9d
coredns-5986966c54-8hzs4                  1/1     Running   2 (9d ago)   9d
metrics-server-7c5c4b56bd-pb6t9           1/1     Running   0            12m
```
## 2. 테스트용 어플리케이션 배포

```yaml #pod-definition.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: php-apache
spec:
  selector:
    matchLabels:
      run: php-apache
  replicas: 1
  template:
    metadata:
      labels:
        run: php-apache
    spec:
      containers:
      - name: php-apache
        image: registry.k8s.io/hpa-example
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 500m
          requests:
            cpu: 200m
---
apiVersion: v1
kind: Service
metadata:
  name: php-apache
  labels:
    run: php-apache
spec:
  ports:
  - port: 80
  selector:
    run: php-apache
```

> **궁금하신점이나 추가해야할 부분은 댓글이나 아래의 링크를 통해 문의해주세요.**  
> **Written with [KKam.\_\.Ji](https://www.instagram.com/kkam._.ji/)**
{: .prompt-info}

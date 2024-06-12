---
title: Network Policy 개념, 실습
date: 2024-06-11 22:31:44 +0900
author: kkamji
categories: [Kubernetes]
tags: [kubernetes, network policy, 3-tier-architecture, web, was, db]     # TAG names should always be lowercase
comments: true
# image:
#   path: https://github.com/kkamji98/kkamji98.github.io/assets/72260110/c39504c6-3de4-4b41-919b-5ef1b132106c
---

> WEB, WAS, DB 네임스페이스 생성.  
>
> WEB <-> WAS <-> DB 이렇게만 통신이 가능해야함  
>
> WEB - 80 포트로만 ingress, egress  
> WAS - 8080 포트로만 ingress, egress  
> DB - 3306 포트로만 ingress, egress  
{: .prompt-info}

![alt text](/assets/img/network-policy/network-policy.png)

## 1. Namespace 및 label 생성

```bash
root@Zest ~# kubectl create ns web
namespace/web created
root@Zest ~# kubectl create ns was
namespace/was created
root@Zest ~# kubectl create ns db
namespace/db created

root@Zest ~/Code/k8s-network-policy-3-tier/test# kubectl label namespace db name=db
namespace/db labeled
root@Zest ~/Code/k8s-network-policy-3-tier/test# kubectl label namespace was name=was
namespace/was labeled
root@Zest ~/Code/k8s-network-policy-3-tier/test# kubectl label namespace web name=web
namespace/web labeled
```

## 2. Pod 및 Service 생성

### WEB manifest file

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-server
  namespace: web
  labels:
    name: web-server
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
  name: web-service
  namespace: web
spec:
  selector:
    name: web-server
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

### WAS manifest file

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: was-server
  namespace: was
  labels:
    name: was-server
spec:
  containers:
  - name: tomcat
    image: tomcat:latest
    ports:
      - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: was-service
  namespace: was
spec:
  selector:
    name: was-server
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
```

### DB manifest file

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: db-server
  namespace: db
  labels:
    name: db-server
spec:
  containers:
  - name: mysql
    image: arm64v8/mysql:latest
    ports:
      - containerPort: 3306
    env:
      - name: MYSQL_ROOT_PASSWORD
        value: "test"
      - name: MYSQL_DATABASE
        value: "mydb"
---
apiVersion: v1
kind: Service
metadata:
  name: db-service
  namespace: db
spec:
  selector:
    name: db-server
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
```

### Pod 생성 & 확인

```bash
root@Zest ~/Code/k8s-network-policy-3-tier/web# kubectl apply -f .
pod/web-server created
service/web-service created

root@Zest ~/Code/k8s-network-policy-3-tier/was# kubectl apply -f .
pod/was-server created
service/was-service created

root@Zest ~/Code/k8s-network-policy-3-tier/db# kubectl apply -f .
pod/db-server created
service/db-service created

root@Zest ~/Code/k8s-network-policy-3-tier/db# kubectl get all -A
NAMESPACE      NAME                                          READY   STATUS    RESTARTS       AGE
db             pod/db-server                                 1/1     Running   0              28s
was            pod/was-server                                1/1     Running   0              37s
web            pod/web-server                                1/1     Running   0              51s
...

NAMESPACE      NAME                           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                  AGE
db             service/db-service             ClusterIP   10.152.183.88    <none>        3306/TCP                 28s
was            service/was-service            ClusterIP   10.152.183.155   <none>        8080/TCP                 37s
web            service/web-service            ClusterIP   10.152.183.96    <none>        80/TCP                   51s
...
```

## 3. Network Policy 생성

### WEB-Network Policy manifest file

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: web-policy
  namespace: web
spec:
  podSelector:
    matchLabels: {}
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: was
    ports:
    - protocol: TCP
      port: 80
  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          name: was
    ports:
    - protocol: TCP
      port: 8080
  - to:
    - namespaceSelector:
        matchLabels: {}
    ports:
    - protocol: UDP
      port: 53
    - protocol: TCP
      port: 53

```

### WAS-Network Policy manifest file

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: was-policy
  namespace: was
spec:
  podSelector:
    matchLabels: {}
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: web
    ports:
    - protocol: TCP
      port: 8080
  - from:
    - namespaceSelector:
        matchLabels:
          name: db
    ports:
    - protocol: TCP
      port: 3306
  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          name: web
    ports:
    - protocol: TCP
      port: 80
  - to:
    - namespaceSelector:
        matchLabels:
          name: db
    ports:
    - protocol: TCP
      port: 3306
  - to:
    - namespaceSelector:
        matchLabels: {}
    ports:
    - protocol: UDP
      port: 53
    - protocol: TCP
      port: 53

```

### DB-Network Policy manifest file

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
  namespace: db
spec:
  podSelector:
    matchLabels: {}
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: was
    ports:
    - protocol: TCP
      port: 3306
  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          name: was
    ports:
    - protocol: TCP
      port: 8080
  - to:
    - namespaceSelector:
        matchLabels: {}
    ports:
    - protocol: UDP
      port: 53
    - protocol: TCP
      port: 53
```

### Network Policy 생성 & 확인

```bash
root@Zest ~/Code/k8s-network-policy-3-tier/Network-Policy# kubectl apply -f web-policy.yaml 
networkpolicy.networking.k8s.io/web-policy created
root@Zest ~/Code/k8s-network-policy-3-tier/Network-Policy# kubectl apply -f was-policy.yaml 
networkpolicy.networking.k8s.io/was-policy created
root@Zest ~/Code/k8s-network-policy-3-tier/Network-Policy# kubectl apply -f db-policy.yaml 
networkpolicy.networking.k8s.io/db-policy created

root@Zest ~/Code/k8s-network-policy-3-tier/Network-Policy# kubectl get netpol -A
NAMESPACE   NAME         POD-SELECTOR   AGE
db          db-policy    <none>         19s
was         was-policy   <none>         22s
web         web-policy   <none>         25s
```

## 4. Test

> busybox 이미지를 사용했고, 각각의 namespace에 test pod를 생성해 테스트 했습니다.
{: .prompt-info}

### web namespace pod에서 연결 확인

```bash
root@Zest ~/Code/k8s-network-policy-3-tier# kubectl exec -it -n web web-test-pod -- sh
/ # wget -qO- was-service.was.svc.cluster.local:8080
wget: server returned error: HTTP/1.1 404 

/ # nc -zv db-service.db.svc.cluster.local 3306
nc: db-service.db.svc.cluster.local (10.152.183.88:3306): Connection timed out
```

### was namespace pod에서 연결 확인

```bash
root@Zest ~/Code/k8s-network-policy-3-tier# kubectl exec -it -n was was-test-pod -- sh
/ # wget -qO- web-service.web.svc.cluster.local:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

/ # nc -zv db-service.db.svc.cluster.local 3306
db-service.db.svc.cluster.local (10.152.183.88:3306) open
```

### db namespace pod에서 연결 확인

```bash
root@Zest ~/Code/k8s-network-policy-3-tier# kubectl exec -it -n db db-test-pod -- sh
/ # wget -qO- was-service.was.svc.cluster.local:8080
wget: server returned error: HTTP/1.1 404 

/ # wget -qO- web-service.web.svc.cluster.local:80
wget: can't connect to remote host (10.152.183.144): Connection timed out
```

## 5. 결과

web → was ( O )

web → db ( X )

was → web ( O )

was → db ( O )

db → web ( X )

db → was ( O )

## 6. Trouble Shooting

네임스페이스를 생성할 때, 네임스페이스에 레이블이 자동으로 추가되지 않는다는 사실과 network policy를 생성 직후 pod에서 다른 pod에 DNS 이름으로 접근이 불가능 하다는 문제를 직면했습니다.

### 해결과정

1. 네임스페이스를 생성하고 네임스페이스 셀렉터를 사용하여 네트워크 정책을 생성했으나, 기대한 대로 통신이 되지 않음
2. Calico 네트워크 플러그인의 문제를 의심하여 Cilium으로 교체했으나, 여전히 문제가 해결되지 않음
3. 네임스페이스에 적절한 레이블이 설정되지 않아 네임스페이스 셀렉터가 작동하지 않았음.
4. 레이블 확인
    ```bash
    kubectl get namespaces --show-labels
    # [kubernetes.io/metadata.name=web]와 같은 형식으로 나타남.
    ```
5. 네임스페이스에 올바른 레이블 설정
    ```bash
    kubectl label namespace db name=db
    kubectl label namespace was name=was
    kubectl label namespace web name=web
    ```
6. 네임스페이스에 레이블을 설정했음에도 불구하고 문제가 해결되지 않음
7. Pod 내부에서 `nslookup` 명령어를 사용하여 DNS 이름으로 접근을 시도 ⇒ DNS 이름으로 접근이 불가능한 것을 확인
    ```bash
    kubectl exec -n web web-test-pod -- nslookup was-service.was.svc.cluster.local
    ```
8. DNS 트래픽을 명시적으로 허용하지 않으면 DNS 이름으로 접근할 수 없다는 사실 확인
  <https://www.cncf.io/blog/2020/02/10/guide-to-kubernetes-egress-network-policies/>
9.  네트워크 정책에 DNS에 대한 egress 규칙을 추가
    ```yaml
    egress:
    - to:
        - namespaceSelector:
            matchLabels: {}
        ports:
        - protocol: UDP
          port: 53
        - protocol: TCP
          port: 53
    ```

### 결론

네임스페이스를 생성할 때 레이블이 자동으로 설정되지 않으므로, 네트워크 정책을 설정할 때 반드시 레이블을 수동으로 설정해야 합니다. 또한, DNS 트래픽을 명시적으로 허용하지 않으면 DNS 이름으로 접근할 수 없습니다.

> **궁금하신점이나 추가해야할 부분은 댓글이나 아래의 링크를 통해 문의해주세요.**  
> **Written with [KKam.\_\.Ji](https://www.instagram.com/kkam._.ji/)**
{: .prompt-info}

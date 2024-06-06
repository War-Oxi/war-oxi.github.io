---
title: EC2위의 MicroK8s Cluster에 Local에서 명령 하기
date: 2024-04-27 16:34:31 +0900
author: kkamji
categories: [Kubernetes]
tags: [kubernetes, k8s, k8s-cluster, cluster, microk8s, aws, ec2, tls, ssl, kubeconfig]     # TAG names should always be lowercase
comments: true
# image:
#   path: https://github.com/kkamji98/kkamji98.github.io/assets/72260110/c39504c6-3de4-4b41-919b-5ef1b132106c
---

> 저번 시간에 EC2위에 MicroK8s Cluster를 구축후 web server를 배포하는 것까지 다뤘습니다..  
> 하지만 MicroK8s를 구축한 이유는..? 데스크탑과 맥북에서 해당 클러스터를 공유해서 사용하기 위해!!  
> 이번에는 EC2의 kubeconfig파일을 Local에 가져와 kubectl 명령을 하는 것을 목표로 진행해보겠습니다.  
{: .prompt-tip}  
> 주의! Local에서 EC2에 kubectl 명령어를 내리기 위해서는 kube-apiserver 포트인 16443 포트를 인바운드 규칙에 허용해주어야 합니다.  
{: .prompt-tip}

## 1. MicroK8s의 kubeconfig 파일 확인하기

```bash
root@i-0fefad06e169df0c3:~/microk8s# microk8s config > kubeconfig
root@i-0fefad06e169df0c3:~/microk8s# cat kubeconfig
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTS0tCk1JSUREekNDQWZlZ0F3SUJBZ0lVV3pXM0laNzBBcm9yaTJjanB1ckNYbi94bE9vd0RRWUpLb1pJaHZjTkFRRUwKQlFBd0Z6RVZNQk1HQTFVRUF3d01NVEF1TVRVeUxqRTRNeTR4TUI0WERUSTBNRFF5T0RBMk5EUXhNbG9YRFRNMApNRFF5TmpBMk5EUXhNbG93RnpFVk1CTUdBMVVFQXd3TU1UQXVNVFV5TGpFNE15NHhNSUlCSWpBTkJna3Foa2lHCjl3MEJBUUVGQUFPQ0FROEFNSUlCQ2dLQ0FRRUEwd1dab3Rib3kxcVE0VjRpZk1SbGJIV1hCVktNblM4K2NMcWEKcTJPcnVkZ045c0lPbDFlMTlVOWxSYmNTQmsycVNzdnYyUVFpOU54akRSTlJGNzN6WkNmSEFOV2MyeE8rcmxJYQpZM3BLNmJpY3pidlkzRzZDaW8yNTFJckhLb0FRYlVmdG5VNXZpRGZOdFZsL2hlUXVLQUdFeWQ1cEUrMW5VN0dXCjBOMWJodVlQcnUycERFcThNNFIzTVBPaWpMUlVDSE9KTGt2YlU3aUUxUGcybHFXZit3S1JyOHRLbmV6ZHhjZXkKb2NYWllJSjFYaWk1cEJacHc1bmNBVHQrOHluR1hXY1ZFZDJUTG1FbjJHRDlXYnFvQldHbmw2VDdxTXJoTTFMagowODRxanp6NWhQaGZWM1JOV1FEcFZhK3lnWHkxbS9jdWpGNGUwYUFSRVpmMUJEN2kvd0lEQVFBQm8xTXdVVEFkCkJnTlZIUTRFRmdRVTF0TlJqZVAyUkJveFlpOHBJS20xQk1ueE9NY3dId1lEVlIwakJCZ3dGb0FVMXROUmplUDIKUkJveFlpOHBJS20xQk1ueE9NY3dEd1lEVlIwVEFRSC9CQVV3QXdFQi96QU5CZ2txaGtpRzl3MEJBUXNGQUFPQwpBUUVBYU1XT21aenFiTUpWTnUrditmeUhPOStjWUdXaUtDTFNLR1FrbW45Um40aUhrQlluOHBpNlVLRks0T2tGCnFCNi91ekxEdUJwK2s5bmVuemQ0UXJMVVNDcktua1RnSXkrdnBld2x1L1VSLzNFVG1MVmREQXFLNU1NeE13QWUKUnI4elVOTVhmSTJ6NnErTzJVOFlpOXlmYUROTXptVzJBcE9EUy82a3lCcEdjNHNrNWNKQVhPRldWeVdQUEVJVgpkN0oxUVBpUENVMXdDWURZTHEzc0lJdmUyMU1EUnhySXptTGo5K1B1MXptSGlCdzJkeTI3WnZPY25SaysrLys5ClBPckNCaTFTbUpFUXVSaG1FQmtWRmN6Y2JvT2ZjZHN4b1llSnJ0U0taMkJaU1NOdmo3NWdybXB3Ly9hSVZPclYKaDIwV3VIMWE1RTJaUGdUZzdrL1RnNU5sWlE9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    server: https://10.0.0.35:16443
  name: microk8s-cluster
contexts:
- context:
    cluster: microk8s-cluster
    user: admin
  name: microk8s
current-context: microk8s
kind: Config
preferences: {}
users:
- name: admin
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCTZ0F3SUJBZ0lVTnFmMDBhRUFsdldKdkdRc0t2RnFhdTRjdS84d0RRWUpLb1pJaHZjTkFRRUwKQlFBd0Z6RVZNQk1HQTFVRUF3d01NVEF1TVRVeUxqRTRNeTR4TUI0WERUSTBNRFF5T0RBMk5EUXhNMW9YRFRNMApNRFF5TmpBMk5EUXhNMW93S1RFT01Bd0dBMVVFQXd3RllXUnRhVzR4RnpBVkJnTlZCQW9NRG5ONWMzUmxiVHB0CllYTjBaWEp6TUlJQklqQU5CZ2txaGtpRzl3MEJBUUVGQUFPQ0FROEFNSUlCQ2dLQ0FRRUFxeTZSRzZnMm1HbkwKdWFOV0hXQXN5YXhRRXk1QjVnd2o2dDN1bDEySnlPMUsrNGlRcGRDTVNuTll5QTlnRlkyenMvcVRzSHZzN25Ubgpqb2NYa1BydEVLUHJYWU1pOGxBcVMyK3NMRS9jNUNodXZlaDFLSU84U3hENzlIc091L3B0OWNxZlJzL3lpbm14CjVkQWVjSVNNQU5RMjROQ1kvQmhvOW1YbU42TzlXeW9veStybENTaXJIQjkvMk9VVXlhWDFPL0JhQXc4MmJsRWQKM3NiYVVBWTJLYVF0c2VWUnhpamlIZjdOVFMyOHJoR1M2dDJLL2hHZTlWTUR4d0RMVXE2Rm4ybE1IWE13SFg2VQpQaUFBUW5USWhpcHBKRjFpc2JMVHdqUjJ3eUZ1amdLUEpWYVY3UjIwN2l0N2lTR0NBNTJtSVA0Unl4U0Q2VEU4Cmx0WVpNM01KMHdJREFRQUJNQTBHQ1NxR1NJYjNEUUVCQ3dVQUE0SUJBUUNDM3Q0aGZRb1RQd2lKcThwV2NCMHAKaFNYL0ZqWE82SW5tREFTRURrbmFUV2lKalk5UnZFRU4yZGJRa3BHSEd3MGFMc2toRWZoOUZLTysyNVpsMFY0eAo3cEVhM3FlaDRaS2k2TWdJM3l1THI5TndqWElZWWpvSUhhNzMzUU5aTGZqREVZQWFTeklDRmNleVJFQUtCSW8yCkE1ZmRkR2gzL2g3Yk5nZzhidWN6QlpKTWdWdGpJQk9sZkFlVXRwa0FxbXFEVEk5cVdoS05rcVFPZ0FTVnozSUwKT3lpajNxNktMaXBFdkhZK2Z5ZS9UNTY3VERQYXpGOWhNSSthTlFTakZvTndTeGgra0V1TzF4c0hyV1VLbHUySgpTVlpURm1pZnhIUG9oYXc1MHNlcXhQZGdvbVN6NzRGaVIyUTFsaEt1c1RYSHl5disxc1hhNUpQV2N5WHZIRUZyCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    client-key-data: LS0tLS1CRUdJTiBSU0EgtLS0tLQpNSUlFcEFJQkFBS0NBUUVBcXk2Ukc2ZzJtR25MdWFOV0hXQXN5YXhRRXk1QjVnd2o2dDN1bDEySnlPMUsrNGlRCnBkQ01Tbk5ZeUE5Z0ZZMnpzL3FUc0h2czduVG5qb2NYa1BydEVLUHJYWU1pOGxBcVMyK3NMRS9jNUNodXZlaDEKS0lPOFN4RDc5SHNPdS9wdDljcWZScy95aW5teDVkQWVjSVNNQU5RMjROQ1kvQmhvOW1YbU42TzlXeW9veStybApDU2lySEI5LzJPVVV5YVgxTy9CYUF3ODJibEVkM3NiYVVBWTJLYVF0c2VWUnhpamlIZjdOVFMyOHJoR1M2dDJLCi9oR2U5Vk1EeHdETFVxNkZuMmxNSFhNd0hYNlVQaUFBUW5USWhpcHBKRjFpc2JMVHdqUjJ3eUZ1amdLUEpWYVYKN1IyMDdpdDdpU0dDQTUybUlQNFJ5eFNENlRFOGx0WVpNM01KMHdJREFRQUJBb0lCQUdLcSthcjJYMW1raVcvYwpETlByem9VRUZEZXFEd1BaZFk1NEU0RWR3a0NWUGdZVDVrUWRHL0FCNG1lV0xTemZPM2grODBmamRtTFA4WFErCnU3K3VVcHluenc5Y2VmVXZiZUdEWWJzODRUYUZjNmQwNktyTW0raXN4bjFadk5sdTlKaWJEUW1VN05rU1lnN3oKMGNmTExxTi9ybzRRemsxb2lIK1Zwb2Zsa2t5TDVCa0FmekZHTnpRaW1rTXdCMitwZEJoTnZKR1V6SmlFdlZFZApmTEhtbm1JZDJhNGgwUzU1YmdrRWpxbytITHZsZzBoditPOTNjRnVWUUlHbkVPZFJLeVlTNW9qOTZqRVhzbFhxCkVnQjVtQ0taRmtacVE4NHZON3lxR1BWeHZOS2dCUng1Yjl0TVgzMS9zeHY1U3JDUS83Y29ETTJCUTNvNFp0bGUKN1VOOGU4a0NnWUVBMHQwbUJlRndSUWlxUk5Qdm14SEZvQk9uUEM1dEMxYzhiL3g2NTFvN2tjak93U0hqRnVVYgpqZjV1aHJSRDVmNE1PM3g3Y1R6RHN0QWhMTk1YdFFYVnN5UnFZc3IzZ3pJRjFlUFM5TFlsR0lvL3hKUUhTR3Q1CmNBNVptUVpLZC9kWndwdGdhSXFZb3FaK1hYM3VyWGRkL1M5SFFrWDNVMHk5SFMxMHV5MVhLV2NDZ1lFQXo5THcKVXdYRmptNHo3b2QyQkV1NjdPaXRtWGxuL0tIRExIcU9ZTkdsMnRPREVLQlV3RUx0ZktqN29jTXdqbk9wMjZaeQpoTWZoM1dlOWlodi95VU9ITkRrWW5uM2NBLzg5cHZ0S3VKOFVWbDd5YVB2c2dKMzNKdU1IMGdLRUNkYmtmZ2Y1CnhFOUt3QkFLOWZDYVh2N1piVzJFY2NpeGliRHRlamZCRGNTb25MVUNnWUFUZ3NMeDZGb3JDUkk0TENRUGhTVjAKR2wxdkJCeXkzKytIclNUeUl6Yy8xZ1RMZWdjNUp4blZtYVZBY1kyV3lscjNaaDFGN1FsdWtqTi9va2lmUjFydQpxSC92aERoQ3UyNHV6RlhJcnRIZjQycmpGWTlXOUJyWWMzdDIzVTRtcTR4eXBVM1dYbnloRUNiQkg5WGwxeFdyClI2U2dUVGtGVENSemZyRStZSU9nSVFLQmdRQ3p6ZCtwSk53NGozWVUwRlEyQVVOWGh6UHpEY3YvRWdGbnNSMFAKL1RLT3NlSzZkc3o4anFzajZKdlU1NmhTQnhZVmVOdXByN25sc2NTV0FnVjZueGNTQXJnZkgrdEQ5Z2ZrT1pQNwpTWDd4L3pHaHpSeGdac3NYZE9NbU9wRDhabFRJQ2FSNXlOUjJlU3JTZi9rL2FTUzZmVDlTcG4yY0VGeFdHbmpICnJiYm41UUtCZ1FERGY3OXpQL1dhU0lIamh4SVp1YnE2QUFBQ1BwTGFPNzNORC9BYzZrWlpvc1F5RVJFRmZJOVQKWmt1U09tZVkwcDlCcXp5em9pcEE1SFJQcERlZ2pPejNNT0hnL21qK0x6TFJQMDJ6TWhpUm1vRmZ4ZGRZR1dtcApxcmdOTlVzdHlwbzduUWR4MytwVVd2Y1NDRC80OTQyTkphNkpmSnRsT2dCa25BZ2ltRHVUclE9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
```

## 2. kubeconfig 파일 내용을 Local로 복사 후 테스트

```bash
root@Zest:~# cd microk8s/
root@Zest:~/microk8s# vim kubeconfig
root@Zest:~/microk8s# vim kubeconfig
root@Zest:~/microk8s# kubectl --kubeconfig=kubeconfig get nodes
E0428 18:14:30.919345    1268 memcache.go:265] couldn't get current server API group list: Get "https://10.0.0.35:16443/api?timeout=32s": dial tcp 10.0.0.35:16443: i/o timeout
E0428 18:15:03.852532    1268 memcache.go:265] couldn't get current server API group list: Get "https://10.0.0.35:16443/api?timeout=32s": dial tcp 10.0.0.35:16443: i/o timeout
```

> ????? 에러가 났다.. 로그를 확인해보니 주소가.. 이상한데..? 저건 EC2 Public 주소가 아닌 Private 주소이기 떄문… 고쳐볼까??  
{: .prompt-info}

```bash
root@Zest:~/microk8s# vim kubeconfig
root@Zest:~/microk8s# kubectl --kubeconfig=kubeconfig get nodes
E0428 18:19:13.211714    1348 memcache.go:265] couldn't get current server API group list: Get "https://15.15.182.77:16443/api?timeout=32s": tls: failed to verify certificate: x509: certificate is valid for 127.0.0.1, 10.152.183.1, 10.0.0.35, not 15.15.182.77
E0428 18:19:13.224469    1348 memcache.go:265] couldn't get current server API group list: Get "https://15.15.182.77:16443/api?timeout=32s": tls: failed to verify certificate: x509: certificate is valid for 127.0.0.1, 10.152.183.1, 10.0.0.35, not 15.15.182.77
```

![pic](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/8a3579fa-502c-4849-981f-c4f2a8bd118e)

public IP로 수정

> ????? 또 .. 에러가… 하지만 에러 내용이 다르다. TLS 인증서에 허가된 IP가 아닌 다른 IP에서 kube-apiserver에 명령을 내릴려고 했기 때문인 것 같다..  
> 테스트를 위해 TLS 검증을 비활성화 해서 kubectl 명령어를 날려볼까..??  
{: .prompt-info}

```bash
root@Zest:~/microk8s# kubectl --kubeconfig=kubeconfig --insecure-skip-tls-verify get nodes
NAME                  STATUS   ROLES    AGE    VERSION
i-0fefad06e169df0c3   Ready    <none>   157m   v1.29.2
root@Zest:~/microk8s# kubectl --kubeconfig=kubeconfig --insecure-skip-tls-verify get deploy
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2/2     2            2           63m
```

> 오.. TLS 검증을 비활성화 하면 접근이 가능하다 ㄷㄷ… 근데 보안상 좀 찜찜..한데.. 인증서를 재발급 받아볼까??  
{: .prompt-info}

## 3. MicroK8s TLS 인증서 갱신하기

### 1. cert-manager 사용 설정 및 MicroK8s 중지

```bash
root@i-0fefad06e169df0c3:~/microk8s# microk8s stop
root@i-0fefad06e169df0c3:~/microk8s# microk8s enable cert-manager
Infer repository core for addon cert-manager
Enable DNS addon
Infer repository core for addon dns
Addon core/dns is already enabled
Enabling cert-manager
....
```

### 2. 인증서 설정 파일 수정

> 해당 파일에 Public IP를 추가해줍니다  
{: .prompt-info}

```bash
root@i-0fefad06e169df0c3:~# vim /var/snap/microk8s/current/certs/csr.conf.template

[ req ]
default_bits = 2048
prompt = no
default_md = sha256
req_extensions = req_ext
distinguished_name = dn

[ dn ]
C = GB
ST = Canonical
L = Canonical
O = Canonical
OU = Canonical
CN = 15.15.12.77

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = kubernetes
DNS.2 = kubernetes.default
DNS.3 = kubernetes.default.svc
DNS.4 = kubernetes.default.svc.cluster
DNS.5 = kubernetes.default.svc.cluster.local
IP.1 = 127.0.0.1
IP.2 = 10.152.183.1
IP.4 = 15.15.12.77
#MOREIPS

[ v3_ext ]
authorityKeyIdentifier=keyid,issuer:always
basicConstraints=CA:FALSE
keyUsage=keyEncipherment,dataEncipherment,digitalSignature
extendedKeyUsage=serverAuth,clientAuth
subjectAltName=@alt_names

```

## 4. 확인

```bash
root@Zest:~/microk8s# kubectl --kubeconfig=kubeconfig get nodes
NAME                  STATUS   ROLES    AGE     VERSION
i-0fefad06e169df0c3   Ready    <none>   6m50s   v1.29.2
```

### 마무리

> 중간중간 TLS 인증서를 갱신하려고 csr, crt, rsa key 생성하고 온갖 방법을 동원했었는데.. MicroK8s는 csr.conf.template를 수정해야 적용이 된다..  
> 다른걸 고치면 무한루프의 지옥에 빠질수도..  
> **궁금하신점이나 추가해야할 부분은 댓글이나 아래의 링크를 통해 문의해주세요.**  
> **Written with [KKam.\_\.Ji](https://www.instagram.com/kkam._.ji/)**
{: .prompt-tip}

---
title: Pod가 계속 종료되지 않음 + EBS Volume 확장
date: 2024-05-05 21:46:52 +0900
author: kkamji
categories: [Kubernetes]
tags: [kubernetes, k8s, k8s-cluster, cluster, api-server, terminating, ebs, volume,  kubelet, aws, ec2, ubuntu]     # TAG names should always be lowercase
comments: true
image:
  path: /assets/img/kubernetes/kubernetes.png
---
> `kubectl delete pod simple-webapp-color`로 pod를 삭제하려 했는데 바로 삭제가 안되서 기다리면 삭제되겠지.. 하면서 다른 일을 하다가 다음날 확인을 해봤는데 아직도 `Terminating` 상태였다.. 그래서 이번에는 Pod가 Terminating 상태에 계속 머물러 있는 원인에 대해 다뤄보겠습니다.
{: .prompt-info}

## 1. Pod 확인

```bash
root@Zest ~# kubectl get pods

NAME                  READY   STATUS        RESTARTS   AGE
simple-webapp-color   0/1     Terminating   0          45h
```

> Pod Terminating 상태에 계속 머물러 있다..
{: .prompt-info}

## 2. Pod 상태 확인

```bash
root@Zest ~# kubectl describe pods simple-webapp-color
...
...
Events:
  Type     Reason   Age                     From     Message
  ----     ------   ----                    ----     -------
  Warning  Evicted  4m50s (x2334 over 26h)  kubelet  (combined from similar events): The node was low on resource: ephemeral-storage. Threshold quantity: 1Gi, available: 925292Ki.
```

> 용량이 부족하다…고?? 설마.. Worker node의 용량이 벌써 다 차버린건가..? 일단 해당 Pods를 강제로 지우고 문제를 해결해보자
{: .prompt-info}

```bash
root@Zest ~# kubectl delete pod simple-webapp-color --grace-period=0 --force

Warning: Immediate deletion does not wait for confirmation that the running resource has been terminated. The resource may continue to run on the cluster indefinitely.
pod "simple-webapp-color" force deleted
```

## 3. Worker Node 접속 및 여유 공간 확인

```bash
root@Zest ~# ssh KKam_Node1

Welcome to Ubuntu 24.04 LTS (GNU/Linux 6.8.0-1008-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro
...
...
...

ubuntu@ip-10-0-0-81:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root       8.7G  7.8G  895M  90% /
tmpfs           479M     0  479M   0% /dev/shm
tmpfs           192M 1016K  191M   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/xvda16     881M   76M  744M  10% /boot
/dev/xvda15     105M  6.1M   99M   6% /boot/efi
shm              64M     0   64M   0% /var/snap/microk8s/common/run/containerd/io.containerd.grpc.v1.cri/sandboxes/974112a4c95616bb637f7c6f8f22fc03efe3b095da7cf921478986607c4212d9/shm
shm              64M     0   64M   0% /var/snap/microk8s/common/run/containerd/io.containerd.grpc.v1.cri/sandboxes/83bf41130e8a6e90af31e7c9b8fc53296520d5029aa6cc102250956652a6e975/shm
tmpfs            96M   12K   96M   1% /run/user/1000
```

> 앗… 실제로 여유공간이 1기가가 되지 않았다.. AWS Free Tier에서 EBS를 30GB까지 무료로 제공해줘서 Worker Node의 용량을 10GB로 했었는데… ~~더 올리면 내 돈이…~~  용량을 추가해야겠다.
{: .prompt-info}

## 4. EBS 용량 확장

> 온프레미스 환경이었다면 HDD나 SSD를 추가 장착해주어야 하지만 EBS는 콘솔을 통해 용량을 추가할 수 있습니다.
{: .prompt-info}

### EBS 용량 확장

> EC2 → Elastic Block Store → Volume에 들어가서 확인해보자
{: .prompt-info}

![1](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/7b2fd302-7940-4f58-9fe2-f02dc5ef7443)

> 해당 볼륨을 체크하고 우상단의 Actions를 누르고 Modify Volume을 누릅니다
{: .prompt-info}

![2](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/c916644d-b83a-4104-90d8-fd2fb94386a4)

> Size를 우선.. 10GB에서 12GB로 늘린 후 Modify를 눌러줍시다
{: .prompt-info}

![3](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/c274b701-bd7f-4dcf-b9f6-4699a25b566d)

> 변경 완료
{: .prompt-info}

![4](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/5c8fca09-f8b9-4e20-90ee-4e439cc3bf89)

## 5. 운영체제에 볼륨 확장 적용

> EBS의 용량을 변경 후 운영체제에 볼륨 확장 적용 해주어야 합니다
{: .prompt-info}

### 마운트 된 볼륨 확인

```bash
ubuntu@ip-10-0-0-81:~$ sudo fdisk -l
...
...
Disk /dev/xvda: 12 GiB, 12884901888 bytes, 25165824 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 8F27EC90-814B-448C-88E6-B35D88412452

Device        Start      End  Sectors  Size Type
/dev/xvda1  2099200 20971486 18872287    9G Linux filesystem
/dev/xvda14    2048    10239     8192    4M BIOS boot
/dev/xvda15   10240   227327   217088  106M EFI System
/dev/xvda16  227328  2097152  1869825  913M Linux extended boot
```

### 파티션 확장

```bash
ubuntu@ip-10-0-0-81:~$ sudo growpart /dev/xvda 1

CHANGED: partition=1 start=2099200 old: size=18872287 end=20971486 new: size=23066591 end=25165790
```

### 파일 시스템 확장

```bash
ubuntu@ip-10-0-0-81:~$ sudo resize2fs /dev/xvda1

resize2fs 1.47.0 (5-Feb-2023)
Filesystem at /dev/xvda1 is mounted on /; on-line resizing required
old_desc_blocks = 2, new_desc_blocks = 2
dThe filesystem on /dev/xvda1 is now 2883323 (4k) blocks long.
```

### 확장 결과 확인

```bash
ubuntu@ip-10-0-0-81:~$ df -h

Filesystem      Size  Used Avail Use% Mounted on
/dev/root        11G  7.8G  2.9G  74% /
tmpfs           479M     0  479M   0% /dev/shm
tmpfs           192M 1016K  191M   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/xvda16     881M   76M  744M  10% /boot
/dev/xvda15     105M  6.1M   99M   6% /boot/efi
shm              64M     0   64M   0% /var/snap/microk8s/common/run/containerd/io.containerd.grpc.v1.cri/sandboxes/974112a4c95616bb637f7c6f8f22fc03efe3b095da7cf921478986607c4212d9/shm
shm              64M     0   64M   0% /var/snap/microk8s/common/run/containerd/io.containerd.grpc.v1.cri/sandboxes/83bf41130e8a6e90af31e7c9b8fc53296520d5029aa6cc102250956652a6e975/shm
tmpfs            96M   12K   96M   1% /run/user/1000
```

## 6. 문제 해결 여부 확인

> 테스트로 nginx pod를 생성하고 삭제해보자
{: .prompt-info}

```bash
root@Zest ~# kubectl run nginx-test --image=nginx
pod/nginx-test created

root@Zest ~# kubectl get pods
NAME         READY   STATUS    RESTARTS   AGE
nginx-test   1/1     Running   0          5s

root@Zest ~# kubectl delete pod nginx-test
pod "nginx-test" deleted

root@Zest ~# kubectl get pods
No resources found in default namespace.
```

> 정상적으로 동작한다. ㅎㅎ 문제가 해결되어서 다행…입니다
{: .prompt-info}

---
> **궁금하신점이나 추가해야할 부분은 댓글이나 아래의 링크를 통해 문의해주세요.**  
> **Written with [KKam.\_\.Ji](https://www.instagram.com/kkam._.ji/)**
{: .prompt-tip}

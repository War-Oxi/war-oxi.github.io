---
title: EC2위에 Jenkins Server 구축하기
date: 2024-05-02 22:42:36 +0900
author: kkamji
categories: [Jenkins]
tags: [jenkins, agent, ec2, aws, microk8s]     # TAG names should always be lowercase
comments: true
image:
  path: /assets/img/jenkins/jenkins.png
---

> <https://www.whatap.io/ko/blog/77/> 와탭랩스에서 이런 글을 올린 것을 확인했고,, 한번 적용해볼까?? 라는 생각을 하게되었습니다. 기존의 계획은 Jenkins Server를 하나 만들고 사용할 예정이었지만 우리는 저번에 MicroK8s Server를 만들었고.. 해당 클러스터를 이용해 Jenkins Agent를 Pod로 동적으로 생성하는 것은 어떨까요?? 아무래도.. 해보고 싶은건 해봐야겠죠??  
{: .prompt-tip}

---

> 일단 이번에는 Jenkins Server를 구축하는 내용을 기반으로 포스트를 작성하겠습니다.  
> EC2 ⇒ t4g.small  
> OS ⇒ Ubuntu 24.04 LTS  
{: .prompt-info}

## 1. Java 설치

> Jenkins는 실행에 Java가 필요합니다. OpenJDK를 설치해봅시다  
{: .prompt-info}

```bash
sudo apt update
sudo apt install fontconfig openjdk-17-jre
java -version
openjdk version "17.0.11" 2024-04-16
OpenJDK Runtime Environment (build 17.0.11+9-Ubuntu-1)
OpenJDK 64-Bit Server VM (build 17.0.11+9-Ubuntu-1, mixed mode, sharing)
```

## 2. Jenkins 설치

```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
sudo systemctl enable jenkins

## 확인

root@ip-10-0-0-24:~/jenkins# systemctl status jenkins
● jenkins.service - Jenkins Continuous Integration Server
     Loaded: loaded (/usr/lib/systemd/system/jenkins.service; enabled; preset: enabled)
     Active: active (running) since Sun 2024-05-05 06:54:50 UTC; 1min 27s ago
   Main PID: 3338760 (java)
      Tasks: 45 (limit: 2197)
     Memory: 594.5M (peak: 602.5M)
        CPU: 26.886s
     CGroup: /system.slice/jenkins.service
             └─3338760 /usr/bin/java -Djava.awt.headless=true -jar /usr/share/java/jenkins.war --webroot=/var/cache/j>

May 05 06:54:32 ip-10-0-0-24 jenkins[3338760]: 39ea4f81e4ca4b0cb3669573128b5aed
May 05 06:54:32 ip-10-0-0-24 jenkins[3338760]: This may also be found at: /var/lib/jenkins/secrets/initialAdminPassw>
May 05 06:54:32 ip-10-0-0-24 jenkins[3338760]: *************************************************************
May 05 06:54:32 ip-10-0-0-24 jenkins[3338760]: *************************************************************
May 05 06:54:32 ip-10-0-0-24 jenkins[3338760]: *************************************************************
May 05 06:54:50 ip-10-0-0-24 jenkins[3338760]: 2024-05-05 06:54:50.698+0000 [id=33]        INFO        jenkins.InitR>
May 05 06:54:50 ip-10-0-0-24 jenkins[3338760]: 2024-05-05 06:54:50.814+0000 [id=24]        INFO        hudson.lifecy>
May 05 06:54:50 ip-10-0-0-24 systemd[1]: Started jenkins.service - Jenkins Continuous Integration Server.
May 05 06:54:52 ip-10-0-0-24 jenkins[3338760]: 2024-05-05 06:54:52.453+0000 [id=49]        INFO        h.m.DownloadS>
May 05 06:54:52 ip-10-0-0-24 jenkins[3338760]: 2024-05-05 06:54:52.454+0000 [id=49]        INFO        hudson.util.R>
lines 1-20/20 (END)
```

## 3. Jenkins 접속해보기

> Jenkins Server에 접속하기 위해서는 EC2 인스턴스에 인바운드 규칙에 8080포트를 허용해주셔야 합니다
{: .prompt-info}
---
> {EC2_Public_IP}:8080.  
> 위와 같이 웹브라우저에서 접속해줍니다.  
{: .prompt-info}

![Untitled](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/54600af8-b49f-4694-a9f0-6bc047ad7ce1)

> 비밀번호가 필요한 것을 알 수 있습니다. /var/lib/jenkins/secrets/initialAdminPassword를 확인해볼까요?
{: .prompt-info}

```bash
root@ip-10-0-0-24:~# cat /var/lib/jenkins/secrets/initialAdminPassword
39a746af851e4dca44bcbe3669r153128ab 
```

> 해당 비밀번호로 접속해봅시다.
{: .prompt-info}

![Untitled](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/902c0205-5850-4ef8-91e9-5c991a426301)

> 접속 했습니다. Install suggested plugins를 눌러 기본 플러그인을 설치해볼게요.
{: .prompt-info}

## 4. Jenkins 설정

> Admin 계정부터 설정해보겠습니다.  
> ~~플러그인 설치하다가 캡쳐 까먹은건 비밀~~.  
{: .prompt-info}

![Untitled](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/a081faf3-b58c-48db-9446-734d63903d0a)

![Untitled](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/5de1886f-9751-43bd-9d83-b5dcd8505298)

## 마무리

![Untitled](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/8392049c-02e8-4a77-baa0-98c404040780)

> 다음 글에는 Kubernetes Pod를 Agent로 사용하는 방법을 올려보도록 하겠습니다.  
> **궁금하신점이나 추가해야할 부분은 댓글이나 아래의 링크를 통해 문의해주세요.**  
> **Written with [KKam.\_\.Ji](https://www.instagram.com/kkam._.ji/)**
{: .prompt-tip}

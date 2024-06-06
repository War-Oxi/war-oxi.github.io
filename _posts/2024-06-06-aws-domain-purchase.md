---
title: Github Pages에 사용자 지정 도메인 설정하기 
date: 2024-06-06 21:33:31 +0900
author: kkamji
categories: [AWS]
tags: [route53, domain, aws]     # TAG names should always be lowercase
comments: true
# image:
#   path: https://github.com/kkamji98/kkamji98.github.io/assets/72260110/c39504c6-3de4-4b41-919b-5ef1b132106c
---

저번 시간에 route53을 통해 구매한 kkamji.net 도메인을 해당 블로그가 사용하기 위해 route53을 통한 DNS Record 설정하는 과정에 대해 다뤄보겠습니다

## 주요 DNS Record 유형

A 레코드 (Address Record): 도메인 이름을 IPv4 주소로 매핑
AAAA 레코드: 도메인 이름을 IPv6 주소로 매핑
CNAME 레코드 (Canonical Name Record): 도메인 이름을 다른 도메인 이름으로 매핑
MX 레코드 (Mail Exchange Record): 도메인 이름을 이메일 서버로 매핑
TXT 레코드 (Text Record): 도메인에 대한 텍스트 정보를 저장
NS 레코드 (Name Server Record): 도메인의 네임 서버를 지정
PTR 레코드 (Pointer Record): IP 주소를 도메인 이름으로 매핑
SRV 레코드 (Service Record): 특정 서비스에 대한 서버의 위치를 지정
SOA 레코드 (Start of Authority Record): DNS 영역에 대한 권한 있는 정보와 기본 설정을 제공
ALIAS 레코드: 루트 도메인(@)을 다른 도메인으로 매핑

## A 레코드 설정

1. A 레코드를 생성하기 위해 Route53 > Hosted zones > 설정할 도메인에 들어가준 뒤 Create record를 클릭합니다
    ![image1](https://github.com/KKamJi98/kkamji98.github.io/assets/72260110/b5c65780-bf33-47e4-ba7c-51e0067b5bdf)
2. Record type에서 A 속성을 선택한 뒤, Github Pages의 IP 주소를 입력합니다. Routing policy는 가중치 기반 라우팅, 거리 기반 라우팅 등의 설정이 있지만 단순 라우팅으로 설정했습니다.
    ![image2-a record](https://github.com/KKamJi98/kkamji98.github.io/assets/72260110/5a8f1e2c-08ae-496f-8889-2f432d472f5b)
    ``` text
    185.199.108.153
    185.199.109.153
    185.199.110.153
    185.199.111.153
    ```

> **궁금하신점이나 추가해야할 부분은 댓글이나 아래의 링크를 통해 문의해주세요.**  
> **Written with [KKam.\_\.Ji](https://www.instagram.com/kkam._.ji/)**
{: .prompt-info}

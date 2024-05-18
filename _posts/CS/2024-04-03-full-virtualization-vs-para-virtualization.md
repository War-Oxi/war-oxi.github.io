---
title: 전가상화 & 반가상화 개념
date: 2024-04-03 21:34:31 +0900
author: kkankkandev
categories: [CS, Virtualization]
tags: [virtualization, 가상화, docker, k8s, container, vm, vmware, fill, 전가상화, 반가상화, full virtualization, para virtualization, hypervisor]     # TAG names should always be lowercase
comments: true
# image:
#   path: https://github.com/War-Oxi/war-oxi.github.io/assets/72260110/0794e0e2-b08e-4cc5-b640-c975f5a1a7b7
---


> type1, type2 가상화의 차이점으로는 하이퍼바이저가 하드웨어 바로 위에서 동작하느냐, 소프트웨어(OS)위에서 동작하느냐였습니다.
> 그렇다면 전가상화, 반가상화는 무엇이고 각각의 차이점은 어떻게 될까요?
{: .prompt-info}

## 전가상화(Full-Virtualization)

> 전가상화에서는 하드웨어를 완전히 가상화 하는 방식입니다. 하이퍼바이저가 하드웨어를 직접 제어하며, Guest OS가 가상화된 하드웨어와 상호 작용합니다. 해당 경우, Guest OS는 자신이 가상화된 환경에서 실행되고 있다는 사살은 인식하지 못합니다. 전 가상화는 Guest OS에 아무런 수정 없이 원래의 OS를 사용할 수 있게 해줍니다.
{: .prompt-info}

- 장점
  - 하드웨어를 완전히 가상화 하기 떄문에 Guest OS의 커널 등의 수정이 필요 없습니다.
  - Windows부터 Linux까지 다양한 OS를 사용할 수 있는 장점이 있습니다.
- 단점
  - 하이퍼바이저의 관리용 가상 머신이 모든 명령을 중재하고 번역하기 때문에 비교적 성능이 느림(오버헤드 발생)

## 반가상화(Para-Virtualization)

> 반가상화는 하드웨어를 완전히 가상화하지 않습니다. 게스트 OS가 가상화 환경에 최적화되어 있어야 하며, 이를 위해 운영 체제에 수정이 필요합니다. Guest OS는 하이퍼바이저 API를 호출하여 필요한 서비스를 요청합니다. 해당 방식은 하드웨어를 직접 에물레이션하는 대신, **Hyper Call** 요청을 통해 하이퍼바이저와 효율적으로 상호작용합니다.
{: .prompt-info}

- 장점
  - 하드웨어 에뮬레이션 대신 API를 통한 효율적인 상호작용으로 더 나은 성능을 제공합니다.
  - 전가상화에 비해 적은 리소스로 운영 가능합니다.
- 단점
  - Guest OS가 하이퍼바이저에 특화되어 있어야 하므로, 지원하는 운영 체제가 제한됩니다.
  - 가상화를 위해 운영 체제 소스 코드를 수정해야 하므로, 구현의 복잡성이 증가합니디.

## 정리

- 전가상화는 Guest OS가 스스로 가상환경임을 인지하지 못하지만 반가상화 환경에서 Guest OS는 스스로가 가상환경임을 인지하고 있습니다.
- 전가상화와 달리 반가상화는 하드웨어와 상호작용 하기위해 별도의 인터페이스를 통해 명령을 합니다 (**Hyper Call**)

## Reference

[kt cloud 기술 블로그](https://tech.ktcloud.com/77)
[Suyeon's Blog](https://suyeon96.tistory.com/53)

> **궁금하신점이나 추가해야할 부분은 댓글이나 아래의 링크를 통해 문의해주세요.**  
> **Written with [KKam.\_\.Ji](https://www.instagram.com/kkam._.ji/)**
{: .prompt-tip}

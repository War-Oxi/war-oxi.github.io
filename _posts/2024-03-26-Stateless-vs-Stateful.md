---
title: Stateful과 Stateless 개념 (NACL vs Security Group)
date: 2024-03-26 14:17:48 +0900
author: kkamji
categories: [Network]
tags: [network, stateful, stateless, firewall, nacl, security group, sg]     # TAG names should always be lowercase
comments: true
# image:
  # path: https://github.com/kkamji98/kkamji98.github.io/assets/72260110/2d2cbf9b-ce45-4102-bbc7-96faa519384a
---

> 네트워크나 웹을 공부하다보면 **Stateless**와 **Stateful**을 자주 접하게 됩니다.  
> **Stateless**는 **상태를 유지하지 않고**, **Stateful**은 **상태를 유지**한다고 하는데 이게 무슨 의미일까요?  
{: .prompt-tip}

## 1. Stateless (무상태)

> Stateless 통신에서는 각 요청을 독립적으로 처리합니다. 서버는 이전 요청에서의 상태나 데이터를 기억하지 않고, 각 요청은 서버와 통신하기 위해 필요한 모든 정보를 포함해야 합니다.  
{: .prompt-info}

### Stateless 장점

- **단순성과 투명성:** 서버가 클라이언트의 상태를 추적하거나 저장할 필요가 없습니다.
- **확장성:** 서버가 클라이언트의 상태 정보를 유지할 필요가 없기 때문에, 시스템의 확장성이 높아집니다. 부하 분산이 쉽고, 서버를 추가하는 것만으로도 시스템 확장이 가능합니다.

### Stateless 단점

- **데이터 전송량:** 모든 요청에 필요한 모든 정보를 포함해야 하므로, 네트워크 오버헤드가 증가할 수 있습니다.
- **복잡한 클라이언트 로직:** 클라이언트가 상태 관리와 관련된 논리를 처리해야 할 수 있습니다.

## 2. Stateful (상태 유지)

> Stateful 통신에서는 서버가 이전이 상태나 세션 정보를 기억합니다. 이를 통해 서버는 클라이언트의 이전 요청을 기반으로 향후 요청을 처리할 수 있습니다.  
> 이로 인해 서버와 클라이언트 간의 통신이 상태 정보를 기반으로 연속적으로 이루어집니다.
{: .prompt-info}

### Stateful 장점

- **효율성:** 서버가 클라이언트의 상태를 알고 있기 때문에, 클라이언트는 매 요청마다 모든 정보를 전송할 필요가 없습니다.
- **사용자 경험:** 사용자의 세션 정보를 유지함으로써 보다 개인화되고 연속적인 상호작용을 제공할 수 있습니다.
- **복잡한 트랜젝션 처리:** 여러 단계에 걸친 트랜잭션이나 작업을 처리하기 쉽습니다.

### Stateful 단점

- **복잡성:** 서버가 상태 정보를 관리해야 하므로 구현이 복잡해질 수 있습니다.
- **리소스 요구량:** 서버가 각 클라이언트의 상태를 유지해야 하므로, 메모리와 처리 리소스를 더 많이 사용할 수 있습니다.
- **확장성 문제:** 서버 간의 상태 공유가 필요할 수 있으며, 이는 시스템의 확장성을 제한할 수 있습니다.

## 3. NACL vs Security Group

> AWS EC2와 VPC를 사용하다 보면 NACL과 Security Group에 대해 자주 접하게 됩니다.
> 둘의 차이는 무엇일까요?
{: .prompt-info}

### NACL(Network Access Control List)

- VPC내 서브넷 측면에서 동작하는 가상의 네트워크 방화벽입니다.
- Stateless(무 상태)특성을 갖습니다.

### SG(Security Group)

- EC2 인스턴스 측면에서 동작하는 가상의 방화벽입니다.
- Stateful(상태 유지)특성을 갖고 있습니다.

<br><br>

> **궁금하신점이나 추가해야할 부분은 댓글이나 아래의 링크를 통해 문의해주세요.**  
> **Written with [KKam.\_\.Ji](https://www.instagram.com/kkam._.ji/)**
{: .prompt-info}

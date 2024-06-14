---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---

<!-- > Add Markdown syntax content to file `_tabs/about.md`{: .filepath } and it will show up on this page.
{: .prompt-tip } -->

# 호기심 많은 트러블슈터 - 김태지 포트폴리오

> ## <span style="color:#BF8C79">소개</span>

<div style="display: flex; align-items: center;">
  <div style="flex: 1.2;">
    <img src="https://github.com/kkamji98/kkamji98.github.io/assets/72260110/63652803-688c-4c82-97fa-9dd8f1cea58f" alt="Profile Photo" style="max-width: 90%; border-radius: 8px;">
  </div>
  <div style="flex: 2; margin-left: 10px;">
    <!-- <h3 style="color: #D49A7B;">Introduce</h3> -->
    <h4> "안녕하세요! 호기심 많은 트러블 슈터 김태지입니다." </h4>
    <p>학부 시절 AWS를 사용한 웹사이트 배포를 통해 클라우드에 대해 접하게 되었고, 졸업 후 AWS와 RAPA에서 주관한 AWS Cloud School 1기에 참여하며 인프라 역량을 쌓아왔습니다.</p>
    <p>
      - 주어진 기간 내 최고의 결과물을 만들어내기 위해 항상 최선을 다합니다.
      <br> - 사소한 문제라도 지나치지 않고 해결하는 것을 즐깁니다.
      <br> - 새로운 기술을 습득하는 것을 좋아합니다.
      <br> - 누군가에게 배움을 나눌 때 큰 성취감을 느낍니다.
      <br> - 겪은 시행 착오나 공부한 기술을 공유하는 것을 좋아합니다.
    </p>
  </div>
</div>

연락처 - 010-8813-5460  
이메일 - <xowl5460@naver.com>
[Blog - https://kkamji.net](https://kkamji.net)  
[Github - https://github.com/kkamji98](https://github.com/kkamji98)

> ## <span style="color:#BF8C79">주요 프로젝트</span>

### Amazon Photo Query - [ 2024.01 ~ 2024.03 ]

> 기존 앨범 서비스에 AI 모델을 도입. 이미지 분류, 이미지 검색 편의성 제공  
> AWS 클라우드 상에서 MSA, 3-Tier-Architecture로 구축 및 배포  
{: .prompt-tip}

[백엔드 개발 저장소 - https://github.com/KKamJi98/Photo-Query](https://github.com/KKamJi98/Photo-Query)  
[EKS 배포 저장소 - https://github.com/kkamji98/aws-app-eks-manifests](https://github.com/kkamji98/aws-app-eks-manifests)

#### 데모 영상

<table>
  <tr>
    <td align="center">
      <iframe width="350" height="220" src="https://www.youtube.com/embed/373Xvy3tddM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
      <p><strong>얼굴 기반 사진 검색</strong></p>
    </td>
    <td align="center">
      <iframe width="350" height="220" src="https://www.youtube.com/embed/jOgX3f43c1Q?si=LOGMPSblNM_v2WwA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
      <p><strong>자연어로 사진 검색</strong></p>
    </td>
  </tr>
  <tr>
    <td align="center">
      <iframe width="350" height="220" src="https://www.youtube.com/embed/GlHXMVzgk-s?si=9l69N_pEyp1g-SAa" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
      <p><strong>태그 자동 생성 기능</strong></p>
    </td>
    <td align="center">
      <iframe width="350" height="220" src="https://www.youtube.com/embed/7bvTYE_tMAg?si=rn0vQ8f-jJ5RynsL" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
      <p><strong>기본 앨범 기능</strong></p>
    </td>
  </tr>
</table>

<!-- [![유사한 사진 검색](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/31dc7819-2d79-481d-8b50-0d1acd783788)](https://www.youtube.com/watch?v=373Xvy3tddM) -->

#### 주요역할 및 담당 ( 기여도 )

- Cloud Architecture 설계  
- AWS 인프라 구축 & 운영  
- CI/CD 파이프라인 구축  
- ERD 구축 & 운영  
- EKS 모니터링 및 비용 추적  
- 이미지 CRD, 북마크, 태그 기능 개발 & 배포  

#### 프로젝트 기술 스택

| 구분        | 기술                                                                                 |
| ----------- | ------------------------------------------------------------------------------------ |
| Programming | Go, Python, Node.js, Flutter                                                         |
| CI/CD       | Jenkins, ArgoCD, CodeSeries                                                          |
| Container   | Docker, EKS, ECR                                                                     |
| Database    | RDS (MySQL), DynamoDB, DocumentDB                                                    |
| Monitoring  | Prometheus, Grafana, Container Insight, KubeCost                                     |
| ETC         | S3, Cognito, Rekognition, Secret Manager, Terraform, SNS, SQS, Karpenter, Fluent-bit |
| Tools       | Notion, Slack, Postman, GitHub                                                       |

#### 주요 기능

- 회원 가입, 로그인
- 자연어를 통한 사진 검색
- 얼굴 인식을 사용한 사진 조회
- 태그 자동 생성
- 사진 업로드, 삭제, 조회
- 선정적인 사진 필터링

#### 클라우드 아키텍처 구성

![Architecture](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/85603917-67cc-4df3-a02b-ec2265ea235c)

#### CI/CD 구성

![CI/CD](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/054c8b31-2309-42ba-9d34-d99e743210d5)

#### EKS모니터링

![EKS 모니터링](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/3534ef17-b8b9-4698-941d-c8c4e7bda81f)

![EKS 모니터링(Container Insight)](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/05163450-9b6d-4c9f-927e-9e229fb6a567)

#### 비용 추적(Kubecost)

![EKS 비용추적](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/f6e3bd64-3681-4ebf-a44a-ee366ec9b160)

#### 업로드 로직

![이미지 업로드 로직](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/c3410c5e-e326-4164-8a2c-803b00a6d641)

#### 프로젝트 일정 관리

![프로젝트 일정 관리](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/dfc08e2b-e621-419a-b520-52d9d87ab71d)

#### 의견 공유 및 공지

![Slack](https://github.com/kkamji98/kkamji98.github.io/assets/72260110/90306aad-c2bb-47b5-8461-2257b180300f)

#### 트러블 슈팅

> **Public Subnet에서 Private Subnet로 서버**를 이전, 웹어플리케이션에서 S3에 Access Deny 문제 직면  
> **AWS IRSA** 개념을 공식문서를 통해 습득 후, 팀원들에게 공유  
{: .prompt-warning}
---
> 성능 테스트 도중 다수(500MB 700장)의 이미지 업로드 시 **5분 이상의 시간과 상당한 리소스를 사용**한다는 문제를 발견  
{: .prompt-warning}

1. 기존 로직에서 이미지 리사이징 기능을 Lambda함수로 분리  
2. 버지니아 리전을 사용으로 생긴 네트워크 지연 해소를 위해 **Global Accelerator** 도입  
3. 동시성 구현을 위해 **Goroutine**을 프로젝트에 도입  
4. **이미지 업로드 API 응답시간을 5분에서 1분 미만으로 단축** (문제 해결)

---

### ACS-Notice Board - [ 2023.11 ~ 2023.11 ]

> 기존 AWS Cloud School 공지 시스템의 문제를 개선하기 위해 제작  
> Docker Swarm을 사용해 3-Tier-Architecture를 구현  
{: .prompt-tip}

<https://github.com/kkamji98/ACS-1st-Notice-Board>

#### 데모 사진

![image](https://github.com/kkamji98/ACS-1st-Notice-Board/assets/72260110/17ca9374-7930-45fb-bff8-350f05598bef)

#### 주요 역할 및 담당 (기여도 100% - 개인 프로젝트)

- 아키텍처 설계
- 웹어플리케이션 개발 및 배포
- 인프라 구축
- Docker Swarm 세팅
- Docker Private Repository 구축
- Reverse Proxy 구현
- ERD 설계

#### 프로젝트 기술 스택

| 구분      | 기술                                              |
| --------- | ------------------------------------------------- |
| Infra     | VMware Workstation Player16, Ubuntu 22.04 - 64Bit |
| Develop   | Go (Gin-Gonic, GORM), HTML, CSS, JavaScript       |
| Container | Docker, Docker Private Repository, Docker Swarm   |
| Database  | MySQL                                             |
| Tools     | Notion, Postman, GitHub                           |
| Server    | Nginx                                             |

#### 주요기능

- 게시글 생성
- 게시글 삭제
- 게시글 조회

#### 아키텍처 구조

![ACS Architecture](https://github.com/kkamji98/ACS-1st-Notice-Board/assets/72260110/9c10e239-64ea-4779-941e-9e078e3750b1)

#### Load-Balancing 확인

![ACS-Load-Balancing](https://github.com/kkamji98/ACS-1st-Notice-Board/assets/72260110/defc1d9d-7c1a-4b3a-99d6-52e0d93bf7fd)

#### 트러블 슈팅

> db -> was -> web의 순서로 컨테이너가 실행이 보장 되지 않아 생긴 컨테이너 재시작 문제 직면  
{: .prompt-warning}

1. docker-compose의 healthcheck, depends_on 기능을 프로젝트에 적용
2. 컨테이너 재시작 문제 해결

---

> ## <span style="color:#BF8C79">교육</span>

> **AWS Cloud School 1기 [2023.08 - 2024.03]**
> - Cloud(AWS) & DevOps 교육
> - Network, Linux, Docker, Kubernetes, Jenkins, ArgoCD, AWS 학습
> - 교육과정 내 공지 게시판 개발
> - AI & Cloud 기반 앨범 서비스 "Photo Query" 팀 프로젝트 참여 (5인)
{: .prompt-tip}

---

> **Rising Camp Plus 1기 [2023.07 - 2023.08]**
> - Java Backend 교육
> - Java, Spring Boot3, JPA, MySQL, Git 학습
> - Spring Boot 기반 채용사이트 개발 팀 프로젝트 참여 (3인)
{: .prompt-tip}

---

> **중원대학교 컴퓨터공학과 [2018.03 - 2023.08]**  
> - 융합과학대학 수석 졸업
> - 학생 대표로 교내 J-Smart, 교수역량진단시스템 사업 참여
> - 데이터베이스 강의 보조 활동
> - GPA (Overall): 4.40 / 4.5
> - GPA (Major): 4.42 / 4.5
{: .prompt-tip}

---

> ## <span style="color:#BF8C79">기술스택</span>

| 구분           | 기술                                                          |
| -------------- | ------------------------------------------------------------- |
| Programming    | Go, Python, Java, C++, Bash                                   |
| OS             | Windows, Ubuntu, CentOS, Rocky Linux                          |
| Virtualization | KVM, VMware Workstation, Docker, Kubernetes                   |
| DevOps         | Jenkins, ArgoCD, Terraform, Prometheus, Grafana               |
| Cloud(AWS)     | EC2, EKS, ECS, ECR, RDS, VPC, Route53, CloudFront, CloudWatch |

> ## <span style="color:#BF8C79">자격사항</span>

- **AWS Certified Solutions Architect - Associate** [2024.02.02]
- **정보처리기사** [2023.09.30]
- **AWS Certified Cloud Practitioner** [2023.05.31]
- **CertifiedAIExpert** [2020.12.31]

> ## <span style="color:#BF8C79">대내외 활동</span>

- **AWS 온라인 세미나 세선 진행 (MicroK8s를 사용하여 EC2기반 경량 클러스터 구축)** [2024.06]
- **AWS Summit Seoul 2024 참여** [2024.05]
- **AWS Student Community Day 2024 참여** [2024.04]
- **Wanted Backend Challenge - AWS를 활용한 시스템 아키텍처 참여** [2024.03]
- **Advanced Architecting on AWS 수료** [2023.12]
- **AWS DevOps Jam 2등** [2023.12]
- **DevOps Engineering on AWS 수료** [2023.12]
- **Developing on AWS 수료** [2023.12]
- **AWS Well-Architected Best Practices 수료** [2023.11]
- **AWS Community Day 2023 참여** [2023.10]
- **AWS Security Essentials 수료** [2023.10]
- **AWS Cloud Practitioner Essentials 수료** [2023.10]
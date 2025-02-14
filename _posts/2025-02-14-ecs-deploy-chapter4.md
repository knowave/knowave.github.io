---
layout: post
title: "[AWS] ECS EC2 배포(4) - ECR, ECS 배포"
description: "AWS ECS EC2 배포 방법 - ECR, ECS 배포"
date: 2025-02-14 13:50:00 +0900
categories: [AWS]
tags: [AWS, ECS, EC2]
toc: true
---

# ECR, ECS 배포

앞서 생성한 VPC를 사용해서 ECR과 ECS를 생성을 하려고 한다.

ECS는 private subnet을 이용해서 생성을 하려고 한다. 

private subnet으로 ECS를 생성하려고 하는 이유는 네트워크의 안정성 때문이다.

예를 들어 운영 환경의 서비스가 있고 그 서비스가 외부에서 접속할 수 있는 환경이라고 가정해보자.

이 서비스가 외부에서 접속할 수 있는 환경이라면 외부에서 공격을 받을 수 있다.

이런 공격을 받을 수 있는 환경을 만들어주면 안된다.

하지만 프론트와 통신을 할 때 프론트가 바로 접속할 수 있는 환경이 필요하다.

이런 환경을 구축하기 위해서 ALB를 이용해서 ALB는 public subnet에 생성해서 외부에서 접속할 수 있도록 해줄 수 있다.

하지만 private subnet으로 생성된 인스턴스도 인터넷을 사용할 수 있게 해야한다.

VPC에서 NAT Gateway를 생성해서 private subnet도 인터넷을 사용할 수 있게 해줄 수 있는데,
비용이 비싸서 여기서는 EC2 인스턴스를 이용해서 NAT Instance를 생성하여 사용할 것이다.

## ECR 생성

AWS console에서 ECR을 검색 후 생성을 해주면 된다.

ECR은 Docker 이미지를 저장하는 저장소이다.

이미지를 저장하고 싶은 이름을 입력하고 생성을 해주면 된다.

![ECR 생성](/assets/img/post/create-ecr.png)

ECR 생성 페이지에서 사용하고자 하는 이름을 입력하고 생성을 해주면된다.


## ECS 생성

ECS는 ECR 이미지를 이용해서 서비스를 배포할 수 있다.

ECS Cluster를 생성하기 전에 Task Definition을 먼저 생성할 것이다.

ECS 인스턴스는 **Fargate**와 **EC2** 두 가지 방식이 있다.

Fargate는 컨테이너 기반의 서비스를 배포할 때 사용하는 방식이다.

EC2는 기존의 인스턴스 방식을 사용하는 방식이다.

이 포스트에서는 EC2 인스턴스를 이용해서 ECS를 배포할 것이다.

### Task Definition 생성

Task Definition을 생성하기 위해서는 ECS 페이지에서 좌측에 **Task Definitions**를 클릭한다.

![Task Definition 생성](/assets/img/post/create-task-definition.png)

Task Definition 페이지에서 **Create new Task Definition** 버튼을 클릭한다.

사용하려고 하는 이름을 먼저 입력을 해준뒤 EC2 인스턴스를 사용하려고 했으니 **인프라 요구 사항**에서 EC2를 선택한다.

![Task Definition 인프라 요구 사항](/assets/img/post/task-definition-infra-setting.png)

이미지를 보면 EC2 인스턴스를 선택해줬고, 운영 체제는 **Linux/X86_64**를 선택했다.

~~ARM64는 인프라 구성이 어려우서 다음 기회에~~

후에 네트워크 모드를 볼 수 있는데 여기서는 **bridge**를 선택했다.

bridge외에 awsvpc 모드도 존재하는데 bridge를 사용한 이유는

Docker의 기본 네트워크 방식으로 EC2 Launch Type에서만 사용이 가능하고,

컨테이너가 bridge 네트워크 통신을 사용하여 통신을 한다.

컨테이너 독립적으로 구성이 되어 **port mapping**을 통해서 통신이 가능하다.
(기본적으로 컨테이너끼리 통신을 할 수 없음. -> 링크 또는 네트워크 설정 필요)

> 💡 이 bridge 모드는 EC2에서만 가능하다.

task size는 실행하고자 하는 EC2 인스턴스의 스펙에 맞게 설정을 해주면 된다.

너무 딱 맞게 CPU와 memory를 설정하는 것보다는 조금 여유있게 설정하는 것이 좋다.

![Task Definition Container](/assets/img/post/create-container-1.png)

Task Definition을 설정할 때 컨테이너도 같이 생성을 해줘야 한다.

이름은 자유롭게 사용하고 싶은 이름을 입력해주고, 이미지는 전에 만들어둔 ECR 이미지의 URL을 입력해준다.

port mapping은 본인이 사용하고자 하는 호스트 포트와 컨테이너 포트를 입력해주면 된다.

(여기서는 둘 다 3000번 포트로 사용.)

아래 리소스 할당 제한은 위에 설정한대로 CPU 2, memory 4GB로 설정했다.

이렇게 설정을 해놓고 생성 버튼을 눌러 Task Definition을 생성해준다.

## ECS Cluster 생성

ECR도 생성했고, Task Definition도 생성해줬으니 이제 ECS Cluster를 생성해줄 차례이다.

ECS Cluster는 작업 또는 서비스의 논리적인 그룹이다.

![ECS Cluster 생성](/assets/img/post/create-cluster.png)

위에 이미지처럼 cluster를 생성할 때 본인이 사용하고 싶은 이름을 자유롭게 입력을 해주고

아래 인프라에서 EC2 인스턴스를 사용하기로 했으니 EC2를 선택해준다.

EC2 인스턴스 유형은 테스트용이라 free tier에 있는 t2.micro를 선택해줬다. 
(task definition에서 알맞게 cpu, memory를 설정)

원하는 용량은 인스턴스를 최소 몇개부터 최대 몇개까지를 사용할 것인지 입력해주면 된다.

사용할 SSH 키페어를 선택해주고 EC2 인스턴스 네트워크 설정으로 넘어간다.

![ECS Cluster 네트워크 설정](/assets/img/post/create-ecs-cluster-1.png)

네트워크 설정에서는 본인이 사용하고자 하는 VPC를 선택해준다.

서브넷은 private subnet을 선택해줘서 사용하고자 했으니 본인이 생성한 private subnet을 선택해준다.

보안그룹은 만들어 놓았다면 만들어 놓은 보안그룹을 사용하고 만들어 놓지 않은 보안 그룹은 본인이 생성해준다.

(보안그룹은 추후에 변경할 예정 _ ALB 보안그룹과 연결해줄것.)

이제 이렇게 하고 ECS Cluster를 생성해준다.

ECS를 배포하려면 Service를 생성해야 하니 다음으로 넘어가보자

### ECS Service 생성

ECS Service는 작업 또는 서비스를 배포하고 관리하는 데 필요한 설정을 정의하는 것이다.

![ECS Service 생성](/assets/img/post/create-ecs-service.png)

위에 이미지처럼 Service를 생성할 때 본인이 사용하고 싶은 이름을 자유롭게 입력을 해주고

생성해둔 Task Definition을 패밀리에서 선택해준다.

서비스 유형은 복제본과 데몬이 존재하는데

복제본은 컨테이너를 여러개 생성하여 분산 처리를 하는 방식이고

데몬은 컨테이너를 하나만 생성하여 실행하는 방식이다.

하지만 나는 로드밸런싱을 위해서 복제본을 사용할 것이다.

아래로 내려가면 로드밸런싱이 나오게되는데 로드밸런싱을 생성을 해줄 것이다.

**Load Balancer 생성**

EC2를 검색 후 로드밸런싱을 선택해준 뒤 로드밸런싱을 생성해주는데

로드 밸런서 유형은 HTTP/HTTPS를 사용하기 위해 Application Load Balancer를 선택한다.

![로드밸런서 유형](/assets/img/post/load-balancer-type.png)

로드 밸런서 유형에서 Application Load Balancer를 선택해준다.

![로드밸런서 기본 설정](/assets/img/post/alb-default.png)

로드밸런서 이름은 왠만하면 본인이 사용하고자 하는 ecs service 이름과 연관되게 입력해주고

![로드밸런서 네트워크 매핑](/assets/img/post/alb-network-mapping.png)
vpc는 ecs에서 설정했던 vpc로 해주고, 서브넷은 프론트와 통신을 하기위해서 public으로 설정해준다.

![로드밸런서 리스너 및 라우팅](/assets/img/post/alb-listener.png)

리스너는 HTTP 포트 80번을 추가해준다. 추후에 HTTPS를 사용하려면 443번 포트를 추가해준다.

이때, 리스너에서 타겟 그룹을 설정해줘야한다.

![타겟 그룹 설정1](/assets/img/post/target-group-1.png)

![타겟 그룹 설정2](/assets/img/post/target-group-2.png)

위와 같이 설정해준 뒤 다시 로드밸런서 생성으로 돌아와 생성한 타켓 그룹을 선택해준다.



![ECS Service 로드밸런싱](/assets/img/post/ecs-service-load-balancing-1.png)

로드밸런싱을 사전에 만들어 놓지 않았기 때문에 생성을 해줘야 한다.

우리는 HTTP/HTTPS에서 라우팅을 하기 때문에 Application Load Balancer를 선택한다.

컨테이너를 선택하고, 상태 검사 유예시간은 10초 정도로 설정을 해준다.

리스너는 추후에 HTTPS를 사용한다면 HTTPS의 443번 포트를 추가해준다.

이렇게 설정해 준 뒤 service를 생성해주면 끝이 난다.
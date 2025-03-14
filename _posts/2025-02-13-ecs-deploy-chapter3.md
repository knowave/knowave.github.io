---
layout: post
title: "[AWS] ECS EC2 배포(3) - 인터넷 게이트웨이"
description: "AWS ECS EC2 배포 방법 - 인터넷 게이트웨이"
date: 2025-02-13 14:30:00 +0900
categories: [AWS]
tags: [AWS, ECS, EC2]
toc: true
---

# 인터넷 게이트웨이

VPC는 기본적으로 격리가 되어있는 네트워크 환경이다. VPC에서 생성된 리소스들은 기본적으로 인터넷을 사용할 수 없다.

인터넷을 연결해주기 위해서는 인터넷 게이트웨이를 연결해줘야 한다.

VPC와 연결되어 있는 라우팅 테이블에 인터넷 게이트웨이를 연결해주면 인터넷을 사용할 수 있게된다.

## 인터넷 게이트웨이 생성

인터넷 게이트웨이는 AWS VPC 콘솔에서 생성을 할 수 있다.

좌측을 보면 인터넷 게이트웨이 탭을 누른 후 우측에 인터넷 게이트웨이 생성 버튼을 누른다.

![인터넷 게이트웨이 생성](/assets/img/post/screenshot-2025-02-13-13-09-17.png)

이름을 입력한 후 인터넷 게이트웨이 생성 버튼을 눌러준다.

버튼을 누르면 아래와 같이 인터넷 게이트웨이가 생성된다.

![인터넷 게이트웨이 생성 완료](/assets/img/post/screenshot2025-02-13-13-12-06.png)

인터넷 게이트웨이 생성이 완료되었으면 이제 생성한 인터넷 게이트웨이를 라우팅 테이블과 연결을 해줘야 한다.

라우팅 테이블과 연결을 해줘야 VPC에서 생성된 리소스들이 인터넷을 사용할 수 있게된다.

## 생성한 인터넷 게이트웨이 연결

라우팅 테이블 탭을 클릭한 후 라우팅 테이블 목록에서 연결을 해줄 라우팅 테이블을 찾는다.

라우팅 테이블 접속 후 편집 버튼을 누른 후에 아래와 같이 설정을 한다.

![라우팅 테이블 편집](/assets/img/post/screenshot-2025-02-13-13-09-17.png)

저장 버튼을 누르면 아래와 같이 인터넷 게이트웨이가 연결된 것을 확인할 수 있다.

![라우팅 테이블 확인](/assets/img/post/screenshot-2025-02-13-13-16-09.png)

이제 해당 VPC에서 리소스맵을 확인해보면 서브넷들과 라우팅 테이블이 연결되어 인터넷 게이트웨이와 연결된 것을 확인할 수 있다.

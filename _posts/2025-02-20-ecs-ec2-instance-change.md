---
layout: post
title: "[AWS] ECS EC2 인스턴스 유형 변경"
description: "ECS EC2 인스턴스 유형 변경하는 방법"
date: 2025-02-20 09:50:00 +0900
categories: [AWS, TIL]
tags: [AWS]
---

## ECS EC2 인스턴스 유형 변경

ECS 클러스터에서 배포를 할 때 사용하는 EC2 인스턴스의 유형을 변경하는 방법을 설명하려고 한다.

Auto Scaling을 통해서 스케일 아웃을 통해 서버의 수를 늘려 처리 능력을 높이더라도 점점 더 서비스가 커지게 되면 한계가 있다.

이런 경우에는 스케일 업을 통해서 서버의 성능을 높일 수 있는데 ECS에서도 EC2의 인스턴스 유형을 변경할 수 있다.

### 인스턴스 유형 변경

먼저 ECS 클러스터를 생성할 때 만든 **EC2 -> Auto Scaling Group**에서 인스턴스 유형을 변경할 수 있다.

![auto-scaling-group](/assets/img/post/auto-scaling-group.png)

해당하는 ECS 클러스터의 auto scaling group을 선택을 하면 시작 템플릿이 보인다.

시작 템플릿 편집을 통해서 시작 템플릿 버전을 생성한다.

기존에 사용하던 시작 템플릿을 선택하고 새로운 버전의 템플릿을 생성해준다.

아래로 내리다보면 인스턴스 유형이 보일 건데 거기서 사용하고자 하는 인스턴스 유형을 선택해주고 생성을 해준 뒤

재배포를 진행해주면 해당 템플릿으로 배포가 돤다.

![instance-type](/assets/img/post/start-template-instance.png)
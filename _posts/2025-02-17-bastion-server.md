---
layout: post
title: "[TIL] Bastion Server"
description: "Bastion Server"
date: 2025-02-17 13:05:00 +0900
categories: [TIL, CS]
tags: [TIL, CS]
toc: true
---

## Bastion Server

Bastion의 사전적 의미는 "요새", "보루"라는 뜻으로, 사라지거나 위협을 받고 있는 신념이나
생활 방식을 지켜내거나 방어하는 것을 의미한다.

설명 하려고 하는 Bastion Server도 이와 같은 의미를 지니고 있다. 

Bastion Server는 네트워크 보안 모델 중 하나로, 네트워크 내부와 외부를 분리하는 것을 의미한다.

쉽게 말해서 네트워크 내부와 외부를 분리하는 것을 의미한다.

터미널에 접속을 하기 위해서 SSH 접속을 할 때 사용하는 22번 포트를 생각해보면 22번 포트의 보안이 뚫리게 되면
서비스에 치명적인 이슈를 불러올 수 있다.

그렇다고 모든 서버를 동일한 수준으로 보안을 설정한다면 확장성을 고려한 구성이나 관리 포인트가 늘어나게 되면서
유지보수 비용이 증가하게 된다.

하지만 Bastion Server는 악성 코드나 바이러스에 대해서 피해를 보더라도 Bastion Server만 갈아끼우면 되니
서비스 운영하는데 있어서 큰 영향을 끼치지 않는다.

따라서 22번 포트를 Bastion Server에만 열어두고, 그 서버에 보안을 집중하는 것이 좋다.

### Bastion Server의 구성

Bastion Server는 일반적으로 다음과 같은 구성을 가진다.

* 외부 네트워크에서 접속할 때는 22번 포트를 사용한다.
* 내부 네트워크에서 접속할 때는 다른 포트를 사용한다.

VPC를 생성할 때 **private subnet**과 **public subnet**을 생성해서 <u>실제로 운영이되는 서버는 **private subnet**에 배치하고</u>
<u>Bastion Server는 **public subnet**에 배치한다.</u>

이렇게 하면 외부에서는 Bastion Server에 접속을 하고, 그 서버를 통해서 내부 서버에 접속을 할 수 있게 된다.

간단하게 EC2 인스턴스를 생성하고 (free tier를 사용) 보안 그룹을 설정하면 된다.

보안 그룹에는 내부 네트워크에서 접속할 포트를 열어주고 (ex. 22번 포트, 3306번 포트, 27017번 포트 등)
외부에서는 22번 포트만 열어주면 된다.
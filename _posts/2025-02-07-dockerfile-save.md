---
layout: post
title: "[Docker] Local에서 Docker 이미지 빌드 후 추출 및 압축 처리하는 방법"
description: "Local에서 Docker 이미지 빌드를 하고 이미지를 추출 및 압축 처리를 하는 방법"
date: 2025-02-07 16:30:00 +0900
categories: [Docker]
tags: [Trouble Shooting, Docker]
toc: true
---

## Local에서 Docker 이미지 빌드 후 추출 및 압축 처리하는 방법

Docker 이미지를 로컬에서 빌드를 한 후에 이를 저장하고 압축해서 보관하거든 다른 환경으로 이동할 수 있다. 솔직히 DockerHub에
이미지를 등록해서 사용하는 방법이 나을 수 있지만 리소스가 부족한 환경에서는 이 방법이 괜찮다고 생각한다.

아래는 이미지 빌드 후 추출 및 압축 처리를 하는 벙법을 설명을 하고자 한다.

## 1. Docker 이미지 빌드

먼저 Docker 이미지를 빌드하기 위해서 `Dockerfile`을 작성해야 한다. (여기서는 node로 예시를 들겠다.)
```Dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm install -g typescript ts-node

EXPOSE 8080

CMD ["npm", "start"]
```

Dockerfile을 작성하고 나면 이 파일을 이용해서 이미지를 빌드를 해준다.

```bash
docker build -t my-image .
```
my-image:1.0을 해주면 version을 1.0으로 지정해줄 수 있고, 적지 않으면 latest로 지정된다.

`.`은 현재 디렉토리를 가리킨다.

> 이미지 목록을 확인하려면 `docker images` 명령어를 사용하면 된다.

## 2. Docker 이미지 저장

이미지를 빌드 후에 이미지 저장을 위해서 아래와 같은 명령어를 사용한다.

```bash
docker save -o my-image.tar my-image
```

만약 여러 이미지를 하나의 압축파일로 만들고 싶을 땐 `-o "압축 파일 이름"` 뒤에 이미지 이름들을 적어주면 된다.

```bash
docker save -o my-images.tar my-image my-image2
```

3. 압축한 docker 이미지 load

압축한 이미지 파일을 다시 이미지로 불러오기 위해서 아래와 같은 명령어를 사용할 수 있다.

```bash
docker load < my-image.tar
```

다시 docker에 image를 빌드가 됐고, 확인하고 싶으면

```bash
docker images
```

명령어를 사용하면 된다.
---
layout: post
title: "[Trouble Shooting] Docker compose"
description: "Docker compose를 사용해서 MySQL container가 준비 상태가 될 때까지 대기하도록 하는 방법"
date: 2025-02-07 16:30:00 +0900
categories: [Trouble Shooting, Docker]
tags: [Trouble Shooting, Docker]
toc: true
---

## 문제

2개의 서버와 mongoDB, MySQL을 `docker-compose`를 이용해서 build 하고 실행하는 단계에서 문제가 발생했다.
MySQL과 연결되어 있는 한 서버에서 **Database Connection** 관련 에러가 발생했고, 이 문제는 MySQL image가 build가 되면서 container가 준비 상태가 아니라서 발생한 문제였다.

`depends_on` 옵션을 사용했지만 `depends_on`은 단순히 실행 순서를 보장하는 것이며 container가 준비 상태인지 확인하지 않아서 발생한 문제였다.

## 해결

해결하기 위해서 `depends_on`만 사용하지 않고, `healthcheck` 옵션을 사용해서 MySQL container가 준비 상태에 도달할 때까지 MySQL을 사용하는 서버는 대기를 하도록 했다.

```yaml
  mysql:
    image: mysql:latest
    container_name: mysql
    restart: always
    ports:
      - '3309:3306'
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - custom_network
    healthcheck:
      test: ['CMD', 'mysqladmin', 'ping', '-h', 'localhost', '-uroot', '-proot']
      timeout: 20s
      retries: 5
```

MySQL container가 `healthcheck` 옵션을 사용하면서 준비 상태까지 도달하게 되면 MySQL을 사용하는 서버는 그제서야 container 생성을 실행하게 된다.

```yaml
    depends_on:
      mysql:
        condition: service_healthy
      mongo:
        condition: service_started
    networks:
      - custom_network
```

MySQL을 사용하는 서버에서는 `depends_on` 옵션에서 `mysql` 서비스가 `service_healthy` 상태가 될 때까지 대기하도록 했다.

이렇게 `yaml` 파일을 작성하고 `docker-compose up -d` 명령어를 실행하게 되면 기대했던 대로 MySQL container가 준비 상태가 될 때 까지 대기를 하면서

**Database Connection** 관련 에러가 발생하지 않게 되었다.

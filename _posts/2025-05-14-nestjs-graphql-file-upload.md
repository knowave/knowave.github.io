---
layout: post
title: "[NestJS] GraphQL File Upload"
description: "NestJS에서 GraphQL을 사용하여 file upload하는 방법"
date: 2025-05-14 15:30:00 +0900
categories: [TIL, NestJS, GraphQL]
tags: [ETC]
---

## 왜 GraphQL?

원래 회사 프로젝트에서 REST API를 사용하고 있었는데 불필요한 join문과 수시로 바뀌는 기획에 대응하기 위해서 클라이언트 입맛에 맞게 데이터를 조회할 수 있도록 GraphQL을 도입하기로 했다.

사용을 하면서 GraphQL로 파일 업로드 하는 API를 개발할 때 2022년도에 실패했던 경험이 있었다. 하지만 이번에는 GraphQL로 온전히 사용하면서 최대한 REST API를 병행해서 사용하고 싶지 않아서 파일 업로드 기능을 GraphQL로 개발을 완료해 보려고 한다.

## GraphQL로 File upload를 하기

GraphQL에서 Mutation을 이용해서 file upload를 하려고 한다. file을 upload를 하기 위해서 `graphql-upload` 라는 라이브러리를 설치해야 한다.

```bash
> npm i graphql-upload
> npm i -D @types/graphql-upload
```

위 명령어를 통해서 라이브러리를 설치해준다. (`npm i` 대신에 `yarn add`를 사용해도 됨.)
이때, nestjs `src/main.ts`에서 graphql upload express를 활성화를 해줘야 한다.

```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import graphqlUploadExpress from 'graphql-upload/graphqlUploadExpress.mjs';

async function bootstrap() {
    const app = await NestFactory.create(AppModule);

    app.use(graphqlUploadExpress());
    await app.listen(3000);
}

bootstrap();
```

이때, 주의사항은 `import graphqlUploadExpress from 'graphql-upload` 혹은 `import { graphqlUploadExpress } from 'graphql-upload` 이렇게 `import`를 하면 해당 모듈을 찾지 못한다는 에러가 발생한다.

무조건 `graphql-upload/graphqlUploadExpress.mjs` 에서 import를 해야 한다!

다음 단계는 이제 file을 upload할 resolver에서 파라미터 처리를 해줘야 하는데 기존에 **REST API**를 사용할 때 처럼 file과 관련된 데코레이터를 붙여줘야 한다.

```ts
import { Args, Mutation, Resolver } from '@nestjs/graphql'
import GraphqlUpload, { FileUpload } from 'graphql-upload/GraphQLUpload.mjs'

@Resolver()
export class UploadResolver {
    constructor() {}

    @Mutation(() => String)
    async uploadFile(@Args('file', { type: () => GraphqlUpload }) file: FileUpload) {
        console.log(file);
    }
}
```

이렇게 앞서 `src/main.ts`에서 import한 것 처럼 특정 경로에서 import를 해준다.
위 예제 코드를 작성하고 `playground` 혹은 `graphiql` 이 두 곳에서 테스트를 할 수 없다.
왜냐하면 이 두 곳은 file upload를 지원하지 않는다.

그래서 찾은게 `Altair` 다. UI도 훌륭하고 사용하기 되게 편하다. 무튼 `Altair`를 사용해서 file을 업로드 해주고, schema를 작성해준다.

![auto-scaling-group](/assets/img/post/graphql-file-upload.png)

위에 이미지 처럼 변수를 만들어서 해줘도 좋고 편한 방법으로 해주면 된다!
이렇게 작성하고 서버로 요청을 하면 `console.log(file);` 을 통해 터미널에서 확인해보면
```ts
{
    filename: string;
    mimetype: string;
    encoding: string;
}
```

이런식으로 원하는 결과가 잘 나올 거다. 이 값을 통해서 s3 bucket에 업로드를 할 수 있고, local에 이미지를 저장할 수 있고 그건 이제 본인의 자유다.

## 마무리

이처럼 GraphQL을 이용해서 File Upload를 하는 방법에 대해서 소개를 해봤다. 뭐 별겨 아니지만 과거에 완료하지 못한 기능이지만 이번에 완료를 할 수 있게 되어서 REST API와의 병행은 잘 마무리 할 수 있을 거 같다. ~~(핑계를 대자면 그 당시에 이 기능을 붙잡고 기능을 개발할 시간이 없었다,,)~~
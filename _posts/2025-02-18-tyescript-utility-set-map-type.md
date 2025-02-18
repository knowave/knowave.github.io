---
layout: post
title: "[TIL] TypeScript Utility Set - Map Type"
description: "TypeScript Utility Set - Map Type"
date: 2025-02-18 09:25:00 +0900
categories: [TIL, TypeScript]
tags: [TIL, TypeScript]
---

## Typescript Utility Types, Set Type, Map Type

### Utility Types

**utility type**은 Typescript에서 제공하는 <u>유형 변환을 도와주는 내장 타입이다.</u>
기존 타입을 변형해서 쉽게 사용할 수 있도록 한다.

| Utility Type | 설명 | 예제 |
|------|------|------|
| `Partial<T>` | 모든 속성을 선택적으로 변경 | `{ name: string }` -> `{ name?: string }` |
| `Required<T>` | 모든 속성을 필수로 변경 | `{ name?: string }` -> `{ name: string }` |
| `Readonly<T>` | 모든 속성을 읽기 전용으로 변경 | `{ name: string }`  -> `{ readonly name: string }`|
| `Pick<T, K>` | 특정 속성만 선택하여 타입 생성 | `Pick<User, 'name'` |
| `Omit<T, K>` | 특정 속성을 제외하여 타입 생성 | `Omit<User, 'password'>` |
| `Record<K, T>` | key-value 객체 타입 생성 | `Record<'id', string>` |

#### Utility Type 예제

```ts
interface User {
    id: number;
    name: string;
    email?: string;
    password: string;
}

// Partial<T>
const partialUser: Partial<User> = { name: 'John' };

// Required<T>
const requiredUser: Required<User> = { id: 1, name: 'John', email: 'john@example.com', password: '123456' };

// Readonly<T>
const readonlyUser: Readonly<User> = { id: 2, name: 'Jane' } // readonly.id = 3; 수정 불가

// Pick<T, K>
type Username = Pick<User, 'name'>; // { name: string }

// Omit<T, K>
type UserWithoutPassword = Omit<User, 'password'>; // { id: number; name: string; email?: string }
```

### Set Type

set type은 <u>중복되지 않은 값을 저장하는 자료구조</u>로, Javascript Set과 유사하지만 Typescript에서는 타입을 정의해서 사용을 할 수 있다.

```ts
const numbers: Set<number> = new Set([1, 2, 3, 3, 4]);

numbers.add(5); // 5 추가
numbers.delete(1) // 1 제거

console.log(numbers); // Set { 2, 3, 4, 5 }
```

위에 예제코드와 같이 Set을 number 타입으로 변수 `numbers`의 타입을 정의를 하고 값을 할당을 해주었다.

숫자 `1, 2, 3, 3, 4`를 할당을 해주고 숫자 `5`를 추가하고 숫자 `1`을 제거해주었다.

Set Type은 중복된 값은 알아서 제거를 해주기 때문에 결과가 `2, 3, 4, 5`가 된다.

Set Type을 이용해서 유틸리티 함수도 만들어 사용할 수 있다.

```ts
function uniqueArray<T>(array: T[]): T[] {
    return [...new Set(array)];
}

const uniqueNumbers = uniqueArray([1, 2, 3, 4, 5, 6, 7, 7, 7]);
console.log(uniqueNumbers); // [1, 2, 3, 4, 5, 6, 7]
```

위에 예제코드와 같이 배열을 받아서 중복된 값을 제거하고 반환을 해주는 함수를 만들었다.

배열 `[1, 2, 3, 4, 5, 6, 7, 7, 7]`을 함수에 전달을 해주면 중복된 값은 알아서 제거를 해주고 반환을 해준다.


### Map Type

Map Type은 <u>key-value 형태의 데이터를 저장하는 자료구조</u>로 Object와 차이점을 가지고 있다.

* Object와 차이점
    * Object는 키가 문자열 또는 심볼만 가능하지만, **Map**은 <u>모든 타입을 키로 사용을 할 수 있다.</u>
    * **Map**은 키의 순서가 늘 유지가 되면서 성능도 더 뛰어나다.

```ts
const userRoles: Map<string, string> = new Map();
userRoles.set('Alice', 'Admin');
userRoles.set('Bob', 'User');

console.log(userRoles.get('Alice'));    // 'Admin'
console.log(userRoles.get('Bob'));      // 'User'
```

위에 예제코드와 같이 변수 `userRoles`를 `Map<string, string>` 타입을 정의하고 Map을 초기화 해줬다.

그 다음 `set` 메서드를 이용해서 key에 이름을 할당하고 value에는 역할을 할당했다.

`get` 메서드를 이용해서 key에 해당하는 value를 조회를 할 수 있다.

Map Type을 이용해서 유틸리티 함수도 만들어 사용할 수 있다.

```ts
type User {
    id: number;
    role: string;
}

function createUserRoleMap(users: User[]): Map<number, string> {
    return new Map(users.map(user => [user.id, user.role]));
}

const users: User[] = [
    { id: 1, role: 'Admin' },
    { id: 2, role: 'User' }
];

const userMap = createUserRoleMap(users);
console.log(userMap.get(1));
```

### 마무리

#### Utility Types

utility type은 **기존 타입을 변형해서 쉽게 사용이 가능**하며
`partial<T>`, `Pick<T, K>`, `Omit<T, K>`등 다양하게 변형이 가능하다.

#### Set, Map

set은 중복 없이 저장이 가능하며 map은 **key-value** 구조의 데이터 저장을 한다. map은 object보다 성능이 더 뛰어나며, 더 다양한 타입을 지원한다.
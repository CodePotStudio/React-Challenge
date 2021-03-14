# Graphql

Graphql은 Schema Definition Language이다.

Type 정의가 가능하며, (단일, 다중)관계를 정의할 수 있다.

## Core Concepts

- Fetching Data with __Queries__
- Writing Data with __Mutations__
- Realtime Updates with __Subscription__

### The GraphQL Schema

1. 클라이언트가 API 기능을 지정함
2. 클라이언트와 서버간의 contract를 명시함
3. 특별한 _root type_ 들의 모음
    - Query
    - Mutation
    - Subscription

## Architecture

GraphQL은 명세일 뿐이다. 구현은 직접하거나 다양한 툴을 사용할 수 있다.

### Architectural Use Cases

1. DB와 연결된 GraphQL 서버
    - 직접 프로젝트를 시작할 때 많이 사용하는 패턴
2. 이미 존재하는 시스템과 연동된 GraphQL 서버
    - 레거시 시스템, 다양한 API가 존재하는 케이스에 적절
    - 위처럼 복잡한 상황속에서 데이터 호출로직의 복잡성을 줄이거나 숨길 수 있음
3. DB도 연결돼 있고 다른 시스템과 연동된 GraphQL 서버(hybrid)
    - GraphQL 서버는 데이터소스가 무엇이든 상관하지 않음(DB, Server, 3rd Party API)

### `Resolver` functions

- GraphQL __Queries/Mutations__ 는 필드의 집합으로 구성
- GraphQL Sever는 각 필드당 __하나의 resolver function__ 이 매핑됨
- 각 `resolver`의 목적은 해당 필드에 대한 데이터를 내어오기 위함

## Data fetching: 명령형에서 선언형으로 (Client)

__명령형: 기존 REST API의 절차__

- HTTP 요청 준비
- 받은 데이터를 파싱
- 로컬 어딘가에 저장
- 데이터를 UI에 렌더링

__선언형: GraphQL__

- 데이터 요구사항을 설명
- 데이터를 UI에 렌더링

## Client: 뷰와 데이터 종속성을 함께 배치

View단위(Smart Container)는 State/Data에 종속. 

이를 함께 배치하여 개발자의 경험을 향상

## Batched Resolving (Server)

Query의 Schema 가 정의돼있고 Resolver가 있다면 BFS 방식으로 관계 데이터를 resolve함.

그리고 DB/Server에 요청을 날릴때 resolve와 함께 요청을 날리게 됩니다. 이는 매우 어설픈 방식임

대표적으로 data-loader라는 라이브러리를 통해 일괄처리를 하여 중복 요청을 방지.

## More GraphQL Concept

### Fragment

Fragment는 특정 타입의 필드들의 모음

```typescript
type User {
    name: String!
    age: Int!
    email: String!
    street: String!
    zipcode: String!
    city: String!
}
```

이제 유저의 주소와 관련된 모든 정보를 담은 `fragment`를 표현할 수 있습니다.

```
fragment addressDetails on User {
    name
    street
    zipcode
    city
}
```

이제 한 유저의 주소 정보에 접근하기위한 쿼리를 쓸 때, 다음과 같은 `fragment`를 참조하는 문법을 사용할 수 있습니다.

```
{
    allUsers {
        ... addressDetails
    }
}

// 다음과 같은 쿼리입니다.
{
    allUsers {
        name
        street
        zipcode
        city
    }
}
```

### Named Query Results with Aliases

GraphqQL에서는 여러 쿼리를 한번의 요청에 담아 보낼 수 있습니다. 하지만 응답 데이터는 요청되는 필드 구조에 따라 형성되므로 동일한 필드를 요청하는 여러 쿼리를 보낼때 이름 지정 문제가 발생할 수 있습니다.

```
{
    User(id: "1") {
        name
    }
    User(id: "2") {
        name
    }
}
```

아래와 같이 쿼리 결과의 이름을 지정할 수 있습니다.

```json
{
    first: User(id: "1") {
        name
    }
    second: User(id: "2") {
        name
    }
}

// 응답 결과
{
    "first": {
        "name": "Alice"
    },
    "second": {
        "name:": "Sarah"
    }
}
```

### Advanced SDL

#### Object & Scalar Types

GraphQL에는 두가지 다른 타입들이 존재합니다.

- __Scalar__ 스칼라 타입은 데이터의 구체적인 단위를 표현합니다. - `String`, `Int` `Float`, `Boolean`, `ID`
- __Object__ 오브젝트 타입은 그 타입의 속성을 표현하며 구성가능한 필드타입 입니다. - `User` or `Post`

#### Enums

```
enum Weekday {
    MONDAY
    TUESDAY
    WEDNESDAY
    THURSDAY
    FRIDAY
    SATURDAY
    SUNDAY
}
```

엄밀히 얘기해서 `enums`는 특펼한 종류의 `scalra` 타입입니다.

#### Interface

```
interface Node {
    id: ID!
}

type User implements Node {
    id: ID!
    name: String!
    age: Int!
}
```

#### Union Types

```
type Adult {
    name: String!
    work: String!
}

type Child {
    name: STring!
    school: String!
}

union Person = Adult | Child
```

Typescript처럼 생각해서 그냥 보면 문제가 없을 것 같지만... 문제가 생깁니다.

```
{
    allPersons {
        name # works for `Adult` and `Child`
        ... on Child {
            school
        }
        ... on Adult {
            work
        }
    }
}
```

`conditional fragments`를 사용하여 해결할 수 있습니다.

# React + Apollo Tutorial

> __Project: Catstronaut__

## Server

_Schema-First Design_

### Define the schema

```javascript
const { gql } = require('apollo-server');
```

스키마 정의와 같은 GraphQL 문자열을 래핑하는 데 사용되는 태그가 지정된 템플릿 리터럴입니다.

### Run Server

```javascript
const { ApolloServer, MockList } = require('apollo-server');

const mocks = {
    Query: () => ({
        "1~3개가 번갈아가며 리턴됨"
        tracksForHome: () => new MockList([1, 3]),
    }),
    Track: () => ({
        id: () => 'track_01',
        title: () => 'Astro Kitty, Space Explorer',
        author: () => {
        return {
            name: 'Grumpy Cat',
            photo:
            'https://res.cloudinary.com/dety84pbu/image/upload/v1606816219/kitty-veyron-sm_mctf3c.jpg',
        };
        },
        thumbnail: () =>
        'https://res.cloudinary.com/dety84pbu/image/upload/v1598465568/nebula_cat_djkt9r.jpg',
        length: () => 1210,
        modulesCount: () => 6,
    }),
};

const server = new ApolloServer({ typeDefs, mocks });
```

서버 인스턴스 생성시 `mocks`에 임시 데이터를 넣으면, 아직 실제 데이터가 없어도 모킹을 이용해 개발을 할 수 있다.

### Apollo Studio Explorer

쿼리를 테스트할 수 있도록(로컬 서버도 연동 가능) 도와주는 Tool(무료!)

## Client

```
npm i @apollo/client graphql
```

```javascript
const client = new ApolloClient({
 uri: 'http://localhost:4000',
 // query 결과를 저장하여 중복 요청을 방지 하거나 store로 사용할 수 있음
 cache: new InMemoryCache(),
});
```

### Wrapping query results

HOC

```jsx
const QueryResult = ({ loading, error, data, children }) => {
  if (error) {
    return <p>ERROR: {error.message}</p>;
  }
  if (loading) {
    return (
      <SpinnerContainer>
        <LoadingSpinner data-testid="spinner" size="large" theme="grayscale" />
      </SpinnerContainer>
    );
  }
  if (!data) {
    return <p>Nothing to show...</p>;
  }
  if (data) {
    return children;
  }
};
```


---

__Reference__

https://www.howtographql.com

https://odyssey.apollographql.com
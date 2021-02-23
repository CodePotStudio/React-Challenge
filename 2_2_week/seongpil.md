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

---

__Reference__

https://www.howtographql.com/
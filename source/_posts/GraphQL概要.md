---
title: GraphQL概要
date: 2023-10-19 16:41:32
tags: [graphql,api]
categories: 应用开发
---

# GraphQL概要

1. GraphQL是什么

* API标准，可以作为REST API的备选方案
* 由Facebook开发并开源发布
* 用于API的查询语言

2. GraphQL诞生的原因

* 在网速较慢的设备上，移动端应用需要更快速的加载数据。GraphQL只需要传输前端需要的数据，而REST API则传世请求资源的所有数据。
* 前端框架和平台多种多样，GraphQL可以让它们可以精确地访问需要的数据。
* 持续集成导致API改动频率增加，而GraphQL则能减少这样的改动。

3. GraphQL相对于REST的优势

* 减少了要传输的数据
* 减少了发送请求的数量
* 避免了由于API设计变化导致的endpoint结构的调整
* 更理解客户端对数据的需求
* Schema和强类型可以让前后端独立开发

4. GraphQL的核心概念

* Schema Definition Language (SDL)

```
type Person {
  name: String!
  age: Int!
  posts: [Post!]!
}

type Post {
  title: String!
  author: Person!
}
```

* 发送请求
```
request:
{
  allPersons(last: 2) {
    name
    age
    posts {
      title
    }
  }
}
```

* 请求改变数据
```
mutation {
  createPerson(name: "Bob", age: 36) {
    name
    age
    id
  }
}
```

* 订阅数据的更新
```
subscription {
  newPerson {
    name
    age
  }
}
```

* Schema例子

```
type Query {
  allPersons(last: Int): [Person!]!
  allPosts(last: Int): [Post!]!
}

type Mutation {
  createPerson(name: String!, age: Int!): Person!
  updatePerson(id: ID!, name: String!, age: String!): Person!
  deletePerson(id: ID!): Person!
}

type Subscription {
  newPerson: Person!
}

type Person {
  id: ID!
  name: String!
  age: Int!
  posts: [Post!]!
}

type Post {
  title: String!
  author: Person!
}
```

* Resolvers

服务端获取每个字段的函数称之为resolver。

* Fragments

Fragment可以简化查询语句，例如可以针对如下的User类型定义一个Fragment，然后查询语句就可以得到简化。

```
type User {
  name: String!
  age: Int!
  email: String!
  street: String!
  zipcode: String!
  city: String!
}

fragment addressDetails on User {
  name
  street
  zipcode
  city
}

{
  allUsers {
    ... addressDetails
  }
}
```

* 参数定义

在类型定义中，每个字段可以定义参数和参数的默认值，例如：

```
type Query {
  allUsers(olderThan: Int = -1): [User!]!
}
```

* 别名

Query语句中可以为返回结果指定别名，例如：

```
{
  first: User(id: "1") {
    name
  }
  second: User(id: "2") {
    name
  }
}

{
  "first": {
    "name": "Alice"
  },
  "second": {
    "name": "Sarah"
  }
}
```

* 类型

GraphQL中的类型可分为标量类型(Scalar type)和对象类型(Object type)。

  - 标量类型有5种： String, Int, Float, Boolean, ID
  - 对象类型可以自定义，如上述的User类型

枚举类型的定义：

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

联合类型的定义：
```
union Person = Adult | Child

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

接口的定义：

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

5. GraphQL客户端的职责

* 让开发者用声明式的方式请求数据，而不用关心底层的网络任务。
* 集成视图层可以让UI自动更新
* 缓存查询结果
* 构建时检查及优化schema

6. 服务端执行流程

服务端会按照广度优先来解析查询语句，并逐个调用每个field的resolver。例如：

```
query: Query {
  author(id: "abc"): Author {
    posts: [Post] {
      title: String
      content: String
    }
  }
}
```
其获取数据的流程类似于：

```
Query.author(root, { id: 'abc' }, context) -> author
Author.posts(author, null, context) -> posts
for each post in posts
  Post.title(post, null, context) -> title
  Post.content(post, null, context) -> content
```

为了避免重复地请求同一个数据，可以使用loader来解析查询语句，到解析完之后再请求数据。
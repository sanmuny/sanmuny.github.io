---
title: OpenAPI 概要
date: 2022-06-06 20:16:44
tags:
  - openapi
categories:
  - 应用开发
cover: https://images.unsplash.com/photo-1580983219883-8463619d50e5?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=2670&q=80
---

# OpenAPI是什么？

OpenAPI被用来描述基于HTTP的API，是目前被广泛接受和使用的API工业标准。

# 使用OpenAPI规范的优势

 - 可以使用工具检查用户定义的API是否满足OpenAPI特定版本的规范，语法是否正确等。
 - 可以检查请求和响应中的数据是否正确。
 - 可以自动生成API文档。
 - 自动生成客户端和服务端的代码。
 - 可以用图形化工具快速、方便地创建API描述文件。
 - 可以在写代码之前创建提供示例响应的伪HTTP服务器。
 - 在API定义阶段就可以发现一些可能出现的安全漏洞。

 # API描述文件

 API描述文件是一个机器可读的API定义文件。它应该是尽量完整的、细致的、明确的。开发者可以使用API描述文件来自动生成API文档以及代码。

 格式: JSON 或者 YAML

### 最小化结构:

``` YAML
openapi: 3.1.0 # OpenAPI版本
info:
  title: A minimal OpenAPI document
  version: 0.0.1 # API版本
paths: {} # No endpoints defined
```

### Servers对象
``` YAML
servers:
  - url: https://{username}.server.com:{port}/{version}
    variables:
      username:
        default: demo
        description: This value is assigned by the service provider.
      port:
        enum:
          - "8443"
          - "443"
        default: "8443"
      version:
        default: v1
```

### 路径对象:
``` YAML
paths:
  /board: # URI
    get: # HTTP方法
      ...
    put:
      ...
```

### 方法对象:
``` YAML
  get:
    summary: Get the whole board
    description: Retrieves the current state of the board and the winner.
    responses:
      "200":
        description: "OK"
        content:
          ...
  post:
    summary: 
    description:
    response:
    requestBody:
    parameters:
```

### response对象
``` YAML
paths:
  /board:
    get:
      responses:
        "200":
          description: Everything went fine.
          content:
            ...
        "404": ...
```

### 内容对象:
``` YAML
content:
  application/json:
    ...
  text/html:
    ...
  text/*:
    ...
```

### Media类型对象:
``` YAML
content:
  application/json:
    schema:
      type: integer
      minimum: 1
      maximum: 100

content:
  application/json:
    schema:
      type: string
      enum:
      - Alice
      - Bob
      - Carl

content:
  application/json:
    schema:
      type: array
      minItems: 1
      maxItems: 10
      items:
        type: integer

content:
  application/json:
    schema:
      type: object
      properties:
        productName:
          type: string
        productPrice:
          type: number
``` 

### requestBody对象

方法对象中的requestBody和parameters共同定义了HTTP请求中的数据。

``` YAML
requestBody:
  required: true
  content:
    application/json:
      schema:
        type: string
        enum: [".", "X", "O"]
```

### parameters对象
``` YAML
paths:
  /users/{id}:
    get:
      parameters:
      - name: id  # 必须有的，定义参数名
        in: path  # 必须有的，定义参数的来源，可以是 path, query, header中的一个
        required: true # 可选的
        schema:
          type: integer
          minimum: 1
          maximum: 100
```

### 对象重用

可以使用components和ref来重用一个对象，例如：
``` YAML
components:
  schemas:
    coordinate:
      type: integer
      minimum: 1
      maximum: 3
  parameters:
    rowParam:
      name: row
      in: path
      required: true
      schema:
        $ref: "#/components/schemas/coordinate"
    columnParam:
      name: column
      in: path
      required: true
      schema:
        $ref: "#/components/schemas/coordinate"
paths:
  /board/{row}/{column}:
    post:
      parameters:
        - $ref: "#/components/parameters/rowParam"
        - $ref: "#/components/parameters/columnParam"
  /page/{row}/{column}:
    post:
      parameters:
        - $ref: "#/components/parameters/rowParam"
        - $ref: "#/components/parameters/columnParam"
```

### 文档格式

段落可以使用文本模式和折叠模式，文本模式中的段落会原样输出，而折叠模式将会自动换行。
``` YAML
文本模式输入：
description: |
  This is a string
  in multiple lines.

  And an extra one.
文本模式输出：
This is a string
in multiple lines.

And an extra one.
```
``` YAML
折叠模式输入：
description: >
  This is a string
  in multiple lines.

  And an extra one.
折叠模式输出：
This is a string in multiple lines.
And an extra one.
```

description对象中也支持[markdown](https://spec.commonmark.org/0.27/)的语法

# OpenAPI Generator

OpenAPI Generator可以根据OpenAPI的API描述文件自动生成客户端、服务器端代码以及API文档。使用homebrew安装的命令如下:

``` bash
|$ brew install openapi-generator
```
生成代码的命令：
``` bash
openapi-generator generate -i petstore.yaml -g ruby -o /tmp/test/
```
其中 `-i`指定API描述文件，`-g`指定代码语言，`-o`指定输出路径。

文档链接：https://openapi-generator.tech/docs/installation
---
title: GraphQL
date: 2021-04-23 11:22:19
tags: [ GraphQL ]
---



## simple example

```
query {
  aman {
    id
    ago: ago # 年龄
    name
    sex
  }
}
```



## 字段 & 别名

```
query {
  groups {
    id
    name
    apis {
      id
      name
      code: uri
    }
  }
}

```



## 参数

```
query {
  groups(ids: [100000010, 100000011, 100000012]) {
    id
    name
    apis {
      id
      name
      code: uri
    }
  }
}
```



## 片断

```
fragment groupFields on Group {
    id
    name
    apis {
        id
        name
        description
        code: uri
    }
}

query {
  SaaS: group(id: 100000010) {
    ...groupFields
  }
  
  Xinlingshou: group(id: 100000011) {
    ...groupFields
  }
}
```



## 变量 & 默认变量

```
fragment groupFields on Group {
    id
    name
    apis {
        id
        name
        code: uri
    }
}

query ($saasId: Int = 100000012, $xinlingshouId: Int = 100000013) {
  SaaS: group(id: $saasId) {
    ...groupFields
  }
  
  Xinlingshou: group(id: $xinlingshouId) {
    ...groupFields
  }
}

```



## 指令

```
query ($saasId: Int, $xinlingshouId: Int, $withApi: Boolean!) {
  SaaS: group(id: $saasId) {
    id
    name
    apis @include(if: $withApi) {
        id
        name
        code: uri
    }
  }
  
  xinlingshou: group(id: $xinlingshouId) {
        id
        name
        apis @skip(if: $withApi) {
            id
            name
            code: uri
        }
    }
}
```



## 变更

```
mutation {
    aman(name: "Bob", sex: "female", ago: 30) {
        name
        ago
        sex
    }
}
```



查询变更结果

```
query {
    aman {
        id
        name
        ago
        sex
    }
}
```


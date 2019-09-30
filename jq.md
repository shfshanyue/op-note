---
title: jq 命令使用及示例
keywords: jq,json命令行工具,jq examples,json格式化工具
description: jq 是一款命令行的 json 处理工具。类似于 lodash 一样，它可以对 json 做各种各样的处理: pick，get，filter，sort，map
date: 2019-10-24 08:00

---

# jq 命令使用及示例

`jq` 是一款命令行的 `json` 处理工具。类似于 `lodash` 一样，它可以对 `json` 做各种各样的处理: `pick`，`get`，`filter`，`sort`，`map`...

由于 `jq` 本身比较简单，以下总结一些经常用到的示例。如果需要更多的细节，可以参考 [jq 官方文档](https://stedolan.github.io/jq/manual/)

先创建一个样例 `demo.jsonl`，`jsonl` 即每行都是一个 `json`，常用在日志格式中

``` json
{"name": "shanyue", "age": 24, "friend": {"name": "shuifeng"}}
{"name": "shuifeng", "age": 25, "friend": {"name": "shanyue"}}
```

由于在后端 API 中会是以 `json` 的格式返回，再次创建一个样例 `demo.json`

``` json
[
  {"name": "shanyue", "age": 24, "friend": {"name": "shuifeng"}},
  {"name": "shuifeng", "age": 25, "friend": {"name": "shanyue"}}
]
```

<!--more-->

+ 原文链接: [jq命令使用及示例](https://shanyue.tech/op/jq) · [github](https://github.com/shfshanyue/op-note/blob/master/jq.md)
+ 系列文章: [当我有台服务器时我做了什么](https://shanyue.tech/op) · [github](https://github.com/shfshanyue/op-note)

### json to jsonl

``` shell
$ cat demo.json | jq '.[]'
{
  "name": "shanyue",
  "age": 24,
  "friend": {
    "name": "shuifeng"
  }
}
{
  "name": "shuifeng",
  "age": 25,
  "friend": {
    "name": "shanyue"
  }
}
```

### jsonl to json

``` shell
$ cat demo.jsonl | jq -s '.'
[
  {
    "name": "shanyue",
    "age": 24,
    "friend": {
      "name": "shuifeng"
    }
  },
  {
    "name": "shuifeng",
    "age": 25,
    "friend": {
      "name": "shanyue"
    }
  }
]
```

### . (_.get)

``` shell
$ cat demo.jsonl | jq '.name'
"shanyue"
"shuifeng"
```

### {} (_.pick)

``` shell
$ cat demo.jsonl| jq '{name, friendname: .friend.name}'
{
  "name": "shanyue",
  "friendname": "shuifeng"
}
{
  "name": "shuifeng",
  "friendname": "shanyue"
}
```

### select (_.filter)

``` shell
$ cat demo.jsonl| jq 'select(.age > 24) | {name}'
{
  "name": "shuifeng"
}
```

### map_values (_.map)

``` shell
$ cat demo.jsonl| jq '{age} | map_values(.+10)'
{
  "age": 34
}
{
  "age": 35
}
```

### sort_by (_.sortBy)

`sort_by` 需要先把 `jsonl` 转化为 `json` 才能进行

``` shell
# 按照 age 降序排列
# -s: jsonl to json
# -.age: 降序
# .[]: json to jsonl
# {}: pick
$ cat demo.jsonl | jq -s '. | sort_by(-.age) | .[] | {name, age}'
{
  "name": "shuifeng",
  "age": 25
}
{
  "name": "shanyue",
  "age": 24
}

# 按照 age 升序排列
$ cat demo.jsonl | jq -s '. | sort_by(.age) | .[] | {name, age}'
{
  "name": "shanyue",
  "age": 24
}
{
  "name": "shuifeng",
  "age": 25
}
```

---
title: json-server mock
date: 2022-02-08 16:56:29
permalink: /pages/c5e701/
categories:
  - 开发环境
tags:
  - 
---
# [JSON-SERVER 配置mock环境](https://github.com/typicode/json-server)

## 全局安装json-server
  `npm install -g json-server`
- 新建 `db.json`
- 运行 `json-server --watch db.json`
- Restful API
  - get 访问
  - post 新建
  - patch 替换
  - put 修改
  - delete 删除

## 项目配置 json-server
- yarn add json-server -D
- 新建文件夹 "__json_server_mock__"，并且在文件夹内新增 db.json
- 配置 `"json-server": "json-server __json_server_mock__/db.json --watch"`

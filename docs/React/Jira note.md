---
title: Jira note
date: 2022-02-09 09:31:58
permalink: /pages/6b2caa/
categories:
  - React
tags:
  - 
---
# Jira note

## baseUrl设置
在 tsconfig.json 文件中，添加 "baseUrl" : "./src"
设置默认根目录
## JWT
表头传  `Authorization: "Bearer ${token}"` 标准格式

## antd
- 安装 antd
- [重写配色方案的话，因为用的是 create-react-app 创建的项目，](https://ant.design/docs/react/use-with-create-react-app-cn#%E8%87%AA%E5%AE%9A%E4%B9%89%E4%B8%BB%E9%A2%98)
- 所以还需要安装 @craco/craco ，来修改默认变量
- 安装好之后，把 package-json 的
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

## 模拟不符合Restful规范的API
可以通过设置中间件来模拟，示例：
```javascript
// __json_server_mock__/middleware.js
module.exports = (req, res, next) => {
    if (req.method === 'POST' && req.path === '/login') {
        if (req.body.username === 'jack' && req.body.password === '123123') {
            return res.status(200).json({
                user: {
                    token: '123'
                }
            })
        } else {
            return res.status(400).json({ message: '用户名或密码错误' })
        }
    }
    next()
}
```

- json-server 配置添加 `--middlewares ./__json_server_mock__/middleware.js`
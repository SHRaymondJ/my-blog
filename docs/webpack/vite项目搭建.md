---
title: vite项目搭建
date: 2022-02-08 14:42:09
permalink: /pages/cdebb8/
categories:
  - webpack
tags:
  - 
---
# vite项目搭建

## 安装

```
$ yarn create @vitejs/app
```



## 打包

- 打包前需要在vite.config.ts中配置`base: './'`, 以确保打包后路径正确

```typescript
import { defineConfig } from 'vite'
import reactRefresh from '@vitejs/plugin-react-refresh'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [reactRefresh()],
  base: './'
})

```

- 兼容性

传统浏览器可以使用 [`@vitejs/plugin-legacy` ](https://github.com/vitejs/vite/tree/main/packages/plugin-legacy)进行打包

配置方法：

```typescript
//vite.config.ts
import legacy from '@vitejs/plugin-legacy'

export default defineConfig({
    plugins: [
        legacy({
       		targets: ['defaults', 'IE 11']	// 兼容IE11
        })
    ]
})
```






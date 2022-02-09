---

title: Javascript 常用第三方库(2021)
date: 2021-08-14 14:44:31
permalink: /pages/115fcf/
categories:
  - 第三方库
tags:
  - 
---
# Javascript 第三方库

## 日期处理类库

### [Moment.js](http://momentjs.cn/) 
现在推荐用 [day.js](https://dayjs.gitee.io/zh-CN/) 大同小异

安装

```
npm install moment --save   # npm
yarn add moment             # Yarn
pnpm install moment         # pnpm
```

引入

```
import moment from 'moment'
```

常用方法：

#### fromNow

获取距离现在的时间间隔

```
moment([2017, 0, 29]).fromNow();     // 4 年前
moment([2017, 0, 29]).fromNow(true); // 4 年
// moment内的参数可以是Date()实例
```

#### locale

获取moment当前的语言环境

```
moment.locale()	// zh-cn
```

#### updateLocale

更新语言环境，并且配置对应显示的文字

```
// 中文
moment.updateLocale('zh-cn', {
    relativeTime : {
        future: "%s 后",
        past:   "%s 前",
        s  : '几秒前',
        ss : '%d 秒',
        m:  "一分钟",
        mm: "%d 分钟",
        h:  "一小时",
        hh: "%d 小时",
        d:  "a 天",
        dd: "%d 天",
        M:  "a 个月",
        MM: "%d 个月",
        y:  "a 年",
        yy: "%d 年"
    }
});
```

#### [diff](http://momentjs.cn/docs/#/displaying/difference/)

时间差

```react
const start = moment(new Date())
const end = moment(new Date('2021/12/4'))
const diff = end.diff(start)	//返回2021/12/4 距离当前时间的事件差
```

#### [duration](http://momentjs.cn/docs/#/durations/creating/)

获取具体时间戳的时长，

有方法：`years()`, `months()` ,`days()`, `hours()`, `minutes()`, `seconds()`

分别获取对应时长

```react
const start = moment(new Date())
const end = moment(new Date('2021/12/4'))
const diff = end.diff(start)	

console.log(moment.duration(diff).hours())	//2021/12/4距离现在的时间差内的小时
```





## React 库

### [react-icons](https://react-icons.github.io/react-icons)

> 图标库

安装

```
npm install react-icons --save
```

使用方法：

```react
import { FaBeer } from 'react-icons/fa'
class Question extends React.Component {
  render() {
    return <h3> Lets go for a <FaBeer />? </h3>	// 跟引入Component一样
  }
}
```



### [styled-components](https://styled-components.com/docs/basics)

> js定义css

安装

```
yarn add styled-components
```

使用方法：

```react
// 引入
import styled from 'styled-components'

// 定义一个div
const UserContainer = styled.div`
	width: 100%;
    display: flex;
    justify-content: center;
`

// 使用div
const UserPage = () => {
    return (
    	<UserContainer>
            <div></div>
        </UserContainer>
    ) 
}

export default UserPage
```

如果使用vscode，推荐安装插件 `vscode-styled-components` 

### [react-datepicker](https://github.com/Hacker0x01/react-datepicker)

日期选择器



### [react-date-range](https://github.com/hypeserver/react-date-range)

日期选择器



## 动态加载库

### [loadable-components](https://loadable-components.com/) 

动态加载。可以降低打包后的文件大小，提升网站加载速度

安装

```
npm install @loadable/component
# or use yarn
yarn add @loadable/component

typescript:
yarn add @types/loadable__component
```

如果用Babel plugin，需要使用cacheKey设置

```react
import React, { useState } from 'react'
import loadable from '@loadable/component'

const AsyncPage = loadable(props => import(`./${props.page}`),{
    cacheKey: props => props.page
})

export default function App() {
    const {page, setPage} = useState('Home')
    return (
    	<button onClick={()=>setPage('Home')}>Home</button>
    	<button onClick={()=>setPage('About')}>About</button>
        {page && <AsyncPage page={page}/>}	// 渲染在这里
    )
}
```

相当于动态加载`./Home` 和`./About` 的：

```react
import React from 'react'
import Home from './Home'
import About from './About'
import { BrowserRouter as Router, Switch, Route, Link }

export default function App() {
    <Router>
    	<Link to='/'>Home</Link>
    	<Link to='/about'>About</Link>
        <Route path='/'>
        	<Home /> 
        </Route>
        <Route path='/about'>
        	<About />
        </Route>
    </Router>
}
```



## Canvas 库

### [Rough.js](https://roughjs.com/)

画手绘风格图片





## 拖曳组件库

### [SortableJS](https://github.com/SortableJS/Sortable)

还没看过，之后看



## 弹窗库

### [popper-core](https://github.com/popperjs/popper-core)

安装

```
npm i @popperjs/core
// or
yarn add @popperjs/core
```

使用

```html
<!DOCTYPE html>
<title>Popper example</title>

<style>
  #tooltip {
    background-color: #333;
    color: white;
    padding: 5px 10px;
    border-radius: 4px;
    font-size: 13px;
  }
</style>

<button id="button" aria-describedby="tooltip">I'm a button</button>
<div id="tooltip" role="tooltip">I'm a tooltip</div>

<script src="https://unpkg.com/@popperjs/core@2"></script>
<script>
  const button = document.querySelector('#button');
  const tooltip = document.querySelector('#tooltip');

  // Pass the button, the tooltip, and some options, and Popper will do the
  // magic positioning for you:
  Popper.createPopper(button, tooltip, {
    placement: 'right',
  });
</script>
```



## 进度条库

### [bar-of-progress](https://github.com/badrap/bar-of-progress)



## Markdown 解析库

### [Marked](https://github.com/markedjs/marked)


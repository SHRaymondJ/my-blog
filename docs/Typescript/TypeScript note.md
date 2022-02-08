---
title: TypeScript note
date: 2022-02-08 14:42:54
permalink: /pages/ad9685/
categories:
  - Typescript
tags:
  - 
---
# TypeScript notes

Install: 

```
npm install -g typescript
```

create tsconfig.json

```
tsc --init
```

install development dependency

```
npm install --save-dev typescript @types/react @types/react-dom ts-loader
```



Compiled to JS 

```
tsc xxx.ts
// normal

tsc xxx.ts -w / tsx xxx.ts --watch
// watching mode

tsc -w
// watching all ts file
```



How to use

```typescript
// object
const person:{
    name:string;
    age:30;
    hobbies: string[];
    role: [number,string]; // [tuple] which means the type of elements inside is fixed.
} = {
    name: 'Raymond',
    age: 30,
    hobbies: ['Sports', 'Cooking'],
    role: [2, 'author']
}

// arguments
const add = (n1: number, n2: number, show: boolen) => {
    //...
}
    
// array
let favoriteActivities: string[];	// all of elements inside of the array should be string type

// enum
enum Role { ADMIN, READ_ONLY, AUTHOR }
//or
enum Role { ADMIN='ADMIN', READ_ONLY=0, AUTHOR='Arther' }
// enum can be any type

// literal type
function combine(resultConversion: 'as-number' | 'as-text') {
    if(resultConversion === 'as-number') {
        //...
    } else {
        //...
    }
}
combine('as-number')	//pass
combine('as-text')		// pass
combine('ddd')			// error

// type aliases
type Combinable = number | string
const obj = {
    input1:Combinable; input2: Combinable;
} = {
    input1: 1,
    input2: 'name' 
}
// this is equal to the below object
const obj = {
    input1: number | string;
    input2: number | string;
} = {
    input1: 1,
    input2: 'name'
}
// function type
let combineValues: Function
combineValues = add;
// or
let combineValues: (a:number, b:number) => number
// which means there's a function named as 'combineValues' has 2 arguments,  types of both the first argument and the second argument are number and the return type is number as well.
```



Function return type:

normal type:

- the return type must be the type which is defined after arguments

```react
function add(n1:number, n2:number): string {
    return n1.toString() + n2.toString()
}
```

Function return void:

- doesn't return anything

```react
function add(n1:number, n2:number): void {
    console.log('Result: ' + n1)
}
```

Function return undefined: 

- doesn't return anything, but the function must include at least one 'return' keyword

```react
function add(n1:number): undefined{
    console.log('Result: ' + n1)
    return	// if you remove this line, your IDE will return error
}
```

函数callback特殊类型

`void`: 无返回值，可以return;

`never`： 必须无返回值，无return

`undefined`： 必须return



interface

接口： 定义复杂数据类型

1. 可用 `?` 定义可选属性

   ```typescript
   interface selectSquare {
       number?: number
       text?: string
   }
   const drawSquare = (square: selectSquare): {color:string; area: number} => {
       // ... 
       return newSquare
   }
   const square = drawSquare({text: 'this is a square'})
   ```

   

2. 可用 `readonly` 定义只读属性

   ```typescript
   interface Point {
       readonly x: number
       readonly y: number
   }
   const point: Point = {x:123,y:456}
   point.x = 2 // error
   ```

   

3. 定义函数参数及返回值

   ```typescript
   interface SearchFunc {
       (source: string, subString: string) : boolean
   }
   let mySearch: SearchFunc
   mySearch = (s, string) => {
       //...
       return true
   }
   ```




泛型:

动态设置函数的入参和返回值类型为同一种类型

```typescript
const identity = <T>(arg: T): T => {
    return arg
}
let output = identity<string>('myString')
```

也可以是数组

```typescript
const loggingIdengtity = <T>(arg: T[]): T[] => {
    console.log(arg.length)
    return arg
}
// 相当于
function loggingIdentity<T>(arg: Array<T>): Array<T> {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}
```

泛型接口：

```typescript
interface GenericIdentityFn<T> {
    (arg: T) : T
}
function identity<T>(arg: T): T {
    return arg
}
let myIdentity: GenericIdentityFn<number> = identity
let myIdentity2: GenericIdentityFn<string> = identity

```





S

### 问题

#### 解决复杂接口返回类型

ts类型可以通过API generator将接口model直接转换为TS文件，`Swagger`, `Restful API` 等


useState是hooks的最基本和最重要的hooks之一，它提供了访问和修改组件状态的能力。



# 一、用法

## 1. 引入（import）

useState从模块`react` 导出的变量，可以通过2种方式引入，

1. 通过React对象useState属性引入（不推荐）

```javascript
import React from 'react'
// React.useState
```

2. 通过import导入useState接口（推荐）

```javascript
import React, { useState } from 'react'
// useState
```

## 2. 接口（interface）

```javascript
const [state, setState] = useState(initialState)
```

### 2.1 类型注解 [1](#r-useState-flow) [2](#r-useState-ts) 

```typescript

type Dispatch<A> = (value: A) => void
type SetStateAction<S> = S | ((prevState: S) => S)
function useState<S = undefined>(initialState: S | () => S): [S | undefined, Dispatch<SetStateAction<S | undefined>>]
```

`initialState`的类型注解为，

```typescript
S
// 或
() => S
```

即，initialState可以

a. state初始值

b. 返回state值的函数



setState的类型注解为，

```typescript
(value: S) => void
// 或
(value: ((prevState: S) => S)) => void // value可以为返回state的函数
```

`setState`的参数支持，

​	a. state值

​	b. 函数，该函数参数为当前state，返回下次render的state值

### 2.2 useState用法

1. `useState`函数返回值的类型是数组，索引第一个值为`state`，索引第二个值为`setState`

   利用数组的解构赋值[3](#r-destructuring_assignment) 可以方便获取这2个值，

   ```javascript
   // stateInfo[0] stateInfo[1]
   const stateInfo = useState(initialState) // ❌ 不推荐
   const [state, setState] = useState([]) // ✅ 推荐
   ```

这种设计在同一个函数内多次使用`useState`情形下优势明显，如果设计成返回对象类型（`key: value`），写法如下，

```javascript
// ⚠️
const { state: list, setState: setList } = useState([]) // ❌ 非官方api
const { state: auth, setState: setAuth } = useState(null) // ❌ 非官方api
```

这样以对象（`key: value`）作为返回类型的设计明显比以数组作为返回类型的设计代码更冗余，因为每次使用都需要复写一次`key`。

1. 多次调用`useState`

   像其他hooks一样，`useState`可以在render函数中多次调用，react库会帮你区分和联系不同的state

```javascript
import { useState } from 'react'
function Page () {
  const [list, setList] = useState([])
  const [auth, setAuth] = useState(null)
  ...
}
```

这可以让**组合**和**拆分**不同关注点的state到不同的逻辑中，这在自定义hooks访问和更新state成为可能，

可以将上面的代码改为，

```javascript
import { useState } from 'react'

// 自定义hook useList
function useList () {
	const [list, setList] = useState([])
	...
	return list
}
// 自定义hook useAuth
function useAuth () {
  const [auth, setAuth] = useState(null)
  ...
  return auth
}
function Page () {
  const list = useList()
  const auth = useAuth()
  ...
}
```

2. `initialState`

   `initialState`可以为表达式或函数，表达式包括字面量、变量、操作子和显式的函数调用。

   由于`useState`使用在render函数中，如果`initialState`为一个计算量大的表达式，则在每次render函数被调用时都会再次执行，函数形式是`useState`提供的被动取值的方式，可以避免计算量大的表达式重复执行的情形。

```javascript
const [state, setState] = useState(someExpensiveComputation(props)) // ❌

const [state, setState] = useState(() => { // ✅ 
  const initialState = someExpensiveComputation(props)
  return initialState;
});
```

3. 使用规则

TBD

3. 

### 2.2 访问和更新state

```
const [state, setState] = useState(initialState)
```

通过state可以**访问**组件状态，通过setState**更新**组件状态

#### 2.2.1 setState 

1. setState用法

这里指的`setState`是`useState`返回值第二个索引值。

set

1. 和this.setState的不同
2. 并发下的setState

# 二、参考

[1] [https://github.com/facebook/react/blob/master/packages/react/src/ReactHooks.js#L83](https://github.com/facebook/react/blob/master/packages/react/src/ReactHooks.js#L83) {#r-useState-flow}

[2] [https://github.com/DefinitelyTyped/DefinitelyTyped/blob/8a1b68be3a64e5d2aa1070f68cc935d668a976ad/types/react/index.d.ts#L852](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/8a1b68be3a64e5d2aa1070f68cc935d668a976ad/types/react/index.d.ts#L852) {#r-useState-ts}


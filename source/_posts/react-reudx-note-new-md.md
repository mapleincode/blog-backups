---
title: React-Redux 笔记 - 其他
date: 2018-05-13 23:51:01
tags: [React.js ]
---

> 参考了阮一峰老师的文章

## combineReducers

### Usage

```
const { combineReducers } from 'redux';
const * as reducers from './reduces';
const reducer = combineReducer(reducers);
```

1. 方便管理 reducer，可以按内容分类 reducer
2. reducer 数据也独立，每个 reducer 所能获取和修改的 state 都是独立的。
3. 获取数据应该要通过 state.xxx.yyy。
4. 当然要跟着修改的地方就是 mapStateToProps 了。

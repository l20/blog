---
title: js常用工具函数
catalog: true
date: 2020-04-09 09:15:44
subtitle:
header-img:
tags:
---

## 数组

### 判断是否存在重复的元素

```js
const isRepeat = (arr) => {
  let hash = {};
  for (let i in arr) {
    if (hash[arr[i]]) {
      return true;
    }
    hash[arr[i]] = true;
  }
  return false;
};
```

e.g:

```js
const arr = ['hello', 'world', 'hello', 'kitty'];
isRepeat(arr); // true
```

### 按数组顺序查找缺失的第一项(从 0 开始)

```js
const findMissing = (arr, key = 'index') => {
  let missing = null;
  for (let i = 0; i < arr.length; i++) {
    const result = arr.find((item) => {
      if (typeof item === 'object') {
        return key in item && i === item[key];
      } else {
        return i === item;
      }
    });
    if (!result) {
      missing = i;
      break;
    } else {
      continue;
    }
  }
  return missing;
};
```

e.g:

```js
const a = [0, 2, 4, 1, 5];
const m = this.findMissing(a); // 3

const a = [{ i: 0 }, { i: 3 }, { i: 1 }, { i: 6 }];
const m = this.findMissing(a, 'i'); // 2
```

### 移动数组元素到某个位置

```js
const moveItem = function (arr, fromIndex, toIndex) {
  const len = arr.length;
  if (toIndex > len - 1) {
    toIndex = len - 1;
  }
  for (const k in arr) {
    const item = arr[k];
    if (k == fromIndex) {
      arr.splice(k, 1);
      arr.splice(toIndex, 0, item);
      break;
    }
  }
  return arr;
};
```

e.g:

```js
const arr = [1, 2, 3, 4, 5];
//将下标2的元素移动到4位置
moveItem(arr, 2, 4); // [1,2,4,5,3]
```

## 字符串

### 测试字符长度，中文算两个字符

```js
const strlen = (str) => {
  let len = 0;
  for (let i = 0; i < str.length; i++) {
    let c = str.charCodeAt(i);
    if ((c >= 0x0001 && c <= 0x007e) || (0xff60 <= c && c <= 0xff9f)) {
      len++;
    } else {
      len += 2;
    }
  }
  return len;
};
```

e.g:

```js
strlen('你好123'); // 7
```

未完待续...

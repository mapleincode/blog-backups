---
title: Nodejs 实现 Python os.walk()
date: 2023-05-09 11:40:13
tags: [nodejs, python]
---



```javascript
const path = require('path');
const fs = require('fs').promises;

async function done (paths, root) {
  const files = await fs.readdir(root);

  const endDirs = [];
  const endFiles = [];

  for (const file of files) {
    const filePath = path.join(root, file);
    const state = await fs.stat(filePath);
    if (state.isDirectory()) {
      paths.push(filePath);
      endDirs.push(file);
    } else {
      endFiles.push(file);
    }
  }

  return [root, endFiles, endDirs];
}

function * walk (root) {
  const paths = [root];

  for (let index = 0; ;index++) {
    if (paths[index] === undefined) {
      break;
    }
    yield done(paths, paths[index]);
  }
}

async function main () {
  let index = 0;
  for (const p of walk('/Users/maple/Desktop')) {
    // 和 yield 不一样的是，of 会多次返回 yield 的内容。而 yield 实际上返回的是 Promise 对象
    // 所以在最外层需要 await 来实现 Promise 对象的执行
    const result = await p;
    console.log(result);
    index++;
    if (index > 4) {
      break;
    }
  }
}

main();

```


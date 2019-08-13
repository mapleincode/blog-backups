---
title: Node 异步三种方式的对比 Demo
date: 2018-06-06 11:37:59
tags: Node.js
---

```javascript
'use strict';

const asyncJs = require('async');

function aa(callback) {
    setTimeout(() => {
        callback(undefined, 1);
    }, 1000);
}

function bb(lastValue, callback) {
    setTimeout(() => {
        callback(undefined, lastValue + 2);
    }, 2000);
}

function cc(lastValue, callback) {
    setTimeout(() => {
        callback(undefined, lastValue + 3);
    }, 3000);
}

const pa = function() {
    return new Promise(function(resolve) {
        aa(function(err, value) {
            resolve(value);
        });
    });
}

const pb = function(lastValue) {
    return new Promise(function(resolve) {
        bb(lastValue, function(err, value) {
            resolve(value);
        });
    });
}

const pc = function(lastValue) {
    return new Promise(function(resolve) {
        cc(lastValue, function(err, value) {
            resolve(value);
        });
    });
}

// Default Callback

// aa(function(err, value) {
//     bb(value, function(err, _value) {
//         cc(_value, function(err, __value) {
//             console.log(__value);
//         });
//     });
// });

// Callback By async.js

// asyncJs.waterfall([
//     function(callback) {
//         aa(function(err, value) {
//             callback(value);
//         });
//     },
//     function(value, callback) {
//         bb(function(err, value) {
//             callback(err, value);
//         });
//     },
//     function(value, callback) {
//         cc(function(err, value) {
//             callback(err, value);
//         });
//     }
// ], function(err, value) {
//     console.log(value);
// });

// asyncJs.waterfall([aa, bb, cc], function(err, result) {
//     console.log(result);
// });

// Promise

// pa().then(function(data) {
//     return pb(data);
// }).then(function(data) {
//     return pc(data);
// }).then(function(data) {
//     console.log(data);
// });

// pa().then(pb).then(pc).then(function(data) {
//     console.log(data);
// });

// async/await

// const done = async function() {
//     console.log(await pc(await pb(await pa())));
// };
// done()

const util = function(func) {
    return function() {
        const args = Array.from(arguments);
        return new Promise(function(resolve, reject) {
            args.push(function(err, result) {
                if(err) {
                    return reject(err);
                }
                resolve(result);
            });
            func.apply(null, args);
        });
    }
}

const ppa = util(aa);
const ppb = util(bb);
const ppc = util(cc);

// ppa().then(ppb).then(ppc).then(function(result) {
//     console.log(result);
// });


```

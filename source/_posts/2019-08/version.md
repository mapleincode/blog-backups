# version 对比

公司一同学为了对比 version 特意引入了一个 module。

对于一般情况，引入成熟的 module 的确可以解决很多问题，尤其是完善性。但是 version 对比却是相当简单的逻辑。



```js
function sort(v1, v2) {
	const diff = v1.shift() - v2.shift();
	return diff !== 0 ? (diff > 0) : sort(v1, v2);
}

function version(v1, v2) {
	v1 = v1.split('.').map(v => parseInt(v));
	v2 = v2.split('.').map(v => parseInt(v));
	return sort(v1, v2);
}
```



只要 version 是规范的都可以对比。如果要支持特殊点的 version 也稍微改动下即可。


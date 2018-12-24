---
title: 0/1 Knapsack in JavaScript
date: 2018-12-24 17:32:48
tags: 分享 算法

---

0/1背包问题的js实现

```javascript

// val => value
// wt => weight
let val = [-1,100,20,60,40] 
let wt = [-1,3,2,4,1] 

// total capacity of the knapsack
let W = 5;

// total items
let n = 4;

// create two dimensional array 
let arr = new Array(n+1);
for (let i = 0; i <= n; i ++) {
	arr[i] = new Array(W+1);
}

// set first row and column to zero
for(let i = 0; i <= n; i++) {
	arr[i][0] = 0
}

for(let i = 0; i <= W; i++ ){
	arr[0][i] = 0;
}

/*
	if wt[i] > w then
	V[i,w] = V[i-1,w]

	else if wt[i] <= w then
	V[i,w] = max( V[i-1,w], val[i] + V[i-1, w - wt[i]] )
*/
for(let i = 1; i <= n; i++) {
	for(let w = 1; w <= W; w++) {
		if (wt[i] > w) {
			arr[i][w] = arr[i-1][w];
		}else if (wt[i] <= w) {
			arr[i][w] = Math.max(arr[i-1][w], (arr[i-1][w - wt[i]] + val[i]));
		}
	}
}

console.table(arr)
```


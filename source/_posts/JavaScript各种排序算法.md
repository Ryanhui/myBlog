---
title: JavaScript各种排序算法
date: 2017-09-30 08:48:13
tags:
---
## 快速排序

### 套路：

（1）在数据集之中，选择一个元素作为"基准"（pivot）。

（2）所有小于"基准"的元素，都移到"基准"的左边；所有大于"基准"的元素，都移到"基准"的右边。

（3）对"基准"左边和右边的两个子集，不断重复第一步和第二步，直到所有子集只剩下一个元素为止。


    var quicksort = function(arr){
	if(arr.length <= 1){return arr;}

	var pivotIndex = Math.floor(arr.length / 2); #取得一个中间值

	var pivot = arr.splice(pivotIndex, 1)[0]; #大于这个中间值的放在右边，小于的放在左边。

	var left = [];
	var right = [];

	for(let i = 0; i < arr.length; i++){
		if(arr[i] < pivot){
			left.push(arr[i]);
		}else{
			right.push(arr[i]);
		}
	}

	return quicksort(left).concat([pivot], quicksort(right)); #递归这个函数即可
	}

	arr = [1,6,3,6,8,3,2,1,9,0,5,34,56];

	console.log(quicksort(arr))

## 冒泡排序

### 套路 

遍历要排序的数列，比较相邻两个元素，如果他们的顺序和我们想要的不一致，就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。冒泡排序的做法有小数上浮 或者 大数下沉两种 ，这里只提及大数下沉的实现。

    var popsort = function(arr){
	for(let i = 0; i < arr.length ; i++){
		for(let j = 0; j < arr.length - i; j++){
			if(arr[j] > arr[j+1]){
				let temp = arr[j];
				arr[j] = arr[j+1];
				arr[j+1] = temp;
			}
		}
	}
		return arr;
	}


## 插入排序

### 套路

* <1>.从第一个元素开始，该元素可以认为已经被排序；
* <2>.取出下一个元素，在已经排序的元素序列中从后向前扫描；
* <3>.如果该元素（已排序）大于新元素，将该元素移到下一位置；
* <4>.重复步骤3，直到找到已排序的元素小于或者等于新元素的位置；
* <5>.将新元素插入到该位置后；
* <6>.重复步骤2~5。

    	var insertionSort = function(arr){

		for(let i = 1; i < arr.length; i++){
		let key = arr[i];
		let j = i - 1;
		while(j >= 0 && arr[j] > key){
			arr[j + 1] = arr[j];
			j--;
		}
			arr[j + 1] = key;
		}
			return arr;
		}
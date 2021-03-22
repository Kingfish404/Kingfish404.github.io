---
layout: post
title: 用typescript实现八大排序-递增
tags: [前端,笔记] 
author: jinyu
desc: typescript真好用，让代码的可维护性和可读性大增
---

用`typescript`实现八大排序算法，分别是：

插入排序，希尔排序，选择排序，堆排序，冒泡排序，快速排序，归并排序，基数排序。

<!-- more -->

先说一下各个排序的复杂度叭，方便之后速查。

| 排序类型 | 平均情况  | 最好情况  | 最坏情况   | 辅助空间  | 稳定性 |
| :------- | :-------- | :-------- | :--------- | :-------- | :----- |
| 插入排序 | O(n²)     | O(n)      | O(n²)      | O(1)      | 稳定   |
| 希尔排序 | O(n^1.3)  | O(nlogn)  | O(n²)      | O(1)      | 不稳定 |
| 选择排序 | O(n²)     | O(n²)     | O(n²)      | O(1)      | 不稳定 |
| 堆排序   | O(nlog₂n) | O(nlog₂n) | O(nlog₂n)  | O(1)      | 不稳定 |
| 冒泡排序 | O(n²)     | O(n)      | O(n²)      | O(1)      | 稳定   |
| 快速排序 | O(nlog₂n) | O(nlog₂n) | O(n²)      | O(nlog₂n) | 不稳定 |
| 归并排序 | O(nlog₂n) | O(nlog₂n) | O(nlog₂n)  | O(n)      | 稳定   |
| 基数排序 | O(d(n+k)) | O(d(n+k)) | O(d(n+kd)) | O(n+kd)   | 稳定   |

下面是我用来测试的例子，其中要注意的是，由于我没有对基数排序进行特殊处理，所以目前被排序的值要求均为非负数。

```typescript
var test_arr: Array<number> = [2, 1, 0, 4, 7, 6, 3, 9, 10, 4, 1, 5, 3, 5, 7];
```

```typescript
// JS默认的排序
function defaultSort(arr: Array<number>): Array<number> {
    let final = new Array(...arr);
    return final.sort(function (a: any, b: any) { return a - b; });
}
```

```typescript
// 插入排序 insertSort
function insertSort(arr: Array<number>): Array<number> {
    let final: Array<number> = new Array(...arr);

    for (let i = 1; i < final.length; i++) {
        let mark: number = final[i];
        let j: number;
        // 寻找插入位置
        for (j = i - 1; j >= 0; j--) {
            if (final[j] < mark) {
                // 插入位置找到
                break;
            } else {
                final[j + 1] = final[j];
            }
        }
        // 进行插入操作
        final[j + 1] = mark;
    }

    return final;
}
```

```typescript
// 希尔排序 shellSort
function shellSort(arr: Array<number>): Array<number> {
    let final: Array<number> = new Array(...arr);

    for (let div = final.length >>> 1; div > 0; div--) {
        // 分步长
        for (let i = div; i < final.length; i++) {
            // 对每个步长对应对子数组进行插入排序
            let temp = final[i];
            let j;
            for (j = i - div; j >= 0 && final[j] > final[j + div]; j -= div) {
                final[j + div] = final[j];
            }
            final[j + div] = temp;
        }
    }
    return final;
}
```

```typescript
// 选择排序 selectionSort
function selectionSort(arr: Array<number>): Array<number> {
    let final: Array<number> = new Array(...arr);

    for (let i = 0; i < final.length; i++) {
        let min = i, temp = final[i];
        // 寻找当前值后面的最小值
        for (let j = i + 1; j < final.length; j++) {
            min = final[min] < final[j] ? min : j;
        }
        // 最小值和当前的交换
        final[i] = final[min];
        final[min] = temp;
    }
    return final;
}
```

```typescript
// 堆排序 heapSort
function heapSort(arr: Array<number>): Array<number> {
    let final: Array<number> = new Array(...arr);

    // 每次找到最大值，放在一个长度递减的数组末尾
    for (let i = final.length - 1; i >= 0; i--) {
        tureMaxHeapSort(final, i);

        // 将堆顶的最大值，和当前数组末尾交换，实现排序
        let temp = final[0];
        final[0] = final[i];
        final[i] = temp;
    }

    // 将最大的元素移动到堆顶
    function tureMaxHeapSort(arr: Array<number>, len: number) {
        let pox_parent = Math.floor(len / 2);
        // 从最后一个父节点开始，遍历其子节点
        for (; pox_parent >= 0; pox_parent--) {
            let left = pox_parent * 2;
            // 如果无右节点，则用左替换
            let right = left + 1 <= len ? left + 1 : left;

            // 找到子节点中最大的
            let max_child = arr[left] >= arr[right] ? left : right;
            if (arr[max_child] > arr[pox_parent]) {
                let temp = arr[pox_parent];
                arr[pox_parent] = arr[max_child];
                arr[max_child] = temp;
            }
        }
    }
    return final;
}
```

```typescript
// 冒泡排序 bubbleSort
function bubbleSort(arr: Array<number>): Array<number> {
    let final: Array<number> = new Array(...arr);

    for (let i = 0; i < final.length; i++) {
        let no_swap: boolean = true;
        for (let j = 0; j < final.length - i - 1; j++) {
            // 如果前一个更大，则和后面的交换
            if (final[j] > final[j + 1]) {
                let temp = final[j + 1];
                final[j + 1] = final[j];
                final[j] = temp;
                no_swap = false;
            }
        }
        // 如果未冒泡，则已经排序完毕
        if (no_swap) {
            break;
        }
    }
    return final;
}
```

```typescript
// 快速排序，quickSort
function quickSort(arr: Array<number>): Array<number> {
    let final: Array<number> = new Array(...arr);

    function tureQuickSort(arr: Array<number>, left: number, right: number): Array<number> {
        if (left >= right) {
            return;
        }
        let i: number = left, j: number = right;
        let mark: number = arr[i];
        // 用mark作为中间值，将数组左右对半分
        while (i < j) {
            // 寻找右边比mark更小的
            while (i < j && arr[j] >= mark) {
                j--;
            }
            arr[i] = arr[j];
            // 寻找左边比mark更大的
            while (i < j && arr[i] <= mark) {
                i++;
            }
            arr[j] = arr[i];
        }
        arr[i] = mark;

        // 递归分左右两个子数组
        tureQuickSort(arr, left, i - 1);
        tureQuickSort(arr, i + 1, right);
    }

    tureQuickSort(final, 0, final.length - 1);

    return final;
}
```

```typescript
// 归并排序 merginSort
function merginSort(arr: Array<number>): Array<number> {
    let final: Array<number> = new Array(...arr);

    function trueMerginSort(arr: Array<number>, left: number, right: number): Array<number> {
        if (right - left == 0) {
            return [arr[right]];
        }
        // 拿到左归并子序列
        let list_a: Array<number> = trueMerginSort(arr, left, Math.floor((left + right) / 2));
        // 拿到右归并子序列
        let list_b: Array<number> = trueMerginSort(arr, Math.floor((left + right) / 2) + 1, right);

        let list_final: Array<number> = [];
        let i: number = 0, j: number = 0;
        // 两个子序列合并成一个有序序列
        while (i < list_a.length && j < list_b.length) {
            if (i < list_a.length && j < list_b.length && list_a[i] <= list_b[j]) {
                list_final.push(list_a[i]);
                i++;
            }
            if (i < list_a.length && j < list_b.length && list_b[j] <= list_a[i]) {
                list_final.push(list_b[j]);
                j++;
            }
        }
        while (i < list_a.length) {
            list_final.push(list_a[i]);
            i++;
        }
        while (j < list_b.length) {
            list_final.push(list_b[j]);
            j++;
        }
        return list_final;
    }

    return trueMerginSort(final, 0, final.length - 1);
}
```

```typescript
// 基数排序 radixSort
function radixSort(arr: Array<number>, radix: number = 10): Array<number> {
    let final: Array<number> = new Array(...arr);
    let base_pool = new Array(radix);

    // 功能函数：获取数字对应低位的值，如:(1234,0)->4
    function getNumAtLoc(n: number, loc: number): number {
        while (n > 0 && loc > 0) {
            n = Math.floor(n / radix);
            loc--;
        }
        return n % radix;
    }

    for (let i = 0; i < 64; i++) {
        // 初始化基数池
        for (let i = 0; i < base_pool.length; i++) {
            base_pool[i] = [];
        }
        // 按照第i位分别入池
        for (let j = 0; j < final.length; j++) {
            base_pool[getNumAtLoc(final[j], i)].push(final[j]);
        }

        // 从池中按顺序取出
        let count = 0;
        for (let j = 0; j < base_pool.length; j++) {
            for (let i of base_pool[j]) {
                final[count] = i;
                count++;
            }
        }

        // 如果所有数都在0，说明已经超过最高位，可以结束了
        if (base_pool[0].length == final.length) {
            break;
        }
    }
    return final;
}
```

## 测试函数

```typescript
console.log('test arr:', test_arr, test_arr.length);

console.log('defaultSort', defaultSort(test_arr));

console.log('insertSort', insertSort(test_arr));

console.log('selectionSort', selectionSort(test_arr));

console.log('heapSort', heapSort(test_arr));

console.log('shellSort', shellSort(test_arr));

console.log('bubbleSort', bubbleSort(test_arr));

console.log('quickSort', quickSort(test_arr));

console.log('merginSort', merginSort(test_arr));

console.log('radixSort', radixSort(test_arr));
```

最完整的可以跑的代码我贴在ubuntu pastebin:

[https://pastebin.ubuntu.com/p/XMYm4HfH4Z/](https://pastebin.ubuntu.com/p/XMYm4HfH4Z/)

## REF

* [八大排序算法总结与java实现](https://itimetraveler.github.io/2017/07/18/%E5%85%AB%E5%A4%A7%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%E6%80%BB%E7%BB%93%E4%B8%8Ejava%E5%AE%9E%E7%8E%B0/#%E5%85%AB%E3%80%81%E5%9F%BA%E6%95%B0%E6%8E%92%E5%BA%8F%EF%BC%88Radix-Sort%EF%BC%89)


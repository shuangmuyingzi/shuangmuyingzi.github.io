### Set集合
Set 是一种叫做集合的数据结构，主要的应用场景在于数据重组；是ES6 新增的一种新的数据结构，类似于数组，但成员是唯一且无序的，没有重复的值。
##### Set 本身是一种构造函数，用来生成 Set 数据结构。
主要有一下特点：
1.成员不能重复
2.只有健值，没有健名，有点类似数组。
3. 可以遍历，方法有add, delete,has

```
new Set([iterable])
```
举个例子：

```
const s = new Set()
[1, 2, 3, 4, 3, 2, 1].forEach(x => s.add(x))

for (let i of s) {
    console.log(i)	// 1 2 3 4
}

// 去重数组的重复对象
let arr = [1, 2, 3, 2, 1, 1]
[... new Set(arr)]	// [1, 2, 3]
```
Set 对象允许你储存任何类型的唯一值，无论是原始值或者是对象引用。

向 Set 加入值的时候，不会发生类型转换，所以5和"5"是两个不同的值。Set 内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，它类似于精确相等运算符（===），主要的区别是**NaN等于自身，而精确相等运算符认为NaN不等于自身。**

```
let set = new Set();
let a = NaN;
let b = NaN;
set.add(a);
set.add(b);
set // Set {NaN}

let set1 = new Set()
set1.add(5)
set1.add('5')
console.log([...set1])	// [5, "5"]
```
另外，两个对象总是不相等的。

```
let set = new Set();

set.add({});
set.size // 1

set.add({});
set.size // 2
```
上面代码表示，由于两个空对象不相等，所以它们被视为两个值。

##### Set 实例的属性和方法

* Set 实例属性

    1. constructor： 构造函数
    
    2. size：元素数量
    
```
let set = new Set([1, 2, 3, 2, 1])

console.log(set.length)	// undefined
console.log(set.size)	// 3
```
* Set 实例方法
    1. 操作方法
        * add(value)：新增，相当于 array里的push
        
        * delete(value)：存在即删除集合中value
        
        * has(value)：判断集合中是否存在 value
        
        * clear()：清空集合
        
        ```
       let set = new Set()
       set.add(1).add(2).add(1)
       
       set.has(1)	// true
       set.has(3)	// false
       set.delete(1)	
       set.has(1)	// false
        ```
        Array.from 方法可以将 Set 结构转为数组
        
        ```
        const items = new Set([1, 2, 3, 2]) 
        //Set(3) {1, 2, 3}
        const array = Array.from(items)
        console.log(array)	// [1, 2, 3]
        // 或
        const arr = [...items]
        console.log(arr)	// [1, 2, 3]
        ```
        
    1. 遍历方法（遍历顺序为插入顺序）
    
       * keys()：返回一个包含集合中所有键的迭代器
       
       * values()：返回一个包含集合中所有值得迭代器
       
       * entries()：返回一个包含Set对象中所有元素得键值对迭代器
       
       * forEach(callbackFn, thisArg)：用于对集合成员执行callbackFn操作，如果提供了 thisArg 参数，回调中的this会是这个参数，没有返回值
       
       ```
       let set = new Set([1, 2, 3])
        console.log(set.keys())	// SetIterator {1, 2, 3}
        console.log(set.values())	// SetIterator {1, 2, 3}
        console.log(set.entries())	// SetIterator {1, 2, 3}
        
        for (let item of set.keys()) {
          console.log(item);
        }	// 1	2	 3
        for (let item of set.entries()) {
          console.log(item);
        }	// [1, 1]	[2, 2]	[3, 3]
        
        set.forEach((value, key) => {
            console.log(key + ' : ' + value)
        })	// 1 : 1	2 : 2	3 : 3
        console.log([...set])	// [1, 2, 3]
       ```
       
       Set 可默认遍历，默认迭代器生成函数是 values() 方法
       
       ```
       Set.prototype[Symbol.iterator] === Set.prototype.values	// true
       ```
       
       所以， Set可以使用 map、filter 方法
       
       ```
       let set = new Set([1, 2, 3])
        set = new Set([...set].map(item => item * 2))
        console.log([...set])	// [2, 4, 6]
        
        set = new Set([...set].filter(item => (item >= 4)))
        console.log([...set])	//[4, 6]
       ```
       因此，Set 很容易实现交集（Intersect）、并集（Union）、差集（Difference）
       
       ```
       let set1 = new Set([1, 2, 3])
        let set2 = new Set([4, 3, 2])
        
        let intersect = new Set([...set1].filter(value => set2.has(value)))
        let union = new Set([...set1, ...set2])
        let difference = new Set([...set1].filter(value => !set2.has(value)))
        
        console.log(intersect)	// Set {2, 3}
        console.log(union)		// Set {1, 2, 3, 4}
        console.log(difference)	// Set {1}
       ```
       



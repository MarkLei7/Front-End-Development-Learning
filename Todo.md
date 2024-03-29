https://juejin.cn/post/6844903512845860872

事件机制 

https://github.com/sisterAn/JavaScript-Algorithms/issues/81

bind

https://juejin.cn/post/6844903779700047885

回流与重绘

https://juejin.cn/post/6844903709772611592

CSS的优先级

https://juejin.cn/post/7016593221815910408

八股文汇总

手写深拷贝
```js
fucntion deepClone(obj={},map=new WeakMap()){
    if(typeof obj!=='object'){
        return obj
    }
    if(map.get(obj)){
        return map.get(obj)
    }
    let result
    if(obj instanceof Array){
        result=[]
    }else{
        result={}
    }
    map.set(obj,result)
    for(const key in obj){
        if(obj.hasOwnProperty(key)){
            result[key]=deepClone(obj[key],map)
        }
    }
    return result

}

```

实现call、apply、bind
```js

Function.prototype.myCall=function(context){
    if(typeof this!=='function'){
        throw new Error("Type Error")
    }
    let args=[...arguments].slice(1)
    let result=null
    context=context||window
    const fnSymbol=Symbol()
    context[fnSymbol]=this
    result=context[fnSymbol](...args)
    delete context[fnSymbol]
    return result
}

Function.prototype.myApply=function(context){
    if(typeof this!=='function'){
        throw new Error("Typer Error")
    }
    let result=null
    context=context||window
    const fnSymbol=Symbol()
    context[fnSymbol]=this
    if(arguments[1]){
        result=context[fnSymbol](...arguments[1])
    }else{
        result=context[fnSymbol]()
    }
    delete context[fnSymbol]
    return result
}

Function.prototype.mybind=function(context){
    const args=[...arguments].slice(1)
    const fn=this
    return function Fn(){
        return fn.apply(
            this instance of Fn?this:context,
            args.concat(...arguments)
        )
    }
}

```
new的实现
```js
function myNew(context){
    const obj={}
    obj._proto_=context.prototype
    const res=context.apply(obj,[...arguments].slice(1))
    return typeof res ==='object'?res:obj
}

```
实现一个 Promise.all
```js
function doPromiseAll(promises){
    return new Promise((resolve,reject)=>{
        if(!Array.isArray(promises)){
            throw new TypeError("promises must be an array")
        }
        const result=[]
        let count=0
        promises.forEach((promise,index)=>{
            Promise.resolve(promise).then((res)=>{
                result[index]=res
                count++
                count===promises.length&&resolve(result)
            },(err)=>{
                reject(err)
            })
        })
    })
}

```
发布订阅模式的典型应用，实现一个 EventMitter 类
```js
{
    //发布订阅模式
    class EventEmitter {
    constructor() {
        this.cache = {}
    }
    on(name, fn) {
        if (this.cache[name]) {
            this.cache[name].push(fn)
        } else {
            this.cache[name] = [fn]
        }
    }
    off(name, fn) {
        let tasks = this.cache[name]
        if (tasks) {
            const index = tasks.findIndex(f => f === fn || f.callback === fn)
            if (index >= 0) {
                tasks.splice(index, 1)
            }
        }
    }
    emit(name, once = false, ...args) {
        if (this.cache[name]) {
            // 创建副本，如果回调函数内继续注册相同事件，会造成死循环
            let tasks = this.cache[name].slice()
            for (let fn of tasks) {
                fn(...args)
            }
            if (once) {
                delete this.cache[name]
            }
        }
    }
}
}

```
防抖
```js
function debounce(func,wait=500,immediate){
    let timeout
    return function(){
        let context=this
        let args=arguments
        if(timeout){
            clearTimeout(timeout)
        }
        if(immediate){
            let callNow=!timeout
            timeout=setTimeout(function(){
                timeout=null
            },wait)
            if(callNow){
                func.apply(context,args)
            }
        }else{
            timeout=setTimeout(function(){
            func.apply(context,args)
            },wait)
        }
       
    }
}
function debounce(func,wait=500){
    let timer=null
    return function(){
        const context=this,args=arguments
        if(timer){
            clearTimeout(timer)
        }
        timer=setTimeout(function(){
            func.apply(context,args)
        },wait)
    }
}

```
节流
```js

function throttle(func,wait=500){
    let pre=0
    return funciton(){
        let now=+new Date()
        const context=this,args=arguments
        if(now-pre>wait){
            func.apply(context,args)
            pre=now
        }
    }
}
function throttle(func,wait){
    let timer=null
    return function(){
        const context=this,args=arguments
        if(!timer){
            timer=setTimeout(function(){
                timer=null
                func.apply(context,args)
            },wait)
        }
    }
}
```
快速排序
```js
function sortArray(nums){
    quickSort(0,nums.length-1,nums)
    return nums
}
function quickSort(start,end,arr){
    if(start<end){
        const mid=sort(start,end,arr)
        quickSort(start,mid-1,arr)
        quickSort(mid+1,end,arr)
    }
}
function sort(start,end,arr){
    const base = arr[start];
  let left = start;
  let right = end;
  while (left !== right) {
    while (arr[right] >= base && right > left) {
      right--;
    }
    arr[left] = arr[right];
    while (arr[left] <= base && right > left) {
      left++;
    }
    arr[right] = arr[left];
  }
  arr[left] = base;
  return left;
}
```
instanceof
```js
function myInstanceof(target,origin){
    if(typeof target!=='object'||target==null) return false
    if(typeof origin!=='function')
        throw new TypeError('origin must be function')
    let proto=Object.getPrototypeOf(target)
    while(proto){
        if(proto===origin.protoType) return true
        proto=Object.getPrototypeOf(proto)
    }
    return false
}
```
数组扁平化
```js
function flat(arr,depth=1){
    if(depth>0){
        return arr.reduce((pre,cur)=>{
            return pre.concat(Array.isArray(cur)?flat(cur,depth-1):cur)
        },[])
    }
    return arr.slice()
}

```
手写 reduce
```js
Array.prototype.reduce=function(cb,initialValue){
    const arr=this
    let total=initialValue||arr[0]
    for(let i= initialValue?0:1;i<arr.length;i++){
        total=cb(total,arr[i],i,arr)
    }
    return total
}
```
带并发的异步调度器 Scheduler
```js

```
去重
```js
function unique(arr){
    return [...new Set(arr)]
}
function unique(arr){
    return arr.filter((item,index,array)=>{
        return array.indexOf(item)===index
    })
}
```

柯里化
```js
function add(){
    const _args=[...arguments]
    const adder=function(){
        _args.push(...arguments)
        return adder
    }
    adder.toString=function(){
        return _args.reduce(function(a,b){return a+b})
    }
    return adder
}
add(1,2)(3).toString()
function add(){
    const args=[...arguments]
    const adder=function(){
        args.push(...arguments)
        return adder
    }
    adder.toString=function(){
        return args.reduce((a,b)=>a+b)
    }
    return adder
}
```

```js
//输入字符串
//返回可能的排列组合
const test_fn=(s='')=>{
        const list=['abc','def','ghi','jkl','mno','pqrs','tuv','wzyz'].map(item=> item.split(""))
        const numString=s.split("")
        const temp=[]
        let ans=[]
        for(let i=0;i<numString.length;i++){
            const numIndex=Number(numString[i])-2
            temp[i]=list[numIndex]
        }
        console.log(temp)
        ans.push(...temp[0])
        for(let i=1;i<temp.length;i++){
            const line=[]
            for(const a of ans){
                for(const c of temp[i]){
                    line.push(a+c)
                }
            }
            ans=[...line]
        }
        return ans

}

test_fn("23")

```

【求满足条件的最长子串的长度】
给定一个字符串，只包含字母和数字，按要求找出字符串中最长（连续）子串的长度，字符字串本身是其最长子串，子串满足：
1. 只包含 n 个字母（a~z,A~Z），其余必须是数字；
2. 字母可以在子串的任意位置
如果找不到满足要求的子串，则返回 -1.

示例1
子串：abc124acb 
n=1
输出：4

示例2
子串：abc124a2cb 
n=1
输出：5

示例3
子串：abc124a2cb 
n=2
输出：6

```js
//滑动窗口

function maxChild(s='',n=1){
        let ans=-1
        let left=0,right=0
        const lengthS=s.length
         //记录出现字母的个数
        let count=0 
        while(right<lengthS){
        //当前右指针是数字
            if(isNumber(s[right])){
                //右边直接增加
            }else{
                //是字母,次数加一
                  count++
                   if(count>n){
                       //次数超过最大值，得甩出左边的字母
                      while(isNumber(s[left])&&left<right){
                          left++
                      }
                      left++
                      count--
                    }    
            }
            ans=Math.max(ans,right-left+1)
                 right++   
              
        }
        return ans
}
//判断是否是字母
const isNumber=(a)=>{
    const num=Number(a)
    return num>=0&&num<=9
}
maxChild('abc124a2cb',3)
```
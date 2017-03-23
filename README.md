# shuffle
洗牌算法
最近工作中需要用到一队人进行随机抽签的算法。数学模型对应起来就是数组的随机排序。
之前为了完成任务，在网上搜到了方法一，就直接使用了。使用过程中被不懂编程的经理
质疑了。遂决定把这个地方整理一下。
## 使用数组sort方法对数组元素随机排序     

  ``` 
  Array.prototype.shuffle = function(n) {
    var len = this.length , num = n?Math.min(n,len):len,arr = this.slice(0)
    arr.sort(function(a,b){
    return Math.random()-0.5
    })
    return arr.slice(0,num)
  }
  ```
  
其中num = n?Math.min(n,len):len用于接受调用参数，当参数存在并小于数组长度时，最后输出结果为随机数列的前n项。

再仔细看这段代码：    

  ```
  arr.sort(function(a,b){
     return Math.random()-0.5
  })
```
我们以以下数据为列：   

  ```
  [1,2,3].sort(function(a,b){

      var a = Math.random()-0.5;
         console.log(a)
       return a;
    })
  ```
打印了两次a,分别为0.034220248033667966，-0.11761075339911564。以冒泡排序为例：  

第一次排序，a>0,升序：    

[2,1,3]  
[2,3,1]  

第二次排序，a小于0,降序 :
[2,1,3]

得到最终结果是[2,1,3].

可以看到这种算法，因为每次冒泡是升序还是降序是随机的，可以认为整个数组是随机排的。
在我使用这种方法的时候，得到的随机数组并不是很理想。特别是初始数组（未排序之前）本身是有序，容易成块出现。

## 随机交换数组内的元素 (原理from underscore.js)：


  ```
  lib = {}
  lib.range = function(min,max) {
    return min+Math.floor(Math.random()*(max-min+1))
  }

  Array.prototype.shuffle = function(n) {
    var len = this.length, num = n?Math.min(n,len):len, arr = this.slice(0), temp, index

    for (var i=0; i<len; i++){
      index = lib.range(i,len-1)
      temp = arr[i]
      arr[i] = arr[index]
      arr[index]=temp    

    }
    return arr.slice(0,num)  
  }
 ```

     
Math.random 生成随机数的范围是[0~1);lib.range生成随机数的范围是[min,max]中的整数。因为这种方法是让数组中的每个数与它
之后的任意一个数进行位置调换。

测试的结果比第一种理想，用时也少一些。PS：第一种方法使用了sort().这种方法的排序还跟浏览器引擎使用的排序方法有关。使用的是冒泡排序
的话就比较慢。

## 随机从原数组抽取一个元素,加入到新数组   


  ```
  lib = {}
  lib.range = function(min,max) {
    return min+Math.floor(Math.random()*(max-min+1))
  }

  Array.prototype.shuffle = function(n) {
    var len = this.length , num = n?Math.min(n,len):len,arr = this.slice(0),result=[],index

    for (var i=0;i<num;i++){
      index = lib.range(0,len-1-i)
      result.push(arr.splice(index,1)[0])        
    }
    return result

  }

  ```
这种方法就是最直观的抽签算法。比起前两种都更加直观，也完全不用考虑最后的顺序是否跟数组的初始位置有关。

好了，最后决定就用第三种方法了。

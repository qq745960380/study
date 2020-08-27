# 手写bind

> 由MDN定义可知：bind()方法创建一个新的函数，在bind()被调用时，这个新函数的this被bind的第一个参数指定，其余的参数将作为新函数的参数供调用时使用。

1. 修改this指向，指向第一个参数
2. 返回新函数
3. bind()第一个参数是this的指向，并且可以传参，返回的新函数可以传剩下的参数
4. bind()返回的函数，函数里可能有会有返回值
5. 当bind()所返回的函数用作构造函数时，那么bind绑定的this将会失效，this指向实例本身

```javascript
//初级版，完成了1，2，3，4
var tea = {
    value: 'red'
}
function bar(){
    console.log(this.value)
}
Function.prototype.bind2 = function(context){
    var self = this
    var args = [].slice.call(arguments,1)
    return function(){
        bargs = [].slice.call(arguments)
        return self.apply(context,args.concat(bargs))
    }
}
```

```javascript
//终极版
Function.prototype.bind2 = function (context) {
    // 判断是不是函数使用了bind2
    if (typeof this !== "function") {
      throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
    }
    var self = this;//this指向具体的函数本身
    
    //获得第二个参数之后的所有参数,arguments是object，类数组结构
    //只有length和索引信息，是和数组一样的。可以转换成数组
    var args = Array.prototype.slice.call(arguments, 1);
    var fNOP = function () {};
    var fBound = function () {
        var bindArgs = Array.prototype.slice.call(arguments);
         //当作为构造函数时，this指向实例，此时结果为true，将函数绑定的this指向该实例，可以让实例获得来自绑定函数的值
         //绑定函数可能是有返回值的
        return self.apply(this instanceof fNOP ? this : context, args.concat(bindArgs));
    }
    //使用空函数过渡，因为修改fBound.prototype的时候，会修改到绑定函数的prototype
    fNOP.prototype = this.prototype;
    //修改返回函数的prototype,实例则可以继承绑定函数的原型中值
    fBound.prototype = new fNOP();
    return fBound;
}
```

文章参考https://github.com/mqyqingfeng/Blog/issues/12
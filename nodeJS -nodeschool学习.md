

### nodeJS -nodeschool学习

@(前端)

nodescholl是一个很棒的学习node知识的网站。

http module例子：

```
var http=require('http');

http.get('http://128.1.10.5:8088/api/cm/v1/relationObject/1000011',
	function callback(response){
		response.setEncoding('utf8');
		response.on("data",function(data){
			console.log(data);
		})
	});
```
> 一般我们在回调函数返回的data中，需要toString()转换为字符串，如果我们使用setEncoding方法，并为其指定参数为utf8，则就不需要手动辣样做了。


## Scoped Variable Access
All nested scopes 遵循相同的规则：每个nested inner scope has outer scope variables,bot NOT vice-versa;

```
function someFunc(){
	var outerVar=1;
	function inner(){
		var innerVar =2;
	}
}
```
inner可以访问到innerVar & outerVar,但是someFunc函数只可以访问到outerVar

## 多层嵌套

![Alt text](./1480043873913.png)


## Scope Tree

![Alt text](./1480043911954.png)

## 全局作用域 & Shadowing

![Alt text](./1480044094862.png)

js runtime在分配变量时，遵循下面步骤：
1. 在当前作用域下寻找
2. 如果没有找到，则立刻在外部作用域找
3. 找到直接跳掉第六步
4. 没有找到，则repeat 第二个不走，直到the global scope is reached
5. 如果在global作用域也没有找到，则创建他（on window/global objects）
6. 分配value

![Alt text](./1480044354835.png)
可以看到 foo前缺少var or let,etc for foo=2,js 运行时遵循上面的算法，最终foo 
成为一个在全局作用域下的变量（window.foo/global.foo）

假设是我们忘记在foo前使用var ,那么这个foo变量原本只是inner作用域下的现在可以在任何作用域下修改，也就是someFunc()现在也可以使用它，当前，这不是我们想要看到的；

## Shadowing
![Alt text](./1480044694242.png)

上面代码即可以叫做 Shadowing,在inner内部的foo变量可以说是Shadow了someFunc()里的foo;


## Closures
它在js语言中是非常重要的一部分，They are what enables the callback-last programming most prominent in node,and provide an excellent mechanism for handling 
the asynchronous nature of most Javascirpt tasks;

![Alt text](./1480045073527.png)

```
function foo(){
	var bar;
	 quux;
	function zip(){
		var quux;
		bar=true;
	}
	return zip;
}
```

## 学习使用.pipe流相关接口

```
var fs=require('fs');
var file=process.argv[2];
fs.createReadStream(file).pipe(process.stdout);
```


```
var through=require('through2');
var tr=through(function(buf,_,next){
	this.push(buf.toString().toUpperCase());
	next();

});
process.stdin.pipe(tr).pipe(process.stdout);	
```

### lines

```
var split = require('split');
var through = require('through2');

var lineCount = 0;
var tr = through(function(buf, _, next) {
    var line = buf.toString();
    this.push(lineCount % 2 === 0 ? line.toLowerCase() + '\n' : line.toUpperCase() + '\n');
    lineCount++;
    next();
});

process.stdin.pipe(split())
    .pipe(tr).pipe(process.stdout)
```


**服务端**

```
 var http = require('http');
  var through = require('through2');

  var server = http.createServer(function (req, res) {
      if (req.method === 'POST') {
          req.pipe(through(function (buf, _, next) {
              this.push(buf.toString().toUpperCase()+'ahaha');
              next();
          })).pipe(res);
      }
      else res.end('send me a POST\n'+req.method);
  });
  server.listen(parseInt(process.argv[2]));
```

然后在命令行启动：》node server.js 8000 &

**客户端**

```
var request = require('request');
var r = request.post('http://localhost:8000');
process.stdin.pipe(r).pipe(process.stdout);
```
在命令行type: > node program.js
						>hello girls



```
 var duck = {
      quack: function() {
        console.log('quack')
      }
    }

    duck.hasOwnProperty('quack') // => true
```
duck初始化用{}符号，and as such it inherits from Object.prototype:
















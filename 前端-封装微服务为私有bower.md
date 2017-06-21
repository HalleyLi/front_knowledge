

### 前端-封装微服务为私有bower

@(前端)

第一步，我们将开发好的微服务，或者其他脚手架发布到服务器上，获得对应的服务地址，http://xxx.x.xx.x:8888/api.
压缩过后的地址为：http://xxx.x.xx.x:8888/api/cm/v1/misc/scripts/resource/compress=false

第二步，将我们得服务api也封装成一个私有库bower放于git项目库中管理，这个私有库的目录如下
![Alt text](./1479201512881.png)

package.json中内容
![Alt text](./1479201618086.png)
bower.json中内容
![Alt text](./1479201654372.png)

```
cmResources.js中的内容是由第一步发布地址的内容，最后我们会对这个webapi里此处内容进行讲解
```

第三部，将上面建的private-bower库添加到我们发布的私有库中；类似下面公有库的方式
![Alt text](./1479201908053.png)

最后，在我们使用的前端项目中，我们在根目录处`touch .bowerrrc`，添加我们私有库发布的路径，代码如下
![Alt text](./1479202061667.png)

在根目录的bower.json文件里添加私有组件（注意，此处添加是为了在空项目初始化时，将对应的所有私有组件都下载下来，不需要手动的bower install something --save了）

    "privateComponents":[
    	"cmResources"
    ]

之后在初始化项目时，通过在终端运行`bower install  cmResources --save`会通过我们得私有仓库路径，找到cmResources资源，并下载到bower_components中，我们需手动将js引用的方式添加到app.html页面中，供前端项目使用。

```
<script src="./static/js/cmResources.js"></script> 
```


下面看一下cmResources中部分代码

```
var cmResources=null;

try{
  cmResources=angular.module('cmResources');
}
catch(err){
  cmResources=angular.module('cmResources',['wapEnv']);
}

cmResources.factory('CMDemoRed',['$resource','envInfo',function($resource,envInfo){
	return $resource(envInfo.cmRes+'/api/cm/v1',{},{
			getSomeThingById:{
				method:'GET',
				url:envInfo.cmRes+'/api/cm/v1/demo/:id',
				params:{
				id:'@id'
				}			
			}
		}
	})
}]);
```
cmResources.js里的代码是由我们开发的后台服务代码转换而成，前面有篇文章，我们讲到过开发框架，唯独没有讲webApi这一层，想要了解webApi层的结构及功能等，请听下回讲解。









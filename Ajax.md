#Ajax
##XMLHttpRequest
##状态码说明
![avatar](/images/QQ20180328-165426.png)
![avatar](/images/QQ20180328-165703.png)

##跨域
###什么是跨域
* 浏览器有同源策略，不允许ajax访问其他域接口
* 跨域条件：协议、域名、端口，有一个不同就算跨域
* 跨域跨域的三个标签： ```<img>、<link>、<script>```

###JSONP

```script
<script>
window.callback = function (data) {
	//这是我们跨域得到的信息
	console.log(data)
}
</script>
<script src="http://coding.m.imooc.com/api.js"></script>
//以上将返回 callback({x:100, y:200})
```
###服务端设置 http header
##例题
* 手动编写一个ajax，不依赖第三方库

	```javascript
	var xhr = new XMLHttpRequest()
	xhr.open("GET", "/api", false) //false表示使用异步
	xhr.onreadystatechange = function () {
		if (xhr.readyState == 4) {
			if (xhr.status == 200) {
				alert(xhr.responseText)
			}
		}
	}
	xhr.send(null)
	```
* 跨域的几种实现方式

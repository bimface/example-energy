# 功能介绍

基于BIMFACE改变构件颜色的扩展功能，可以直观显示停车场内车库占用的比例情况。基于BIMFACE增加图片标签的扩展功能，同时结合室内定位装置读取的地下车库行人的实时坐标模拟行人的走动，点击行人的标签可以高亮显示对应的车位，实现车位引导的功能。

# 效果图
![view](resources/img/view.jpg)

# 主要逻辑

通过BIMFACE前端JavaScript API灵活使用构建协同批注应用场景。

# 前端实现

## 引用BIMFACE的JavaScript显示组件库
```javascript
<script src="https://static.bimface.com/api/BimfaceSDKLoader/BimfaceSDKLoader@latest-release.js" charset="utf-8"></script>
```
## 定义DOM元素，用于在该DOM元素中显示模型或图纸
```javascript
<div id="view3d"></div>
```
## 模型初始化
```javascript
var options = new BimfaceSDKLoaderConfig();
options.viewToken = viewToken;
BimfaceSDKLoader.load(options, successCallback, failureCallback);

function successCallback() {
// 获取DOM元素
var dom4Show = document.getElementById('view3d');
var webAppConfig = new Glodon.Bimface.Application.WebApplication3DConfig();
webAppConfig.domElement = dom4Show;

// 创建WebApplication
window.app = new Glodon.Bimface.Application.WebApplication3D(webAppConfig);

// 添加待显示的模型
app.addView(viewToken);

// 监听添加view完成的事件
app.addEventListener(Glodon.Bimface.Application.WebApplication3DEvent.ViewAdded, function () {

  // 渲染3D模型
  app.render();

  // 从WebApplication获取viewer3D对象
  window.viewer3D = app.getViewer();

  // 配置annotationconfig
  var annotationconfig = new Glodon.Bimface.Plugins.Annotation.AnnotationManagerConfig();
  // 把viewer3D注册到annotation绘制管理器
  annotationconfig.viewer = viewer3D;
  // annotation样式的配置
  annotationconfig.lineColor = new Glodon.Web.Graphics.Color(255, 0, 0, 1);
  annotationconfig.fillColor = new Glodon.Web.Graphics.Color(255, 255, 255, 0);
  annotationconfig.fontSize = 14;
  // 设置annotation的类型
  window.annotationManager= new Glodon.Bimface.Plugins.Annotation.AnnotationManager(annotationconfig);
});

};
function failureCallback(error) {
console.log(error);
};

```
## 工具栏按钮对应方法
  * 箭头
```javascript
annotationManager.setAnnotationType(Glodon.Bimface.Plugins.Annotation.AnnotationTypeOption.Arrow);
```		
  * 矩形
```javascript
annotationManager.setAnnotationType(Glodon.Bimface.Plugins.Annotation.AnnotationTypeOption.Rectangle);
```
  * 圆形
```javascript
annotationManager.setAnnotationType(Glodon.Bimface.Plugins.Annotation.AnnotationTypeOption.Circle);
```
  * 十字
```javascript
annotationManager.setAnnotationType(Glodon.Bimface.Plugins.Annotation.AnnotationTypeOption.Cross);
```
  * 文字
```javascript
annotationManager.setAnnotationType(Glodon.Bimface.Plugins.Annotation.AnnotationTypeOption.Text);
```
  * 设置颜色
```javascript
annotationManager.setLineColor(new Glodon.Web.Graphics.Color(87,222,0,1));
```
  * 设置线宽
```javascript
annotationManager.setLineWidth(3);
```
  * 设置字体
```javascript
annotationManager.setFontSize(14);
```
  * 清空批注
```javascript
let _empty = [];
annotationManager.setAnnotationList(_empty);
```
  * 保存图片
```javascript
annotationManager.createSnapshot(function(data){
	var date = new Date();
	var seperator1 = "-";
	var seperator2 = ":";
	var month = date.getMonth() + 1;
	var strDate = date.getDate();
	let currentdate = date.getFullYear() + seperator1 + month + seperator1 + strDate + " " + date.getHours() + seperator2 + date.getMinutes() + seperator2 + date.getSeconds();
	let _state = annotationManager.getCurrentState();
	let _img = data;
	var obj = {
		name:me.comment,
		time:currentdate,
		src:_img,
		state:_state,
		isClick:false
	}
	me.list.push(obj);
});
```
  * 点击列表获取对应状态
```javascript
annotationManager.setState(data.state);
```



ps. 改Demo基于vue+webpack进行开发打包，如用jquery/React实现同上。

参考API：[http://doc.bimface.com/book/js/articles/basic/index.html](http://doc.bimface.com/book/js/articles/basic/index.html)

# 查看示例

[http://bimface.com/example/energy](http://bimface.com/example/energy)

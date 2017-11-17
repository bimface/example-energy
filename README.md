# 功能介绍

基于BIMFACE改变构件颜色的扩展功能，可以直观显示停车场内车库占用的比例情况。基于BIMFACE增加图片标签的扩展功能，同时结合室内定位装置读取的地下车库行人的实时坐标模拟行人的走动，点击行人的标签可以高亮显示对应的车位，实现车位引导的功能。

# 效果图
![view](resources/img/view.jpg)

# 主要逻辑

通过BIMFACE前端JavaScript API灵活使用构建能源管理应用场景。

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

  // 初始化DrawableContainer
  var drawableConfig = new Glodon.Bimface.Plugins.Drawable.DrawableContainerConfig();
  drawableConfig.viewer = window.viewer3D;
  window.drawableContainer = new Glodon.Bimface.Plugins.Drawable.DrawableContainer(drawableConfig);
});

};
function failureCallback(error) {
console.log(error);
};

```
## JDSDK对象
  * 箭头
```javascript
annotationManager.setAnnotationType(Glodon.Bimface.Plugins.Annotation.AnnotationTypeOption.Arrow);
```		
  * 矩形
```javascript
annotationManager.setAnnotationType(Glodon.Bimface.Plugins.Annotation.AnnotationTypeOption.Rectangle);
```

## 显示实时耗电量的折线图

基于highcharts进行开发



ps. 改Demo基于vue+webpack进行开发打包，如用jquery/React实现同上。

参考API：[http://doc.bimface.com/book/js/articles/basic/index.html](http://doc.bimface.com/book/js/articles/basic/index.html)

# 查看示例

[http://bimface.com/example/energy](http://bimface.com/example/energy)

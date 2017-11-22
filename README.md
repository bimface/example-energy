# 功能介绍

基于BIMFACE的拓展功能，可以对接一些空调设备、能源检测的传感器，以此达到BIM+物联网的功能。选中某一个设备的时候，可以实时显示设备的数据信息，同时也可以远程操控设备。

# 效果图
![view](resources/img/view.png)

# 主要逻辑

通过BIMFACE前端JavaScript API灵活使用构建能耗管理应用场景。

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
## 用到的JSSDK方法
  * 改变构件颜色
```javascript
let modeColor = new Glodon.Web.Graphics.Color(0,255,0,100);
viewer3D.overrideComponentsColorById(me.componentsColor,modeColor);
```		
  * 还原构件颜色
```javascript
viewer3D.restoreComponentsColorById(me.componentsColor);
```
  * 添加标签以及标签操作
```javascript
//循环标签数组
for(let i=0;i<me.tagArray.length;i++){
  if(me.tagArray[i].isOn){
    //获取构件属性
    viewer3D.getComponentProperty(me.tagArray[i].id,function(data){
      let _worldPosition = new Object();
      _worldPosition.x = (data.boundingBox.max.x + data.boundingBox.min.x)/2;
      _worldPosition.y = (data.boundingBox.max.y + data.boundingBox.min.y)/2;
      _worldPosition.z = (data.boundingBox.max.z + data.boundingBox.min.z)/2;
      
      //配置自定义标签
      var config = new Glodon.Bimface.Plugins.Drawable.CustomItemConfig();
      var circle = document.createElement('div');
      circle.className = 'bln';
      config.content = circle;
      config.viewer = window.viewer3D;
      config.index = i;
      config.worldPosition = _worldPosition;

      //生成customItem实例
      var customItem = new Glodon.Bimface.Plugins.Drawable.CustomItem(config);
      //标签点击事件
      customItem.onClick(function(item) {
        me.selectTag(item._config.index);
        viewer3D.setCameraStatus(me.tagArray[i].cameraStatus);
      });

      me.tagList.push(customItem);
    })
  }
}
//添加标签
drawableContainer.addItems(me.tagList);
```

## 显示实时耗电量的折线图

基于highcharts进行开发

## 注

ps. 该Demo基于vue+webpack进行开发打包，如用jquery/React实现同上。

参考API：[http://doc.bimface.com/book/js/articles/basic/index.html](http://doc.bimface.com/book/js/articles/basic/index.html)

# 查看示例

[http://bimface.com/example/energy](http://bimface.com/example/energy)

<template>
  <div id="app">
    <header>
      <a href="#" class="logo"></a>
      <h2>基于BIM+物联网的能耗监控平台<i>官方示例</i></h2>
      <div class="btn-box">
        <a href="https://github.com/bimface/example-energy" target="_blank" class="btn btn-sm btn-primary">源代码</a>
      </div>
    </header>

    <div class="container" :style="{'height':cHeight + 'px'}">
      <div id="view3d"></div>
      <div class="side" v-show="loaded">
        <ul class="list" :style="{'height':(cHeight - 384) + 'px'}">
          <li v-for="(item,i) in tagArray" :class="{'cur':item.isCheck}" @click="showBoard(i)">{{item.name}}</li>
        </ul>

        <ul class="info" :class="{'off':!switchBtn}">
          <li>{{infoBox.co}}</li>
          <li>{{infoBox.tvoc}}</li>
          <li>{{infoBox.pm25}}</li>
          <li>
            <span v-if="infoBox.t<28">{{infoBox.t}}</span>
            <span class="yellow" v-if="infoBox.t>27 && infoBox.t<32">{{infoBox.t}}</span>
            <span class="red" v-if="infoBox.t>31">{{infoBox.t}}</span>
          </li>
          <li>{{infoBox.h}}</li>
          <li>{{infoBox.pm}}</li>
        </ul>

        <div class="btn-box">
          <span class="switch" @click="btnSwitch"></span>
          <span class="up" @click="btnUp"></span>
          <span class="down" @click="btnDown"></span>
        </div>

        <div class="energy">
          实时耗电量
          <div id="energy" class="energy-charts" data-highcharts-chart="0"></div>
        </div>
      </div>

    </div>

    <footer>
      <div class="w1200">
        <div class="copyright">
          Copyright ©2016-2020 <a href="//bimface.com" target="_blank">BIMFACE</a> 京ICP备10021606号-19 <a href="http://www.glodon.com/" target="_blank">广联达</a>旗下产品
        </div>
      </div>
    </footer>
  </div>
</template>

<script>
  import Vue from 'vue'
  import './example-common.css'
  import './btn.less'
  import './energy.less'

  export default {
    data(){
      return {
        cHeight:null,
        info:null,
        switchBtn:false,
        loaded:false,
        tagArray:[{
          isCheck:false,
          isOn:true,
          name:'总经理办公室',
          id:1639053,
          boundingBox: {max: {x: -53101.81, y: 2721.257, z: 1352.483}, min: {x: -53123.81, y: 2527.628, z: 1220.26}},
          cameraStatus:{
            position:{
              x:-56096.073975798034,y:4884.61085529558,z:1455.4962965204097},
              target:{x:-30775.62227342169,y:-23835.759863121624,z:-7429.469801293131},
              up:{x:0,y:-0.000003673205225160099,z:0.9999999999932537},
            },
          arr:[831512,831648,831511,831510,831632,831481,831480,831479,831477,831476,831475,831503,831474,831453,831644,831454,831455,831456,831457,831458,831459,831460,831506,831507,831508,831649,831509,1242435,838898,1280238,831453,831454,831455,831456,831457,831458,831459,831460,831474,831475,831476,831477,831479,831480,831481,831503,831506,831507,831508,831509,831510,831511,831512,831632,831644,831648,831649],
          info:{
            co:800,
            tvoc:0.2,
            pm25:32,
            t:24,
            h:32,
            pm:60
          }
        },{
          isCheck:false,
          isOn:true,
          name:'财务部',
          id:1630239,
          boundingBox: {max: {x: -52981.81, y: 3003.376, z: 1493.396}, min: {x: -53001.81, y: 2883.376, z: 1373.396}},
          cameraStatus:{
            position: { x: -50485.97233161145,y: 1810.6539109981597, z: 1384.4994731227448 },
            target: { x: -86053.88197909827, y: 17646.517699621483, z: -4007.792483273068 },
            up: { x: 0, y: -0.000003673205225160099, z: 0.9999999999932537 }
          },
          arr:[831533,831532,831531,831530,831526,831529,831528,831527,831488,831489,831635,831490,831491,831492,831493,840866,831473,831472,831471,831470,831469,831502,831468,831467,831467,831468,831469,831470,831471,831472,831473,831488,831489,831490,831491,831492,831493,831502,831526,831527,831528,831529,831530,831531,831532,831533,840866],
          info:{
            co:792,
            tvoc:0.2,
            pm25:32,
            t:26,
            h:32,
            pm:60
          }
        },{
          isCheck:false,
          isOn:true,
          name:'会议室',
          id:1636188,
          boundingBox: {max: {x: -46427.89, y: 17902.37, z: 1360.034}, min: {x: -46449.89, y: 17708.74, z: 1227.811}},
          cameraStatus:{
            position: { x:-42892.31421576283, y:17357.586760923277, z: 1236.1869775190482 },
            target: { x: -78428.09044744725, y: 33599.0226988437, z: -3047.7314919041332 },
            up: { x: 0, y: -0.000003673205225160099, z: 0.9999999999932537 }
          },
          arr:[831199,831200,831201,831203,831204,831205,831206,831207,831208,831209,831210,831211,831247,831249,831250,831252,831253,831255,831256,831257,831258,831259,831261,831297,831298,831302,831303,831304,831305,831306,831308,831309,831310,831311,831338,831339,831340,831545,831546,831547,831548,831549,831550,831551,831552,831553,831554,831555,831556,831557,831558,831608,841228,841327,843654,831146,831147,831149,831150,831151,831152,831153,831154,831155,831156,831157,831158,831159,831160,831161,831163,831164,831165,831196,831197,831198,831199,831200,831201,831203,831204,831205,831206,831207,831208,831209,831210,831211,831212,831213,831214,831215,831216,831217,831247,831249,831250,831252,831253,831255,831256,831257,831258,831259,831261,831263,831266,831296,831297,831298,831302,831303,831304,831305,831306,831308,831309,831310,831311,831312,831313,831315,831316,831317,831338,831339,831340,831545,831546,831547,831548,831549,831550,831551,831552,831553,831554,831555,831556,831557,831558,831559,831560,831561,831562,831563,831564,831608,841228,841327,841370,843654,844007,1244007,1495905],
          info:{
            co:802,
            tvoc:0.2,
            pm25:32,
            t:25,
            h:33,
            pm:60
          }
        },{
          isCheck:false,
          isOn:true,
          name:'销售部',
          id:1625158,
          boundingBox: {max: {x: -49050, y: 18892.37, z: 1346.615}, min: {x: -49070, y: 18772.37, z: 1226.615}},
          cameraStatus:{
            position: { x: -51246.26251002617, y: 19076.97565961202, z: 1158.185693794961 },
            target: { x: -13607.57359278682, y: 30360.165471628476, z: 185.3982443508427 },
            up: { x: 0, y: -0.000003673205225160099, z: 0.9999999999932537 }
          },
          arr:[831274,831275,831276,831277,831278,831279,831280,831293,831300,831321,831322,831323,831324,831325,831326,831336,831337,831341,831569,831570,831571,831572,831573,831574,831575,831576,831577,831578],
          info:{
            co:798,
            tvoc:0.2,
            pm25:32,
            t:26,
            h:32,
            pm:60
          }
        },{
          isCheck:false,
          isOn:true,
          name:'采购部',
          id:1634894,
          boundingBox: {max: {x: -57602.53, y: 21253.29, z: 1371.756}, min: {x: -57796.16, y: 21231.29, z: 1239.534}},
          cameraStatus:{
            position: { x: -57210.67194448014, y: 18665.20717556469, z: 1294.4303330885907 },
            target:{ x: -50472.388641952806, y: 57125.12085109189, z: -3217.5724803987805 },
            up: { x: 0,y: -0.000003673205225160099, z: 0.9999999999932537 }
          },
          arr:[831287,831288,831290,831294,831332,831333,831587,831588,831589,831590,831591,831616,831659,831660,844253,844402],
          info:{
            co:812,
            tvoc:0.2,
            pm25:32,
            t:27,
            h:36,
            pm:60
          }
        }],
        componentsColor:[],
        tagList:[],
        infoBox:{
          co:0,
          tvoc:0,
          pm25:0,
          t:0,
          h:0,
          pm:0
        },
      }
    },

    mounted(){
      var  me = this;

      me.cHeight = document.documentElement.clientHeight - 105 - 40;

      me.$http.get('//bimface.com/api/console/share/preview/viewtoken?token=4b042b5d').then((res)=>{
        if(res.data.code == 'success'){
          var viewToken = res.data.data;

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

              // 从WebApplication获取viewer3D对象
              window.viewer3D = app.getViewer();

              app.render();
              me.loaded = true;

              // 初始化DrawableContainer
              var drawableConfig = new Glodon.Bimface.Plugins.Drawable.DrawableContainerConfig();
              drawableConfig.viewer = window.viewer3D;
              window.drawableContainer = new Glodon.Bimface.Plugins.Drawable.DrawableContainer(drawableConfig);

              me.updateTag();
            });

          };
          function failureCallback(error) {
            console.log(error);
          };

        }
      })

      window.chart = new Highcharts.Chart({
        chart: {
          renderTo:'energy',
          type: 'spline',
          animation: Highcharts.svg, // don't animate in old IE
          backgroundColor:'#333333',
          events: {
            load: function() {
              // set up the updating of the chart each second
              var series = this.series[0];
              setInterval(function() {
                var x = (new Date()).getTime(), // current time
                        y = Math.random();
                series.addPoint([x, y], true, true);
              }, 500);
            }
          }
        },
        plotOptions: {
          spline: {
            lineWidth: 1.5,
            fillOpacity: 0.1,
            marker: {
              enabled: false,
              states: {
                hover: {
                  enabled: true,
                  radius: 2
                }
              }
            },
            shadow: false
          }
        },
        colors: ['#68a526'],
        credits:{
          enabled:false
        },
        title: {
          text: null
        },
        xAxis: {
          gridLineColor:'#5d5d5d',
          gridLineWidth:1,
          lineColor:'#5d5d5d',
          labels: {
            enabled: false
          }
        },
        yAxis: {
          title: {
            text: null
          },
          min:0,
          gridLineColor:'#5d5d5d',
          gridLineWidth:1,
          labels: {
            enabled: false
          }
        },
        tooltip: {
          enabled: false
        },
        legend: {
          enabled: false
        },
        exporting: {
          enabled: false
        },
        series: [{
          name: '实时耗电量',
          data: (function() {
            // generate an array of random data
            var data = [],
                time = (new Date()).getTime(),
                i;

            for (i = -10; i <= 0; i++) {
              data.push({
                x: time + i * 500,
                y: Math.random()
              });
            }
            return data;
          })()
        }]
      })
    },

    methods: {
      showBoard: function(num){
        viewer3D.setView(Glodon.Bimface.Viewer.ViewOption.Home);
        this.selectTag(num);
      },

      selectTag: function(num){
        var me = this;
        me.reset();
        me.tagArray[num].isCheck = true;
        viewer3D.restoreComponentsColorById(me.componentsColor);
        if(this.tagArray[num].isOn){
          me.switchBtn = true;
          me.infoBox = me.tagArray[num].info;
          me.componentsColor = me.tagArray[num].arr;
          let modeColor = new Glodon.Web.Graphics.Color(0,255,0,100);
          viewer3D.overrideComponentsColorById(me.componentsColor,modeColor);
        } else {
          me.switchBtn = false;
          this.infoBox = {
            co:0,
            tvoc:0,
            pm25:0,
            t:0,
            h:0,
            pm:0
          };
        }
        viewer3D.render();
      },

      updateTag: function(){
        var me = this;
        me.tagList = [];
        for(let i=0;i<me.tagArray.length;i++){
          if(me.tagArray[i].isOn){
            let _worldPosition = new Object();
            _worldPosition.x = (me.tagArray[i].boundingBox.max.x + me.tagArray[i].boundingBox.min.x)/2;
            _worldPosition.y = (me.tagArray[i].boundingBox.max.y + me.tagArray[i].boundingBox.min.y)/2;
            _worldPosition.z = (me.tagArray[i].boundingBox.max.z + me.tagArray[i].boundingBox.min.z)/2;

            var config = new Glodon.Bimface.Plugins.Drawable.CustomItemConfig();
            var circle = document.createElement('div');
            circle.className = 'bln';
            config.content = circle;
            config.viewer = window.viewer3D;
            config.index = i;
            config.worldPosition = _worldPosition;

            //生成customItem实例
            var customItem = new Glodon.Bimface.Plugins.Drawable.CustomItem(config);
            customItem.onClick(function(item) {
              me.selectTag(item._config.index);
              viewer3D.setCameraStatus(me.tagArray[i].cameraStatus);
            });

            me.tagList.push(customItem);
          }
        }
        drawableContainer.addItems(me.tagList);
        viewer3D.render();
      },

      reset:function(){
        var me = this;
        for(let i=0;i<me.tagArray.length;i++){
          me.tagArray[i].isCheck = false;
        }
      },

      btnSwitch:function(){
        for(let i=0;i<this.tagArray.length;i++){
          if(this.tagArray[i].isCheck){
            if(this.tagArray[i].isOn){
              this.switchBtn = false;
              this.tagArray[i].isOn = false;
              this.infoBox = {
                co:0,
                tvoc:0,
                pm25:0,
                t:0,
                h:0,
                pm:0
              };
              viewer3D.restoreComponentsColorById(this.componentsColor);
              this.updateTag();
            } else {
              this.switchBtn = true;
              this.tagArray[i].isOn = true;
              this.infoBox = {
                co:this.tagArray[i].info.co,
                tvoc:this.tagArray[i].info.tvoc,
                pm25:this.tagArray[i].info.pm25,
                t:this.tagArray[i].info.t,
                h:this.tagArray[i].info.h,
                pm:this.tagArray[i].info.pm
              };
              this.componentsColor = this.tagArray[i].arr;
              let modeColor = new Glodon.Web.Graphics.Color(0,255,0,100);
              viewer3D.overrideComponentsColorById(this.componentsColor,modeColor);
              this.updateTag();
            }
          }
        }
        viewer3D.render();
      },

      updataCharts: function(){
        let inc = [];
        let time = (new Date()).getTime();
        for (let i = -10; i <= 0; i++) {
          inc.push({
            x: time + i * 1000,
            y: Math.random()
          });
        }
        chart.series[0].setData(inc);
        chart.redraw(false);
      },

      btnUp: function(){
        if(this.switchBtn && this.infoBox.t<36){
          this.infoBox.t++;
        }
      },

      btnDown: function(){
        if(this.switchBtn && this.infoBox.t>18){
          this.infoBox.t--;
        }
      }
    }
  }
</script>

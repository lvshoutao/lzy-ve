# lzy-ve
vue+elementUI 快速开发多页面后台，no单页面，no脚手架，no打包编译，适合刚学前端，写后台老程序员，快速完成后台开发，自适应、地图、图表、全局tab 、全页模态框、没有套路规则，完全自由，双击页面即可运行，10分钟完成后台


### LZY-WEB框架及主要模块使用
    #该框架基于Vue，element-ui，jquery，echarts,百度地图API，多页面
    
    Vue是基于数据驱动的前端框架，需要熟悉该框架的生命周期和数据指令。
    
    element-ui是前端组件框架，主要用于页面布局、显示，需要熟悉该框架里各组件的用法。

    自行百度这两个框架，即可进入官网进行学习。
#### 1.项目结构目录
>src
>>assets(存放第三方资源文件)
>>imgs(存放项目中用到的图片)
>>js
>>>common.js　　全局配置及公用js
>>>component.js　　组件文件
>>>component.min.css　　组件样式
>>
>>pages　　　　　页面目录
>>favicon.ico　　图标
>>index.html　　主页面
>>login.html　　登录页面
>>style.css　　 全局样式
#### 2.页面说明
##### 2.1每个页面都要引入的资源

    <html>
        <head>
            <link rel="stylesheet" href="assets/bundle.element.css">
            <link rel="stylesheet" href="style.css">
        </head>
        <body>
            <div class="page" id="page-vue">
                ......
                ......
                ......
                <script type='text/javascript' src='assets/bundle.js' charset='utf-8'></script>
                <script type='text/javascript' src='assets/jquery-1.11.2.min.js' charset='utf-8'></script>
                <script type='text/javascript' src='js/common.js' charset='utf-8'></script>
                <script type='text/javascript' src='js/component.js' charset='utf-8'></script>
            </div>
        </body>
    </html>

如果页面中需要地图还需引入百度地图api

    <script type="text/javascript" src="https://api.map.baidu.com/api?v=2.0&ak=你的key"></script>
    <script type="text/javascript" src="http://echarts.baidu.com/gallery/vendors/echarts/extension/bmap.min.js"></script>

##### 2.2如何实现窗口缩放自适应
    例：
    <el-aside v-bind:style="{width:width+'px'}">
        <site-menu ref="siteMenu" v-on:click-menu="onClickMenu"></site-menu>
    </el-aside>
    <el-main>
        <!--全局tab页-->
        <site-tab ref="siteTab" v-bind:newtab="newTabPage" v-bind:max=8></site-tab>
    </el-main>

    mounted:function(){
        LzyEvent.addCompOnresize(this);
    }

    1.在页面里引用组件时设置 ref="什么都行，页面内不重名" 属性
    2.在页面vue的mounted方法中调用LzyEvent.addCompOnresize(this)即可

##### 2.3Dialog复杂表单编辑及更新

    HttpUtil.goPageDialog(url)
    /**
    * 打开全局Dialog页面
    * @param url 跟目录的相对路径或更目录决绝路径
    */

    HttpUtil.callPageMainfuc(vuePage,methodName,close)
    /**
     * Dialog页面保存后回掉父页面的方法
     * @param {string} vuePage 父页面中的vue实例名
     * @param {string} methodName 父页面中的vue实例中的方法名
     * @param {boolean} close 是否自动关闭detail页面
     */

    HttpUtil.getQueryString(name)
    /**
    * 获取url中的参数
    * @param name 参数名 
    */

...应用场景

    在编辑表单时，一般有2种情况，
    1.表单内容不多时，使用页面内Dialog组件即可
    2.表单内容较多时，需要在新页面中编辑表单，这样一是界面简洁，二是利于代码维护。但是我们又希望编辑保存后无跳转更新上一页面。


    例:
    1.用户列表页面/pages/user/user-list.html，每条列表后有个编辑按钮。
    2.在列表页，第三分页，点击列表中用户id=50的编辑按钮。
    3.打开用户编辑页面/pages/user/user-edit.html，进行编辑后保存，并让列表页，第三分页无跳转刷新

//user-list.html

    <html>
        <head>
            <!--引入css文件-->
        </head>
        <body>
            <div class="page" id="page-userlist">
                <table-wrap 
                    ref="tableWrap"
                    v-bind:datas="tableDatas">
                    <el-table-column label="操作" width="200">
                        <template slot-scope="scope">
                            <el-button size="small" @click="onEdit(scope.row)">编辑</el-button>
                        </template>
                    </el-table-column>
                </table-wrap>
            </div>
            <!--引入js文件-->
            <script>
                var vuePageList = new Vue({
                    el: '#page-userlist',
                    data:{
                        tableDatas:[{id:1,name:"张三"}]
                    },
                    methods:{
                        refreshUserList(){
                            //假设从服务端获取数据
                            HttpUtil.get("user/list",{page:3})
                            .success(data=>this.tableDatas=data);
                        },
                        onEdit(row){
                            HttpUtil.goPageDialog("/pages/manage/user-edit.html?id=" + row.id);
                        }
                    }
                });
            </script>
        </body>
    </html>

//user-edit.html

    <html>
        <head>
            <!--引入css文件-->
        </head>
        <body>
            <div class="page" id="page-useredit">
                <el-form ref="form" label-width="135px" :inline="true" style="width:100%;  margin-top: 36px;">
                    <el-form-item label="用户姓名">
                        <el-input v-model="form.userName" type="text" style="width: 220px"></el-input>
                    </el-form-item>

                    <el-from-item>
                        <el-button type="primary" @click="onSave('form')">保存</el-button>
                    </el-form-item>

                </el-from>
            </div>
            <!--引入js文件-->
            <script>
                var vuePageAdd = new Vue({
                    el: '#page-userlist',
                    data:{
                        form:{id:1,userName:"张三"}
                    },
                    methods:{
                        onSave(row){
                            //调用webapi进行保存
                            HttpUtil.post("user/update",this.form)
                            .success(data=>{
                                //成功后更新user-list.html页面，并关闭编辑页面
                                HttpUtil.callPageMainfuc("vuePageList", "refreshUserList", true);
                            });
                        }
                    }
                });
            </script>
        </body>
    </html>

    




#### 3.首页结构（index.html）
    系统界面分为头部、左边菜单、中间正文三部分

    <html>
        <head>
            <!--引入css-->
            <style>
                /*头部背景样式*/
                .el-header{
                    background-color: #00E598;
                    color: #333;
                    height: 60px;
                    padding: 0px;
                    background:linear-gradient(to top right, #00E598, rgb(8, 179, 8));
                }
                
                /*左边菜单背景样式*/
                .el-aside {
                    background-color: #324A5D;
                    color: #fff;
                }
            </style>
        </head>
        <body>
            <div class="page" id="page-index">

                <el-container>
                    <!--头部-->
                    <el-header>
                        <site-header v-bind:title="title" v-bind:alarm="alarm" ></site-header>
                    </el-header>
                    <el-container>
                        <!--左边菜单-->
                        <el-aside v-bind:style="{width:width+'px'}">
                            <site-menu ref="siteMenu" v-on:click-menu="onClickMenu"></site-menu>
                        </el-aside>
                        <el-main>
                            <!--全局tab页-->
                            <site-tab ref="siteTab" v-bind:newtab="newTabPage" v-bind:max=8></site-tab>
                        </el-main>
                    </el-container>
                </el-container>

                <!--引入js-->
            </div>
        </body>
    </html>

#### 4.内页结构（根据菜单变动的页面）

    内页分为标题、头部、正文、底部分页（如果有分页的话）四部分
    <html>
        <head>
            <!--引入css-->
        </head>
        <body>
            <div class="page" id="page-userlist">

                <div class="page-title">权限管理 › 用户列表</div>
                <div class="page-main">
                    <el-row>
                        <el-col :span="24">
                            <div class="main-head">
                                头部查询条件等
                            </div>

                            <!--列表等，如果有分页，会自动定位在底部-->
                            <table-wrap />
                        </el-col>
                    </el-row>
                </div>
                
                <!--引入js-->
            </div>
        </body>
    </html>



#### 5.大屏页面
    大屏页面分为主题、饼图、折线面积图、柱状图、堆叠图、重要数据项、地图标识，可根据需要自行调整文字说明。

    页面：pages/visual/default.html
    数据：pages/visual/default-chart.js


#### 6.全局css（style.css文件）
    对页面总布局样式的控制，如头部、左边、中间、底部等，可覆写以下样式进行更改：
    
    .site-menu-wrap{} /*菜单背景样式*/

    .site-header-wrap{} /*头部背景样式*/

    .page-footer{} /*页脚（分页）*/

    .chart-wrap{} /*图表背景样式*/

    .ec-extension-bmap .bmap-win{} /*地图小窗口背景样式*/




#### 7.全局配置（common.js文件）
***common.js文件中变量、函数及函数入参、返回数据格式，均不可改变，但函数过程可根据需要，自行修改***

    1. SiteConfig.webApi = "http://你的webapi地址";

    2. SiteConfig.chartColors = []; //图表颜色 

    3. SiteConfig.account = {
            userName:'', //该属性重要，会在头部显示
            CompanyLogo:'', //该属性重要，会在头部显示
            CompanyName:'',
            id:'',//当前登录用户ID
            systemTitle:'',
            accesstoken:'', //访问令牌
            expiresIn:0, //失效时间
            refreshtoken:'',
            roleId:'',
            uId:'', //全局唯一标识
            loginTime:'',//登录时间
            
       }; //当前账号信息（其它属性可根据需要自定义）

    4. SiteConfig.menu =  [
            {
                name:'系统概览',
                index: '1',
                icon: 'icon-monitor',
                url: 'pages/default.html',
            },
            {
                name:'后台管理',
                index: '2',
                icon: 'icon-energy-sum',
                child:[
                    {
                        name:'人员管理',
                        index: '2-1',
                        url: 'pages/manager/user-list.html',
                    }
                ]
            }
        ]; //系统菜单

    5.SiteConfig.mapStyle={}; //地图样式

    6.SiteConfig.cache=true; //是否开启缓存

    7.isLogined(); //判断用户是否登录

    8.userLogin(username,password) //用户登录
      .success(function(data){
          //data.state==0 表示登录成功
      }); 
 

    9.logout(username,password) //注销登录
      .success(function(){
          //注销后处理
      }); 
    
    10.loadCitys(parentCode,level); //详见common.js中文件注释
    /**
    * 异步获取省、市、区数据联动
    * 与selcity组件配合使用，参数和回掉格式不能更改，接口根据自己的api自行调整
    * @param {string} parentCode 父级代码
    * @param {string} level 获取级别
    *          province：省
    *          city：市
    *          district：区
    * @callback {[]} 回调数据
    *          [{name:区域名称,code:区域代码},...]
    * 
    */
  

#### 8.主要组件（component.js文件）
##### 8.1 图表-柱状图
        <chart-bar v-bind:data="data"></chart-bar>
        /**
        * Description 柱状图表插件
        *             依赖echarts
        * 
        * @param data:{}
        *          {
        *              title:'',
        *              x:[],
        *              y:[{
        *                  name:'',
        *                  value:[]
        *                 }],
        *          }
        *          例:{
        *                  title:'一周天气情况',
        *                  x:['周1','周2','周3','周4','周5']
        *                  y:[{
        *                          name:'降雨量',
        *                          value:[10,25,47,57,88]
        *                      },{
        *                          name:'温度',
        *                          value:[25,26,27,30,24]
        *                      }]
        *          }
        * 
        */
##### 8.2 图表-折线图
        <chart-line v-bind:data="data"></chart-line>
        /**
        * Description 折线图表插件
        *             依赖echarts
        * 
        * @param data:{}
        *          {
        *              title:'',
        *              x:[],
        *              y:[{
        *                  name:'',
        *                  value:[]
        *                 }],
        *          }
        *          例:{
        *                  title:'一周天气情况',
        *                  x:['周1','周2','周3','周4','周5']
        *                  y:[{
        *                          name:'降雨量',
        *                          value:[10,25,47,57,88]
        *                      },{
        *                          name:'温度',
        *                          value:[25,26,27,30,24]
        *                      }]
        *          }
        * 
        */
##### 8.3 图表-饼图
        <chart-pie v-bind:data="data"></chart-pie>
        /**
        * Description 饼状图表插件
        *             依赖echarts
        * 
        * @param data:{}
        *          {
        *              title:'',
        *              y:[{value:1,name:''}],
        *          }
        *          例:{
        *                  title:'访问来源',
        *                  y:[{
        *                          name:'广告',
        *                          value:200
        *                      },{
        *                          name:'邮件',
        *                          value:300
        *                      }]
        *          }
        * 
        */
##### 8.4 图表-自定义图表
        <chart-elex v-bind:data="data"></chart-elex>
        /**
        * Description 自定义图表
        *             依赖echarts
        * 
        * @param option:{} 参考百度echarts
        */
##### 8.5 地图
        <chart-map 
            ref="chartMap"
            :mapstyle="mapStyle" 
            :markers="markers" 
            :center="center" 
            :zoom="zoom" 
            v-on:click-marker="onMarker"
            v-on:on-zoom="onZoom">
            <info-win :title="title" :point="point">
                <p>12313123</p>
                <p>12313123</p>
            </info-win>
        </chart-map>
        /**
        * Description 地图插件
        *             依赖echarts，百度地图api
        * @param {Object} mapstyle //参考百度地图样式
        * @param {[]} markers 地图上标注
        *        [{
        *              lng,
        *              lat,
        *              icon:'', //图标路径
        *              text:''
        *        }]
        * @param {Object} center 中心点
        *          {lat:0,lng:0}
        * @param {Number} zoom 级别
        * @param {String} title 地图窗口标题
        * @param {Object} point 地图窗口坐标
        *        {lng:0,lat:0} 只要该坐标改变，窗口就会显示

        * event on-zoom 地图缩放事件
        * event click-marker 点击标记事件
        */


##### 8.6 表格
        <table-wrap ref="tableWrap"
                    v-bind:diffheight="230"
                    v-bind:datas="tableDatas"
                    v-loading="loading"
                    v-bind:page="page"
                    v-on:on-page="onChangePage">
            <el-table-column prop="userName" label="姓名" align="center">
            </el-table-column>
            <el-table-column prop="tel" label="电话" align="center">
            </el-table-column>
        </table-wrap>
        /**
        * Description 表格插件
        * 
        * @param {[]}  datas 展示数据
        * @param {Number} diffheight 
        *          高度差量，即body窗口高度-diffheight=表格高度
        * @param {Object}  page 分页(不设置该属性，不会显示分页)
        *          {
        *             total:总共多少条,
        *             size:每页显示多少条,
        *             index:当前第几页，从1开始
        *          }
        * @param {Boolean} loading 是否显示加载状态
        */

        关于diffheight参数说明，特别是在列表页面，默认理想情况是不管行数多少，只出现表格滚动条，而不出现页面滚动条，设置diffheight就是设置表格到body的上边距

##### 8.7 省市区选择框
        <selcity v-bind:seloption="seloption">
        </selcity>
        /**
        * Description 省市县级联选择框
        * @param seloption 默认选择区域的编码（即是接口中获取的城市代码）
        *      如["33","3310","331001"]
        *      也可只设置前面的一个或2个
        */


        

----
以下是全局组件，默认只能在index.html页中引用


##### 8.9 全局组件-头部
        <site-header v-bind:title="title" v-bind:alarm="alarm" ></site-header>
        /**
        * Description 头部
        * @param title:系统主标题
        * @param alarm:数量显示（消息，通知等）
        */

##### 8.10 全局组件-菜单
        <site-menu ref="siteMenu" v-on:click-menu="onClickMenu"></site-menu>
        /**
        * Description 菜单
        * event click-menu 点击事件,返回菜单项,通常配合全局标签页使用
        *       返回参数{title,url}
        */

##### 8.11 全局组件-标签页
        <site-tab ref="siteTab" v-bind:newtab="newTabPage" v-bind:max=8></site-tab>
        /**
        * Description 全局标签页
        * newTab {Object} 新标签对象
        *      .title 标题
        *      .url 页面url
        * max { number} 可打开标签页的最大数量,若超过max，则会从第一个标签页开始替换
        */

#### 9.Ajax请求
##### 9.1一般请求

        let url = "User/getUser"; //此时的请求地址就是全局配置的SiteConfig.webApi+"User/getuser"

        let param = {id:1};

        let cache = {
            key:string|null 可不填（系统自动生成）
            value:Object
            expire:number 过期时间(毫秒)，0：立刻过期
            level:number 级别 默认1， 1(低),2(中),3(高)
        }; //使用缓存，只有在SiteConfig.cache = true 且不为null时才有效，详细用法见本地缓存 

        //get方式，使用缓存
        HttpUtil.get(url,param,cache)或

        //get方式，不使用缓存
        HttpUtil.get(url,param)
        .success(data=>{
            //获取成功后数据
        }).error(res=>{
            //请求失败处理
        });
        

        //post方式，参数可为一般对象，也可是FormData
        param = new FormData();  
        HttpUtil.post(url,param)
        .success()
        .error();

##### 9.2错误处理
        HttpUtil.post(url,param)
        .success(data=>{})
        .error(res=>{
            //处理错误
        });
        /**
        * 如果页面没订阅error()，或处理后返回true,则会自动进行全局处理，反之则不会进行全局处理
        * res {Object} 错误信息
                {
                    status:请求状态,
                    statusText:状态描述
                }
        */
        这里主要指调用webApi时对产生系统级错误的处理，如网络错误，超时等，而业务级错误只能在success中根据结果自行处理

##### 9.3一次请求多个接口
        有时需要多个接口都返回后，才能进行业务处理，同时又希望请求是异步进行的，此时可用HttpUtil.when()
        let params=[
            {
                url: 'User/List',
                type: 'post',
                param:{
                    "year": "2019",
                }
            },{
                url: 'Pay/List',
                type: 'get',
                param:{
                    "year": "2019",
                }
            }
        ];

        HttpUtil.when(params).success(function (data){
            //请求成功后处理
        });

        /**
        * data {[]} 请求结果是一个数组与参数顺序相对应的数组
        */

#### 10.本地缓存
        $LzyCache对象，HttpUtil请求时的cache，实际上也是调用了该对象，所以HttpUtil请求时产生的缓存，除了系统会自动维护外，也可用该对象自行进行操作，该对象包含3个方法

        1.$LzyCache.set(cache);
        /**
        * 新增缓存
        * @param {Object} cache
        *        {
        *              {string} key|null 可不填（系统自动生成）
        *              {object} value
        *              {number} expire 过期时间(毫秒)，0：立刻过期
        *              {number} level 级别 默认1， 1(低),2(中),3(高)
        *        }
        */


        2.$LzyCache.get(key);
        /**
        * 获取缓存
        * @param {string} key
        * @return value|null
        */


        3.$LzyCache.remove(key,level);
        /**
        * 移除缓存,根据key或level移除缓存
        * @param {string} key
        * @param {number} level
        */

        



#### 11.demo下载   QQ:290852491




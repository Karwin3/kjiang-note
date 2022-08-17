## 1. 下载和安装

### 1. 模板下载

- 集成方案: [vue-element-admin](https://gitee.com/jinglins/vue-element-admin)
- 基础模板: [vue-admin-template](https://gitee.com/jinglins/vue-admin-template)

### 2. 下载依赖

```
# install dependency
cnpm install
```

```
# 运行
npm run dev
```

### 3. 解决报错

```
# 下载依赖
cnpm install core-js
```

## 2. 路由和侧边栏

 当你一个路由下面的 children 声明的路由大于1个时，自动会变成嵌套的模式--如组件页面 

只有一个时，会将那个子路由当做根路由显示在侧边栏--如引导页面 

若你想不管路由下面的 children 声明的个数都显示你的根路由 

你可以设置 alwaysShow: true，这样它就会忽略之前定义的规则，一直显示根路由

 alwaysShow: true

## 3. 图标

一、

1. https://www.iconfont.cn/
2. 下载图片为 svg 格式
3. 将图片复制到 vue-admin-template\src\icons 下
4. 将icon:'' 的内容进行更改即可

二、

1. 直接使用element中的 icon 图标

https://element.eleme.cn/#/zh-CN/component/icon

## 4. 副路由重定向

![img](F:\笔记\05 vue-element-admin.assets\image-1653571549480.png)

## 5. 跨域代理



src\utils\request.js 复制到 src\api\request.js 进行修改 、在封装接口时使用 **import** request **from** '@/api/request'

 原文件中的代码为模拟数据使用，实际开发可直接修改

```js
// src\api\request.js

const service = axios.create({
   // 修改为 'http://leju.bufan.cloud'或直接注释
  baseURL: 'http://leju.bufan.cloud', // url = base url + request url
  // withCredentials: true, // send cookies when cross-domain requests
  timeout: 5000 // request timeout
})
```

https://cli.vuejs.org/zh/config/#devserver-proxy

**devServer.proxy**

- Type: `string | Object`

  如果你的前端应用和后端 API 服务器没有运行在同一个主机上，你需要在开发环境下将 API 请求代理到 API 服务器。这个问题可以通过 `vue.config.js` 中的 `devServer.proxy` 选项来配置。

  `devServer.proxy` 可以是一个指向开发环境 API 服务器的字符串：

  ```js
  module.exports = {
    devServer: {
      proxy: 'http://localhost:4000'
    }
  }
  ```

  这会告诉开发服务器将任何未知请求 (没有匹配到静态文件的请求) 代理到`http://localhost:4000`。

  如果你想要更多的代理控制行为，也可以使用一个 `path: options` 成对的对象。完整的选项可以查阅 [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware#proxycontext-config) 。

  ```js
  module.exports = {
    devServer: {
       proxy: {
        // key:value
        // key:相对路径的开始单词
        '/lejuAdmin': {
          target: defaultSettings.host,
          changeOrigin: true
        }
    },
    // before ==> after
    // before 优先模拟数据 after 优先代理
      after: require('./mock/mock-server.js')
  }
  ```

```js
// settings.js 

 host: 'http://leju.bufan.cloud'
```

```
ps:
修改后重启后生效
```

## 6. 对 auth.js 的修改

目前很少使用 cookie , 新建 myAuth.js 通过 tookie 进行使用

// 配置此文件时为登录使用，因此存入的为token与userinfo两个数据

```js
// myAuth.js

const LEJU_ADMIN_TOKEN = 'leju-admin-token-jk'
const LEJU_ADMIN_USERINFO = 'leju-admin-userinfo-jk'
export function setToken(token) {
  try {
    window.localStorage.setItem(LEJU_ADMIN_TOKEN, token)
  } catch (error) {
    console.log(error)
  }
}

export function getToken() {
  const token = window.localStorage.getItem(LEJU_ADMIN_TOKEN)
  if (token) {
    return token
  } else {
    return null
  }
}

export function reomveToken() {
  window.localStorage.removeItem(LEJU_ADMIN_TOKEN)
}

export function setUserInfo(userInfo) {
  try {
    window.localStorage.setItem(LEJU_ADMIN_USERINFO, JSON.stringify(userInfo))
  } catch (error) {
    console.log(error)
  }
}

export function getUserInfo() {
  const userInfo = window.localStorage.getItem(LEJU_ADMIN_USERINFO)
  if (userInfo) {
    try {
      return JSON.parse(userInfo)
    } catch (error) {
      console.log(error)
    }
  } else {
    return null
  }
}

export function removeUserInfo() {
  window.localStorage.removeItem(LEJU_ADMIN_USERINFO)
}

```

## 7. vue-admin 中图片原生上传

src\components\Tinymce\index.vue

```js
<input type="file" @change="uploadImage">


  uploadImage(e) {
      console.log(e)
      const file = e.target.files[0]
      const formData = new FormData()
      formData.append('file', file)
      // uploadFileOs、token 混入的接口地址和token
      axios.post(this.uploadFileOss, formData, {
        headers: this.token
      }).then(res => {
        console.log(res)
        const { success, data, message } = res.data
        if (success) {
          this.imageSuccessCBK(data.fileUrl)
        } else {
          this.$message.error(message)
        }
      })
    }
    
  // 图片回显到富文本组件
    imageSuccessCBK(val) {
      window.tinymce.get(this.tinymceId).insertContent(`<img class="wscnph" src="${val}" >`)
    }
```

## 8. vue 重置初始化表单

```js
      //   存在表单验证的内容会被清空
      //   this.$refs.form.resetFields()
      //   初始化表单 存在的默认值会被保存
      // this.$options.data() data的初始值
      console.log(this.$options.data())
      this.form = this.$options.data()['form']


// 封装
// 表单初始化
export function initFormData(vm, property) {
  vm[property] = vm.$options.data()[property]
}
```

## 9. vue 路由跳转

vue跳转有总体分为带参跳转和不带参跳转；
带参又有路由显示和不显示的差别

### 1. router-link跳转

```
//不带参数跳转
 <router-link to="/register">
    <span>去这里注册</span>
 </router-link>
 
 //带参跳转 路由显示
 <router-link :to="{path:'register',query:{setid:123456}}">
    <span>去这里注册</span>
 </router-link>
 
 //带参跳转 路由不显示
 <router-link :to="{path:'register',params:{setid:123456}}">
    <span>去这里注册</span>
 </router-link>
```

### 2. this.$router.push()

```
//不带参数跳转
this.$router.push("/register")

//带参跳转 路由显示
this.$router.push({path:"/register",query:{setid:123456}})

//带参跳转 路由不显示 页面刷新数据丢失
this.$router.push({name:"register",params:{setid:123456}})
```

### 3. this.$router.go(1);

```
// 后退一步记录，等同于 history.back()
this.$router.go(-1);

// 在浏览器记录中前进一步，等同于 history.forward()
this.$router.go(1);
```

### 4. this.$router.replace

1.跳转到指定URL，替换history栈中最后一个记录，点击后退会返回至上上一个页面
(A----->B----->C 结果B被C替换 A----->C)
2.设置 replace 属性（默认值: false）的话，当点击时，会调用 router.replace() 而不是 router.push()，于是导航后不会留下 history 记录。即使点击返回按钮也不会回到这个页面
加上replace: true后，它不会向 history 添加新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录。

```
//跳转后无法返回到上一页
this.$router.replace("/register")
//声明式：
<router-link :to="/register" replace></router-link>
// 编程式:
this.$router.replace('/register')
//push方法也可以传replace
this.$router.push({path: '/register', replace: true})
```

```
const { v4: uuidv4 } = require('uuid');
uuidv4(); // ⇨ '1b9d6bcd-bbfd-4b2d-9b5d-ab8dfbbd4bed'
```

## 10. mackdown编辑器

https://www.npmjs.com/package/mavon-editor

Install mavon-editor (安装)

```
$ npm install mavon-editor --save
```

Use (如何引入)

`main.js`:

```js
    // 全局注册
    // import with ES6
    import Vue from 'vue'
    import mavonEditor from 'mavon-editor'
    import 'mavon-editor/dist/css/index.css'
    // use
    Vue.use(mavonEditor)
    new Vue({
        'el': '#main',
        data() {
            return { value: '' }
        }
    })
```

`index.vue`:****

```vue
<div id="main">
    <!--ref="markdownEditor" 属性可以获取转换后的html格式的内容-->
    <!--this.$refs.markdownEditor.d_render 获取-->
    <mavon-editor v-model="value"/>
</div>
```

## 11.  富文本编辑器

**1. 使用完整模板的组件文件中的**Tinymce (文件中使用的时cdn)

```js
# index.vue 
# 导入
import Tinymce from '@/components/Tinymce'
# 挂载模板
 components: {
    Tinymce
  }
#引用
<Tinymce />
```

![image-20220608100100707](F:\笔记\05 vue-element-admin.assets\image-20220608100100707-1654750617151.png)

此版本中的为cdn地址，需翻墙

**or1. 使用静态本地Tinymce**

https://github.com/PanJiaChen/vue-element-admin/releases/tag/4.0.1 在此版本中使用的为静态Tinymce

1. vue-element-admin-4.0.1\src\components\Tinymce 复制到自己的项目中

2. 将 vue-element-admin-4.0.1\public\static\tinymce4.7.5 复制到自己的项目中

3. vue-element-admin-4.0.1\public\index 中引用

```js
<script src="<%= BASE_URL %>static/tinymce4.7.5/tinymce.min.js"></script>
```

4. 挂载使用和1中相同

   

   ps: 报错

   Syntax Error: SassError: expected selector 

   ```js
   # vue-admin-template-master\src\components\Tinymce\components\editorImage.vue
   
   /deep/ 更改为 ::v-deep 
   ```

   

### 富文本图片上传

### 1.打开接口、修改配置

// src\components\Tinymce\index.vue

```vue
 images_upload_handler(blobInfo, success, failure, progress) {
          progress(0)
  }
```

```vue
     images_upload_handler(blobInfo, success, failure, progress) {
          progress(0)
          // Send a POST request
          const formData = new FormData()
          formData.append('file', blobInfo.blob())
          axios({
            method: 'post',
            url: _this.uploadFileOss,
            headers: _this.token,
            data: formData
          }).then(res => {
            console.log(res.data)
            progress(100)
            const { success: succ, data, message } = res.data
            if (succ) {
              // 上传到服务器的图片路径，回填
              success(data.fileUrl)
            } else {
              _this.$message.error(message)
              failure('出现未知问题，刷新页面，或者联系程序员')
            }
          })
        }
```

## 12 . Excel表格的导出与导入

https://panjiachen.github.io/vue-element-admin-site/zh/feature/component/excel.html

### 1. excel表格的导出

**使用**

由于 `Export2Excel`不仅依赖`js-xlsx`还依赖`file-saver`和`script-loader`。

所以你先需要安装如下命令：

```bash
npm install xlsx file-saver -S
npm install script-loader -S -D
```

将 vue-element-admin-master\src\vendor 复制到项目相同路径下

**由于xlsx下载版本更新需进行代码更改**

// Export2Excel.js 

```js
// import XLSX from 'xlsx' => 
import * as XLSX from 'xlsx'
```

由于`js-xlsx`体积还是很大的，导出功能也不是一个非常常用的功能，所以使用的时候建议使用懒加载。使用方法如下：

```js
import('@/vendor/Export2Excel').then(excel => {
  excel.export_json_to_excel({
    header: tHeader, //表头 必填
    data, //具体数据 必填
    filename: 'excel-list', //非必填
    autoWidth: true, //非必填
    bookType: 'xlsx' //非必填
  })
})
```

使用实例

```js
 handleDownload() {
      this.downloadLoading = true
      import('@/vendor/Export2Excel').then(excel => {
        const tHeader = ['Id', 'Title', 'Author', 'Readings', 'Date']
        // 过滤用数组
        const filterVal = ['id', 'title', 'author', 'pageviews', 'display_time']
        const list = this.list
        const data = this.formatJson(filterVal, list)
        excel.export_json_to_excel({
          header: tHeader,
          data,
          filename: this.filename,
          autoWidth: this.autoWidth,
          bookType: this.bookType
        })
        this.downloadLoading = false
      })
    },
  formatJson(filterVal, jsonData) {
      return jsonData.map(v => filterVal.map(j => {
          return v[j]
      }))
    }
```



### 2. excel表格的导入

https://panjiachen.github.io/vue-element-admin-site/zh/feature/component/excel.html#excel-%E5%AF%BC%E5%85%A5

## 13. ECharts 主页大屏

v4 （基础讲解好）

https://echarts.apache.org/v4/zh/index.html

v5

https://echarts.apache.org/handbook/zh/basics/download/



### 1. npm 安装 ECharts

你可以使用如下命令通过 npm 安装 ECharts

```text
npm install echarts --save
```

### 2. 引入 ECharts

```js
import * as echarts from 'echarts';

mounted(){
    // 基于准备好的dom，初始化echarts实例
    var myChart = echarts.init(document.getElementById('main'));
    // 绘制图表
    const options = {
      title: {
        text: 'ECharts 入门示例'
      },
      tooltip: {},
      xAxis: {
        data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']
      },
      yAxis: {},
      series: [
        {
          name: '销量',
          type: 'bar',
          data: [5, 20, 36, 10, 10, 20]
        }
      ]
    };
   	// 加载
    myChart.setOption(options)
}


```

## 14. 打包及相关问题

### 1. 打包命令

```
npm run build:prod
```

### 2. 代理问题

配置的代理会失效，采用request.js中的BaseURL

### 3. 跨域问题

开发阶段

​	配置代理

上线部署

	1. 集成部署：将dist给后台，后台把dist文件内容放到后台代码中
 	2. 独立部署：
      	1. nginx 配置代理
      	2. 后台配置cors
	3. 自动化部署 ： 提交代码就ok

## 15. 部署后的白屏问题

在gitee中部署项目后，需修改配置文件

//vue.config.js

```
/**
   * You will need to set publicPath if you plan to deploy your site under a sub path,
   * for example GitHub Pages. If you plan to deploy your site to https://foo.github.io/bar/,
   * then publicPath should be set to "/bar/".
   * In most cases please use '/' !!!
   * Detail: https://cli.vuejs.org/config/#publicpath
   */
   // 将'修改为gitee中的命名空间'
  publicPath: '/',
```

## 16.部署后图片无法上传

el-upload中的action，使用的是mix中的混入,没有走request.js中的BaseURL，在uploadFileOss中填上BaseURL即可

```
:action="uploadFileOss"
```

```
// mix
 *// 通用素材接口*
   //uploadFileOss: '/lejuAdmin/material/uploadFileOss'
   uploadFileOss: 'http://leju.bufan.cloud/lejuAdmin/material/uploadFileOss'
```


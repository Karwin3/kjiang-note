

## 1 页面标题

### 1. H5平台

// pages.json

```javascript
{
            "path": "pages/index/index",
            "style": {
                "navigationBarTitleText": "uni-app",
                // 编译到h5平台时去掉页面标题
                "h5":{
                    "titleNView":false
                }
            }
        }
```

### 2. 自定义导航栏

```javascript
// pages.json
// H5和小程序端都会居顶
{
            "path": "pages/index/index",
            "style": {
                "navigationBarTitleText": "uni-app",
                // 自定义导航栏
                "navigationStyle":"custom"
            }
        }
```



非H5端，手机顶部状态栏区域会被页面内容覆盖。这是因为窗体是沉浸式的原因，即全屏可写内容。uni-app提供了状态栏高度的css变量 –status-bar-height ，如果需要把状态栏的位置从前景部分让出来，可写一个占位div，高度设为css变量。

```html
<template>
  <view>
      <view class="status_bar">
          <!-- 这里是状态栏 -->
      </view>
      <view> 状态栏下的文字 </view>
  </view>
</template>    
<style>
  .status_bar {
      padding-top: var(--status-bar-height);
      width: 100%;
  }
</style>
```



微信小程序端获取胶囊信息

​	在H5端执行会报错，条件编译进行处理

```
 uni.getMenuButtonBoundingClientRect();
```

样式修改



## 2. 条件编译

https://uniapp.dcloud.net.cn/tutorial/platform.html#preprocessor

条件编译是用特殊的注释作为标记，在编译时根据这些特殊的注释，将注释里面的代码编译到不同平台。

**写法：**以 #ifdef 或 #ifndef 加 **%PLATFORM%** 开头，以 #endif 结尾。 

- \#ifdef：if defined 仅在某平台存在

- \#ifndef：if not defined 除了某平台均存在

- **%PLATFORM%**：平台名称

  | 值        | 生效条件   |
  | :-------- | :--------- |
  | H5        | H5         |
  | MP-WEIXIN | 微信小程序 |
  | APP-PLUS  | App        |

在<script></script> 、<style></style>、app.json中

```javascript
// #ifdef  %PLATFORM%
平台特有的API实现
// #endif
```

在<template></template>中

```html
<!--  #ifdef  %PLATFORM% -->
平台特有的组件
<!--  #endif -->
```



## 3. 开发环境和生产环境

```javascript
if(process.env.NODE_ENV === 'development'){
    console.log('开发环境')
}else{
    console.log('生产环境')
}
```

## 4. 页面跳转

https://uniapp.dcloud.net.cn/api/router.html

### 1. uni.navigateTo(OBJECT)

保留当前页面，跳转到应用内的某个页面，使用`uni.navigateBack`可以返回到原页面。

| 参数 | 类型   | 必填 |
| :--- | :----- | :--- |
| url  | String | 是   |

### 2. uni.redirectTo(OBJECT)

关闭当前页面，跳转到应用内的某个页面。

### 3. uni.switchTab(OBJECT)

跳转到 tabBar 页面，并关闭其他所有非 tabBar 页面

### 4. uni.reLaunch(OBJECT)

关闭所有页面，打开到应用内的某个页面。

### 5. uni.navigateBack(OBJECT)

关闭当前页面，返回上一页面或多级页面。可通过 `getCurrentPages()` 获取当前的页面栈，决定需要返回几层。

| 参数  | 类型   | 必填 | 默认值 |
| :---- | :----- | :--- | :----- |
| delta | Number | 否   | 1      |

### 6. navigator 组件

```vue
	<navigator url="./demo/demo">navigator-navigate</navigator>
	<navigator url="./demo/demo" open-type="redirect">navigator-redirect</navigator>
	<navigator url="../home/home" open-type="switchTab">navigator-switchTab</navigator>
	<navigator url="../home/home" open-type="reLaunch">navigator-reLaunch</navigator>

	<navigator open-type="navigateBack" :delta="1">navigator-navigateBack</navigator>
```
| 属性名    | 类型   | 默认值   | 说明                                                         |
| :-------- | :----- | :------- | :----------------------------------------------------------- |
| url       | String |          | 应用内的跳转链接，值为相对路径或绝对路径，如："../first/first"，"/pages/first/first"，注意不能加 `.vue` 后缀 |
| open-type | String | navigate | 跳转方式                                                     |
| delta     | Number |          | 当 open-type 为 'navigateBack' 时有效，表示回退的层数        |

**open-type 有效值**

| 值           | 说明                                   |
| :----------- | :------------------------------------- |
| navigate     | 对应 uni.navigateTo 的功能             |
| redirect     | 对应 uni.redirectTo 的功能             |
| switchTab    | 对应 uni.switchTab 的功能              |
| reLaunch     | 对应 uni.reLaunch 的功能               |
| navigateBack | 对应 uni.navigateBack 的功能           |
| exit         | 退出小程序，target="miniProgram"时生效 |

## 5 页面通讯

### 1. 路由传参

适用场景:
适合于简单的页面传参，传递的参数比较少，如传递的比较多，推荐适用globalData、本地缓存、vuex。

A页面向B页面传递参数

```javascript
<navigator url="./demo/demo?id=1">navigator-navigate</navigator>


uni.navigateTo({  
    url: 'test/test?id=1'  
});
```

B页面接收A页面传递的参数

```javascript
export default {  
    onLoad (options) { //option为object类型，会序列化上个页面传递的参数  
        console.log(options); //打印出上个页面传递的参数。  
    }  
}
```

### 2. gloabalData

小程序有globalData，这是一种简单的全局变量机制。这套机制在uni-app里也可以使用，并且全端通用。

可以直接修改，页面刷新恢复初始值。

以下是 App.vue 中定义globalData的相关配置：

```javascript
<script>  
    export default {  
        globalData: {  
            text: 'text'  
        }
    }  
</script>
```

js中操作globalData的方式如下： `getApp().globalData.text`

### 3. 本地存储

https://uniapp.dcloud.net.cn/api/storage/storage.html

```html
		<button @click="setData">异步存储数据</button>
		<button @click="getData">异步获取数据</button>
		<button @click="setDataSync">同步存储数据</button>
		<button @click="getDataSync">同步获取数据</button>
		<button @click="removeDataSync">同步删除某条数据</button>
		<button @click="clearDataSync">同步清除全部数据</button>

<!-- 
		存储对象数据
			对象 => 字符串
			JSON.stringify()	
		获取数据
			字符串 => 对象
			JSON.parse()
-->
```



```js
		setData() {
				uni.setStorage({
					key: 'name',
					data: 'zs',
					success(res) {
						console.log(res);
					},
					fail(error) {
						console.log(error);
					}
				})
			},
			getData() {
				uni.getStorage({
					key: 'name',
					success(res) {
						console.log(res);
					},
					fail(error) {
						console.log(error);
					}
				})
			},
			setDataSync() {
				try {
					uni.setStorageSync('book', 'js');
				} catch (e) {
					//TODO handle the exception
					console.log(e);
				}
			},
			getDataSync() {
				try {
					var book = uni.getStorageSync('book');
					console.log(book);
				} catch (e) {
					//TODO handle the exception
					console.log(e);
				}
			},
			removeDataSync() {
				try {
					uni.removeStorageSync('book')
				} catch (e) {
					//TODO handle the exception
					console.log(e);
				}

			},
			clearDataSync() {
				try {
					uni.clearStorageSync()
				} catch (e) {
					//TODO handle the exception
					console.log(e);
				}
			}
```

### 4. vuex

http://doc.bufanui.com/docs/a411524237/a411524237-1cphrtsa0tkik

https://vuex.vuejs.org/zh/guide/state.html#mapstate-%E8%BE%85%E5%8A%A9%E5%87%BD%E6%95%B0

在 uni-app 项目根目录下，新建 store 目录，在此目录下新建 index.js 文件。在 index.js 文件配置如下：

```javascript
// 页面路径：store/index.js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex);//vue的插件机制

//Vuex.Store 构造器选项
const store = new Vuex.Store({
    state:{//存放状态
        "username":"foo"
        "age":18
    }
})
export default store
```

在 main.js 中导入文件。

```javascript
<!-- 页面路径：main.js -->
import App from './App'

// #ifndef VUE3
import Vue from 'vue'
import store from './store'

Vue.prototype.$store = store
Vue.config.productionTip = false
App.mpType = 'app'

// 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
const app = new Vue({
	store,
	...App
})
app.$mount()
// #endif

// #ifdef VUE3
import {
	createSSRApp
} from 'vue'
export function createApp() {
	const app = createSSRApp(App)
	return {
		app
	}
}
// #endif

```

#### 1. 获取数据

##### 1. this.$store

​	在组件中使用，通过 `this.$store` 访问到 `state` 里的数据。

```vue
	<view>{{$store.state.username}}</view>
	<view>{{$store.state.age}}</view>
```
##### 2. mapState

​	通过 `mapState` 辅助函数获取。

```vue
<!-- 页面路径：pages/index/index.vue -->
<script>
    import { mapState } from 'vuex'//引入mapState
    
    export default {
        data() {
            return {
                username: 'ls'
            }
        },
        computed:{
           // 可能与data中的数据存在命名冲突，vuex数据中的优先级大于data
			...mapState(['username', 'age'])
        } 
    }
</script>
```

#### 2. 修改数据

##### 1.mutions

**Vuex中store数据改变的唯一方法就是mutation**



**传参**

你可以向 `store.commit` 传入额外的参数，即 `mutation` 的 载荷（payload）

```js
// 页面路径：store/index.js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex); //vue的插件机制

//Vuex.Store 构造器选项
const store = new Vuex.Store({
	state: { //存放状态
		"username": "foo",
		"age": 18
	},
	mutations: {
		// payload 载荷
		ageUpdate(state, payload) {
			state.age = payload;
		}
	}
})
export default store

```

```vue
	 <!-- 页面路径：pages/index/index.vue -->
		methods:{
			upDateStateAge() {
				this.$store.commit('ageUpdate', 30)
			}
		}
```

##### 2.mapMutations

```vue
<!-- 页面路径：pages/index/index.vue -->
<script>
    import { mapMutations } from 'vuex'//引入mapMutations
    
    export default {
        data() {
            return {
                
            }
        },
       methods:{
           ...mapMutations(['ageUpdate']),
           upDateStateAge() {
				this.ageUpdate(30)
			}
       }
    }
</script>
```
##### 3.action 异步修改

```js
// 页面路径：store/index.js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex); //vue的插件机制

//Vuex.Store 构造器选项
const store = new Vuex.Store({
	state: { //存放状态
		"username": "foo",
		"age": 18
	},
	mutations: {
		// payLoad 载荷
		ageUpdate(state, payLoad) {
			state.age = payLoad;
		}
	},
	actions: {
		// context 上下文对象
		ageUpdateAction(context, payLoad) {
			setTimeout(() => {
				context.commit('ageUpdate', payLoad);
			}, 2000)
		}
	}
})
export default store

```
```vue
	 <!-- 页面路径：pages/index/index.vue -->
		methods:{
			upDateStateAgeAction() {
				this.$store.dispatch('ageUpdateAction', 40)
			}
		}		
```

##### 4. mapActions

```vue
<!-- 页面路径：pages/index/index.vue -->
<script>
    import { mapActions } from 'vuex'//引入mapActions
    
    export default {
        data() {
            return {
                
            }
        },
       methods:{
           ...mapMutations(['ageUpdateAction']),
           upDateStateAgeAction() {
				this.ageUpdateAction(40)
			}
       }
    }
</script>
```

##### 命名重复情况

用对象解决

```js
			...mapState({
				un: state => state.username
			})
			...mapMutations({
				muAge: 'ageUpdate'
			}),
			...mapActions({
				acAge: 'ageUpdateAction'
			})
```

## 6 上传资源

### 1. 本地获取资源

**uni.chooseImage(OBJECT)**

从本地相册选择图片或使用相机拍照。

App端如需要更丰富的相机拍照API（如直接调用前置摄像头），参考[plus.camera(opens new window)](https://www.html5plus.org/doc/zh_cn/camera.html)

**OBJECT 参数说明**

| 参数名     | 类型          | 必填 | 说明                                                         | 平台差异说明                              |
| :--------- | :------------ | :--- | :----------------------------------------------------------- | :---------------------------------------- |
| count      | Number        | 否   | 最多可以选择的图片张数，默认9                                | 见下方说明                                |
| sizeType   | Array<String> | 否   | original 原图，compressed 压缩图，默认二者都有               | App、微信小程序、支付宝小程序、百度小程序 |
| extension  | Array<String> | 否   | 根据文件拓展名过滤，每一项都不能是空字符串。默认不过滤。     | H5(HBuilder X2.9.9+)                      |
| sourceType | Array<String> | 否   | album 从相册选图，camera 使用相机，默认二者都有。如需直接开相机或直接选相册，请只使用一个选项 |                                           |
| crop       | Object        | 否   | 图像裁剪参数，设置后 sizeType 失效                           | App 3.1.19+                               |
| success    | Function      | 是   | 成功则返回图片的本地文件路径列表 tempFilePaths               |                                           |
| fail       | Function      | 否   | 接口调用失败的回调函数                                       | 小程序、App                               |
| complete   | Function      | 否   | 接口调用结束的回调函数（调用成功、失败都会执行）             |                                           |

**crop 参数说明**

| 参数名  | 类型    | 必填 | 说明                                                         | 平台差异说明 |
| :------ | :------ | :--- | :----------------------------------------------------------- | :----------- |
| quality | Number  | 否   | 取值范围为1-100，数值越小，质量越低（仅对jpg格式有效）。默认值为80。 |              |
| width   | Number  | 是   | 裁剪的宽度，单位为px，用于计算裁剪宽高比。                   |              |
| height  | Number  | 是   | 裁剪的高度，单位为px，用于计算裁剪宽高比。                   |              |
| resize  | Boolean | 否   | 是否将width和height作为裁剪保存图片真实的像素值。默认值为true。注：设置为false时在裁剪编辑界面显示图片的像素值，设置为true时不显示 |              |

### 2. 上传到服务器

**uni.uploadFile(OBJECT)**

将本地资源上传到开发者服务器，客户端发起一个 `POST` 请求，其中 `content-type` 为 `multipart/form-data`。
如页面通过 [uni.chooseImage](https://uniapp.dcloud.net.cn/api/media/image#chooseimage) 等接口获取到一个本地资源的临时文件路径后，可通过此接口将本地资源上传到指定服务器。另外选择和上传非图像、视频文件参考：[https://ask.dcloud.net.cn/article/35547 (opens new window)](https://ask.dcloud.net.cn/article/35547)。

> 在各个小程序平台运行时，网络相关的 API 在使用前需要配置域名白名单。

**OBJECT 参数说明**

| 参数名   | 类型     | 必填                        | 说明                                                         | 平台差异说明                                |
| :------- | :------- | :-------------------------- | :----------------------------------------------------------- | :------------------------------------------ |
| url      | String   | 是                          | 开发者服务器 url                                             |                                             |
| files    | Array    | 是（files和filePath选其一） | 需要上传的文件列表。**使用 files 时，filePath 和 name 不生效。** | App、H5（ 2.6.15+）                         |
| fileType | String   | 见平台差异说明              | 文件类型，image/video/audio                                  | 仅支付宝小程序，且必填。                    |
| file     | File     | 否                          | 要上传的文件对象。                                           | 仅H5（2.6.15+）支持                         |
| filePath | String   | 是（files和filePath选其一） | 要上传文件资源的路径。                                       |                                             |
| name     | String   | 是                          | 文件对应的 key , 开发者在服务器端通过这个 key 可以获取到文件二进制内容 |                                             |
| header   | Object   | 否                          | HTTP 请求 Header, header 中不能设置 Referer。                |                                             |
| timeout  | Number   | 否                          | 超时时间，单位 ms                                            | H5(HBuilderX 2.9.9+)、APP(HBuilderX 2.9.9+) |
| formData | Object   | 否                          | HTTP 请求中其他额外的 form data                              |                                             |
| success  | Function | 否                          | 接口调用成功的回调函数                                       |                                             |
| fail     | Function | 否                          | 接口调用失败的回调函数                                       |                                             |
| complete | Function | 否                          | 接口调用结束的回调函数（调用成功、失败都会执行）             |                                             |

**注意**：

- App支持多文件上传，微信小程序只支持单文件上传，传多个文件需要反复调用本API。所以跨端的写法就是循环调用本API。

**files参数说明**

files 参数是一个 file 对象的数组，file 对象的结构如下：

| 参数名 | 类型   | 必填 | 说明                                        |
| :----- | :----- | :--- | :------------------------------------------ |
| name   | String | 否   | multipart 提交时，表单的项目名，默认为 file |
| file   | File   | 否   | 要上传的文件对象，仅H5（2.6.15+）支持       |
| uri    | String | 是   | 文件的本地地址                              |

Tip:

- 如果 `name` 不填或填的值相同，可能导致服务端读取文件时只能读取到一个文件。

**success 返回参数说明**

| 参数       | 类型   | 说明                           |
| :--------- | :----- | :----------------------------- |
| data       | String | 开发者服务器返回的数据         |
| statusCode | Number | 开发者服务器返回的 HTTP 状态码 |

**示例**

```js
chooseImage() {
				var that = this;
				// 本地选择图片
				uni.chooseImage({
					count: 1,
					success(res) {
						console.log(res);
						var filePath = res.tempFilePaths[0];
						// 上传资源
						uni.uploadFile({
							url: BASE_URL + '/lejuClient/login/uploadAvatar',
							name: 'file',
							filePath,
							timeout: 3000,
							success(res) {
								console.log(res);
								that.imagePath = JSON.parse(res.data).data.fileUrl;
							},
							fail(error) {
								console.log(error);
							}
						})
					},
					fail(error) {
						console.log(error);
					}

				})
			}
```

## 7. uniapp引入 uView

**view 官网**

https://www.uviewui.com/



### 下载安装方式配置

### 关于SCSS

uView依赖SCSS，您必须要安装此插件，否则无法正常运行。

- 如果您的项目是由`HBuilder X`创建的，相信已经安装scss插件，如果没有，请在HX菜单的 工具->插件安装中找到"scss/sass编译"插件进行安装， 如不生效，重启HX即可

### 准备工作

在进行配置之前，请确保您已经根据[安装](https://www.uviewui.com/components/install.html)中的步骤对uView进行了下载安装，如果没有，请先下载安装。

### 配置步骤

#### 1. 引入uView主JS库

在项目根目录中的`main.js`中，引入并使用uView的JS库，注意这两行要放在`import Vue`之后。

```js
// main.js
import uView from '@/uni_modules/uview-ui'
Vue.use(uView)
```

#### 2. 在引入uView的全局SCSS主题文件

在项目根目录的`uni.scss`中引入此文件。

```css
/* uni.scss */
@import '@/uni_modules/uview-ui/theme.scss';
```

#### 3. 引入uView基础样式

注意！

在`App.vue`中**首行**的位置引入，注意给style标签加入lang="scss"属性

```css
<style lang="scss">
	/* 注意要写在第一行，同时给style标签加入lang="scss"属性 */
	@import "@/uni_modules/uview-ui/index.scss";
</style>
```



## 8.uni-icons 图标

https://uniapp.dcloud.net.cn/component/uniui/uni-icons.html#%E4%BB%8B%E7%BB%8D



**下载地址**

https://ext.dcloud.net.cn/plugin?name=uni-icons

### 基本用法

```html
<uni-icons type="contact" size="30"></uni-icons>
```

### API

**Icons Props**

|    属性名    |  类型  | 默认值 |        说明        |
| :----------: | :----: | :----: | :----------------: |
|     size     | Number |   24   |      图标大小      |
|     type     | String |   -    | 图标图案，参考示例 |
|    color     | String |   -    |      图标颜色      |
| customPrefix | String |   -    |     自定义图标     |

**Icons Events**

| 事件名 |        说明        | 返回值 |
| :----: | :----------------: | :----: |
| @click | 点击 Icon 触发事件 |   -    |

## 9. 三级地址联动

https://ext.dcloud.net.cn/plugin?id=1084

```html
<simple-address
                ref="simpleAddress" 
                :pickerValueDefault="cityPickerValueDefault" 
                @onConfirm="onConfirm"
				themeColor="#007AFF">
</simple-address>
<button @tap="selectedAddress">打开地址</button>
```

```js
import simpleAddress from '@/components/simple-address/simple-address.vue';
export default {
    data() {
        return {
            cityPickerValueDefault: [0, 0, 1],
        };
    },
    components: {
        simpleAddress
    },
    methods: {
        openAddres() {
            this.cityPickerValueDefault = [0,0,1]
            this.$refs.simpleAddress.open();
        },
        onConfirm(e) {
            this.pickerText = JSON.stringify(e);
        }
    }
};
```

## 10. checkbox复选框组件

https://ext.dcloud.net.cn/plugin?id=1236

支持easycom模式 可直接使用

```vue
<template>
    <evan-checkbox v-model="checked">复选框</evan-checkbox>
    <evan-checkbox-group v-model="color">
        <evan-checkbox v-for="item in colorList" :key="item.value" :label="item.value">{{item.label}}</evan-checkbox>
    </evan-checkbox-group>
</template>
<script>
export default{
    data(){
        return{
            checked:true,
            color: null,
            colorList: [{
                    label: '红色',
                    value: 'red'
                },
                {
                    label: '绿色',
                    value: 'green'
                },
                {
                    label: '蓝色',
                    value: 'blue'
                },
                {
                    label: '粉色',
                    value: 'pink'
                },
                {
                    label: '黑色',
                    value: 'black'
                }
            ]
        }
    }
}
</script>
```


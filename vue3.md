## 1. Composition API

### 1. setup

- 新的option, 所有的组合API函数都在此使用, 只在初始化时执行一次
- 函数如果返回对象, 对象中的属性或方法, 模板中可以直接使用



- setup执行的时机
  - 在beforeCreate之前执行(一次), 此时组件对象还没有创建
  - this是undefined, 不能通过this来访问data/computed/methods / props
  - 其实所有的composition API相关回调函数中也都不可以
- setup的返回值
  - 一般都返回一个对象: 为模板提供数据, 也就是模板中可以直接使用此对象中的所有属性/方法
  - 返回对象中的属性会与data函数返回对象的属性合并成为组件对象的属性
  - 返回对象中的方法会与methods中的方法合并成功组件对象的方法
  - 如果有重名, setup优先
  - 注意:
  - 一般不要混合使用: methods中可以访问setup提供的属性和方法, 但在setup方法中不能访问data和methods
  - setup不能是一个async函数: 因为返回值不再是return的对象, 而是promise, 模板看不到return对象中的属性数据
- setup的参数
  - setup(props, context) / setup(props, {attrs, slots, emit})
  - props: 包含props配置声明且传入了的所有属性的对象
  - attrs: 包含没有在props配置中声明的属性的对象, 相当于 this.$attrs
  - slots: 包含所有传入的插槽内容的对象, 相当于 this.$slots
  - emit: 用来分发自定义事件的函数, 相当于 this.$emit

```vue
// App
<template>
  <div>App</div>
  <div>{{ msg }}</div>
  <br />
  <Child :msg="msg" msg2="msg2" @test="test" />
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import Child from './components/Child.vue'

export default defineComponent({
  name: 'App',
  components: {
    Child
  },
  setup() {
    const msg = ref('hello')
    function test(txt: string) {
      msg.value = msg.value += txt
    }
    return { msg, test }
  }
})
</script>
<style></style>

```



### 2. ref

- 作用: 定义一个数据的响应式
- 语法: const xxx = ref(initValue):
  - 创建一个包含响应式数据的引用(reference)对象
  - js中操作数据: xxx.value
  - 模板中操作数据: 不需要.value
- 一般用来定义一个基本类型的响应式数据

```vue
<template>
  <div>{{ count }}</div>
  <button @click="updateCount">+</button>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'

export default defineComponent({
  name: 'App',
  setup() {
    // 变量
    // ref 响应式数据 一般用来定义一个基本类型的响应式数据
    let count = ref(0)
    function updateCount() {
      count.value++
    }
    return {
      count,
      updateCount,
    }
  },
})
</script>
```



### 3. reactive

- 作用: 定义多个数据的响应式
- const proxy = reactive(obj): 接收一个普通对象然后返回该普通对象的响应式代理器对象
- 响应式转换是“深层的”：会影响对象内部所有嵌套的属性
- 内部基于 ES6 的 Proxy 实现，通过代理对象操作源对象内部数据都是响应式的

```vue
<template>
  <div>{{ user.name }}</div>
  <div>{{ user.happiness }}</div>
  <button @click="update">update</button>
</template>

<script lang="ts">
import { defineComponent, reactive } from 'vue'

export default defineComponent({
  name: 'App',
  setup() {
    const obj = {
      name: 'jk',
      happiness: {
        name: ['play']
      }
    }
    const user = reactive(obj)
    function update() {
      user.name = 'jk1'
    }
    return { user, update }
  }
})
</script>

```



**ref 和 reactive **

- 是Vue3的 composition API中2个最重要的响应式API
- ref用来处理基本类型数据, reactive用来处理对象(递归深度响应式)
- 如果用ref对象/数组, 内部会自动将对象/数组转换为reactive的代理对象
- ref内部: 通过给value属性添加getter/setter来实现对数据的劫持
- reactive内部: 通过使用Proxy来实现对对象内部所有数据的劫持, 并通过Reflect操作对象内部数据
- ref的数据操作: 在js中要.value, 在模板中不需要(内部解析模板时会自动添加.value)

```vue
<template>
  <h2>App</h2>
  <p>m1: {{m1}}</p>
  <p>m2: {{m2}}</p>
  <p>m3: {{m3}}</p>
  <button @click="update">更新</button>
</template>

<script lang="ts">
import {
  reactive,
  ref
} from 'vue'

export default {

  setup () {
    const m1 = ref('abc')
    const m2 = reactive({x: 1, y: {z: 'abc'}})

    // 使用ref处理对象  ==> 对象会被自动reactive为proxy对象
    const m3 = ref({a1: 2, a2: {a3: 'abc'}})
    console.log(m1, m2, m3)
    console.log(m3.value.a2) // 也是一个proxy对象

    function update() {
      m1.value += '--'
      m2.x += 1
      m2.y.z += '++'

      m3.value = {a1: 3, a2: {a3: 'abc---'}}
      m3.value.a2.a3 += '==' // reactive对对象进行了深度数据劫持
      console.log(m3.value.a2)
    }

    return {
      m1,
      m2,
      m3,
      update
    }
  }
}
</script>
```



### 4. vue3数据响应式原理

- 核心:
  - 通过Proxy(代理): 拦截对data任意属性的任意(13种)操作, 包括属性值的读写, 属性的添加, 属性的删除等...
  - 通过 Reflect(反射): 动态对被代理对象的相应属性进行特定的操作
  - 文档:
    - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy
    - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect

```vue
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <script>
    // 目标对象
    const user = {
      name:'jk',
      age:20
    }
    // Proxy
    // 参数 1: target 目标对象
    // 参数 2: handler 处理器对象，用来监视和操作数据
    const userProxy = new Proxy (user,{
      // 获取
      get(target,prop){
        console.log('get方法执行');
        return Reflect.get(target,prop)
      },
      // 修改，添加
      set(target,prop,value){
        console.log('set方法执行');
        return Reflect.set(target,prop,value)
      },
      // 删除
      deleteProperty(target,prop){
        console.log('delete方法执行');
        return Reflect.deleteProperty(target,prop)
      }
    })

    // 通过代理对象获取目标对象中的某个属性值
    console.log(userProxy.name);

    userProxy.name = 'jk1'
    console.log(userProxy);

    delete userProxy.name
    console.log(userProxy);
  </script>
</body>
</html>
```

### 5. 计算属性与监视

- computed函数:
  - 与computed配置功能一致
  - 只有getter
  - 有getter和setter
- watch函数
  - 与watch配置功能一致
  - 监视指定的一个或多个响应式数据, 一旦数据变化, 就自动执行监视回调
  - 默认初始时不执行回调, 但可以通过配置immediate为true, 来指定初始时立即执行第一次
  - 通过配置deep为true, 来指定深度监视
- watchEffect函数
  - 不用直接指定要监视的数据, 回调函数中使用的哪些响应式数据就监视哪些响应式数据
  - 默认初始时就会执行第一次, 从而可以收集需要监视的数据
  - 监视数据发生变化时回调

```vue
<template>
  <div>
    <div>ref和reactive的细节问题</div>
    姓氏：
    <input type="text" v-model="user.firstName" />
    名字：
    <input type="text" v-model="user.lastName" />
    <br />
    姓名：
    <input type="text" v-model="fullName" />
    姓名1：
    <input type="text" v-model="fullName1" />
    姓名2：
    <input type="text" v-model="fullName2" />
  </div>
</template>

<script lang="ts">
import {
  defineComponent,
  computed,
  reactive,
  watch,
  ref,
  watchEffect
} from 'vue'

export default defineComponent({
  name: 'App',
  setup() {
    const user = reactive({
      firstName: 'j',
      lastName: 'k'
    })
    // computed 只执行get
    const fullName = computed(() => {
      return user.firstName + '-' + user.lastName
    })
    // computed 执行get和set
    const fullName1 = computed({
      get(): string {
        return user.firstName + '-' + user.lastName
      },
      set(value: string) {
        const names = value.split('-')
        user.firstName = names[0]
        user.lastName = names[1]
      }
    })
    
    
    // watch
    const fullName2 = ref('')
    // watch(
    //   user,
    //   () => {
    //     fullName2.value = user.firstName + '-' + user.lastName
    //   },
    //   {
    //     immediate: true, // 是否初始化立即执行一次, 默认是false
    //     deep: true // 是否是深度监视, 默认是false
    //   }
    // )

    // watch 监听非响应式数据 ()=>user.firstName
    // 监听多个数据 使用数组
    // oldValue 返回改变之前的数据 newValue 返回改变之后的数据
    watch(
      [() => user.firstName, () => user.lastName],
      (newValue, oldValue) => {
        console.log(newValue)
        console.log(oldValue)
        fullName2.value = user.firstName + '-' + user.lastName
      },
      {
        immediate: true, // 是否初始化立即执行一次, 默认是false
        deep: true // 是否是深度监视, 默认是false
      }
    )

    //watchEffect 监视回调中使用的数据 默认执行一次
    // watchEffect(() => {
    //   fullName2.value = user.firstName + '-' + user.lastName
    // })
    // watchEffect(() => {
    //   const names = fullName2.value.split('-')
    //   user.firstName = names[0]
    //   user.lastName = names[1]
    // })

    return { user, fullName, fullName1, fullName2 }
  }
})
</script>

<style></style>

```

### 6. 生命周期

**与 2.x 版本生命周期相对应的组合式 API**

- `beforeCreate` -> 使用 `setup()`
- `created` -> 使用 `setup()`
- `beforeMount` -> `onBeforeMount`
- `mounted` -> `onMounted`
- `beforeUpdate` -> `onBeforeUpdate`
- `updated` -> `onUpdated`
- `beforeDestroy` -> `onBeforeUnmount`
- `destroyed` -> `onUnmounted`
- `errorCaptured` -> `onErrorCaptured`

```vue
<template>
<div class="about">
  <h2>msg: {{msg}}</h2>
  <hr>
  <button @click="update">更新</button>
</div>
</template>

<script lang="ts">
import {
  ref,
  onMounted,
  onUpdated,
  onUnmounted, 
  onBeforeMount, 
  onBeforeUpdate,
  onBeforeUnmount
} from "vue"

export default {
  beforeCreate () {
    console.log('beforeCreate()')
  },

  created () {
    console.log('created')
  },

  beforeMount () {
    console.log('beforeMount')
  },

  mounted () {
    console.log('mounted')
  },

  beforeUpdate () {
    console.log('beforeUpdate')
  },

  updated () {
    console.log('updated')
  },

  beforeUnmount () {
    console.log('beforeUnmount')
  },

  unmounted () {
     console.log('unmounted')
  },
  

  setup() {
    
    const msg = ref('abc')

    const update = () => {
      msg.value += '--'
    }

    onBeforeMount(() => {
      console.log('--onBeforeMount')
    })

    onMounted(() => {
      console.log('--onMounted')
    })

    onBeforeUpdate(() => {
      console.log('--onBeforeUpdate')
    })

    onUpdated(() => {
      console.log('--onUpdated')
    })

    onBeforeUnmount(() => {
      console.log('--onBeforeUnmount')
    })

    onUnmounted(() => {
      console.log('--onUnmounted')
    })
    
    return {
      msg,
      update
    }
  }
}
</script>
```

```vue
<template>
  <h2>App</h2>
  <button @click="isShow=!isShow">切换</button>
  <hr>
  <Child v-if="isShow"/>
</template>

<script lang="ts">
import Child from './Child.vue'
export default {

  data () {
    return {
      isShow: true
    }
  },

  components: {
    Child
  }
}
</script>	
```



### 7. 自定义 hook 函数

- 使用Vue3的组合API封装的可复用的功能函数
- 自定义hook的作用类似于vue2中的mixin技术
- 自定义Hook的优势: 很清楚复用功能代码的来源, 更清楚易懂

```vue
<template>
  <!-- 需求：点击页面出现x y 坐标 -->
  <div>App</div>
  x:{{ x }} y:{{ y }}
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import useMousePosition from './hooks/useMousePosition'

export default defineComponent({
  name: 'App',
  setup() {
    const { x, y } = useMousePosition()
    return { x, y }
  }
})
</script>

<style></style>

```

```ts
// ./hooks/useMousePosition
import { ref, onMounted, onUnmounted } from 'vue'

export default function () {
  const x = ref<number>()
  const y = ref<number>()

  onMounted(() => {
    window.addEventListener('click', clickHandler)
  })

  onUnmounted(() => {
    window.removeEventListener('click', clickHandler)
  })

  function clickHandler(e: MouseEvent) {
    x.value = e.pageX
    y.value = e.pageY
  }

  return { x, y }
}
```


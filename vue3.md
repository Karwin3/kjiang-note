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

利用ref函数获取组件中的标签元素

```vue
<template>
  <div>
    <input type="text" ref="inputRef">
  </div>
</template>

<script lang="ts" setup>
// 1. 页面加载完成后，文本框自动获取焦点
import { ref, onMounted } from "vue"
const inputRef = ref<HTMLElement | null>(null)
onMounted(() => {
  inputRef.value && inputRef.value.focus()
})
</script>

<style lang="less" scoped>

</style>
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

### 8.  toRef与toRefs

#### 1. toRef

- toRef  原始对象为非响应式，数据更新，视图不更新 
- toRef  原始对象为响应式，   数据更新，视图更新 

```vue
// App.vue
<template>
  <div>
    <p>name:{{ name }}</p>
    <p>age:{{ age }}</p>
    <hr>
    <button @click="test">test</button>
    <hr>
    <Child :age="age" />
  </div>
</template>
  
<script lang ="ts" setup>
import { ref, reactive, toRef } from "vue"
import Child from './components/Child.vue'

let m1 = reactive({
  name: 'jk1',
  age: 9
})

const name = toRef(m1, 'name')
const age = ref(m1.age)

function test() {
  // name.value += "="
  // m1.name += "="
  age.value += 1
  // m1.age += 1
}
</script>

<style lang="less" scoped>

</style>
```

```vue
// Child.vue
<template>
  <div>
    Child
    <p>age: {{ props.age }}</p>
    <p>length: {{ length }}</p>
  </div>
</template>

<script lang="ts" setup>
import { ref, Ref, toRef, computed } from "vue"
// name: 'Child'
function getLength(age: Ref) {
  return computed(() => {
    return age.value.toString().length
  })
}
const props = defineProps({
  age: {
    type: Number,
    required: true
  }
})
const length = getLength(toRef(props, 'age'))

</script>

<style lang="less" scoped>

</style>
```



#### 2. toRefs

把一个响应式对象转换成普通对象，该普通对象的每个 property 都是一个 ref

应用: 当从合成函数返回响应式对象时，toRefs 非常有用，这样消费组件就可以在不丢失响应式的情况下对返回的对象进行分解使用

```vue
<template>
  <div>
    name: {{ name }}
  </div>
  <div>
    age: {{ age }}
  </div>
  <hr>
  <div>
    name2: {{ name2 }}
  </div>
  <div>
    age2: {{ age2 }}
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive, toRefs } from 'vue'
function useFeature() {
  const stu2 = reactive({
    name2: 'jk2',
    age2: 12
  })
  return {
    ...toRefs(stu2)
  }
}
export default defineComponent({
  name: 'App',
  setup() {
    const stu = reactive({
      name: 'jk',
      age: 1
    })
    const stu1 = toRefs(stu)
    // 定时器
    setInterval(() => {
      // stu.name += "==="
      stu1.name.value += "==="
      console.log("===");
    }, 1000)
    const {name2, age2} = useFeature()
    return {
      // ...stu // 不是响应式数据 {name: 'jk',age: 1}
      ...stu1,
      name2,
      age2
    }
  },
})
</script>

<style>

</style>
```

### 9. shallowRef 与 shallowReactive

- shallowReactive : 只处理了对象内最外层属性的响应式(也就是浅响应式)
- shallowRef: 只处理了value的响应式, 不进行对象的reactive处理
- 什么时候用浅响应式呢?
  - 一般情况下使用ref和reactive即可
  - 如果有一个对象数据, 结构比较深, 但变化时只是外层属性变化 ===> shallowReactive
  - 如果有一个对象数据, 后面会产生新的对象来替换 ===> shallowRe

```vue
<template>
  <div>
    <p>ref</p>
    {{ m1 }} <br />
    <p>shallowRef</p>
    {{ m2 }} <br />
    <p>reactive</p>
    {{ m3 }} <br />
    <p>shallowReactive</p>
    {{ m4 }} <br />
    <hr>
    <button @click="test">test</button>
  </div>
</template>

<!-- shallowRef 和 shallowReactive -->
<script lang="ts" setup>
import { reactive, ref, shallowRef, shallowReactive } from "vue"
const m1 = ref({
  name: 'jk1',
  hobby: {
    name: 'none1'
  }
})
const m2 = shallowRef({
  name: 'jk2',
  hobby: {
    name: 'none2'
  }
})
const m3 = reactive({
  name: 'jk3',
  hobby: {
    name: 'none3'
  }
})
const m4 = shallowReactive({
  name: 'jk4',
  hobby: {
    name: 'none4'
  }
})
function test() {
  // m1.value.name += "name"
  // m1.value.hobby.name += "hobby"

  // m2.value.name += "name"
  // m2.value.hobby.name += "hobby"

  // m3.name += "name"
  // m3.hobby.name += "hobby"
  
  // m4.name += "name"
  m4.hobby.name += "hobby"
}
</script>

<style lang="less" scoped>

</style>
```

### 10. readonly 与 shallowReadonly

- readonly:
  - 深度只读数据
  - 获取一个对象 (响应式或纯对象) 或 ref 并返回原始代理的只读代理。
  - 只读代理是深层的：访问的任何嵌套 property 也是只读的。
- shallowReadonly
  - 浅只读数据
  - 创建一个代理，使其自身的 property 为只读，但不执行嵌套对象的深度只读转换
- 应用场景:
  - 在某些特定情况下, 我们可能不希望对数据进行更新的操作, 那就可以包装生成一个只读代理对象来读取数据, 而不能修改或删除

```vue
<template>
  <div>
    <p>readonly</p>
    {{ m1 }} <br />
    <p>shallowRef</p>
    {{ m2 }} <br />
    <hr>
    <button @click="test">test</button>
  </div>
</template>

<!-- readonly shallowReadonly -->
<script lang="ts" setup>
import { ref, readonly, shallowReadonly, reactive } from "vue"
const m1 = readonly(ref({
  name: 'jk1',
  hobby: {
    name: 'none1'
  }
}))
const m2 = shallowReadonly(reactive({
  name: 'jk2',
  hobby: {
    name: 'none2'
  }
}))
function test(){
  // m1.name += "name"
  // m2.name += "name"
   m2.hobby.name += "name"
}
</script>

<style lang="less" scoped>

</style>

```



### 11.  toRaw与markRaw 

- toRaw
  - 返回由 `reactive` 或 `readonly` 方法转换成响应式代理的普通对象。
  - 这是一个还原方法，可用于临时读取，访问不会被代理/跟踪，写入时也不会触发界面更新。
- markRaw
  - 标记一个对象（不包括ref和reactive），使其永远不会转换为代理。返回对象本身
  - 应用场景:
    - 有些值不应被设置为响应式的，例如复杂的第三方类实例或 Vue 组件对象。
    - 当渲染具有不可变数据源的大列表时，跳过代理转换可以提高性能。

```vue
<template>
  <div>
    {{ m1 }} <br />
    {{ m2 }} <br />
    {{ state }} <br />
    <hr>
    <button @click="toRawFun">toRaw</button>
    <button @click="markRawFun">markRaw</button>
  </div>
</template>

<!-- toRaw 与 markRaw -->
<script lang="ts" setup>
import { ref, toRaw, reactive, markRaw } from "vue"
let m1 = reactive({
  name: 'jk1',
  hobby: {
    name: 'none1'
  }
})
const m2 = ref({
  name: 'jk2',
  hobby: {
    name: 'none2'
  }
})

function toRawFun() {
  const m3 = toRaw(m1)
  const m4 = toRaw(m2.value)
  m3.name += 'name'
  m4.name += 'name'
  console.log(m1)
  console.log(m2)
}

let obj = { name: 'jk3', age: 18 }
// 不能追踪，监听，作为响应式的数据
obj = markRaw(obj)
let state = reactive(obj)

function markRawFun() {
  state.name = 'zs'
}
</script>

<style lang="less" scoped>

</style>
```

### 12. customRef

- 创建一个自定义的 ref，并对其依赖项跟踪和更新触发进行显式控制

```vue
<template>
  <div>
    <input type="text" v-model="m1">
    <p>{{ m1 }}</p>
  </div>
</template>
<!-- customRef -->
<script lang="ts" setup>
import { ref, customRef } from "vue"
// 防抖函数
function useDebouncedRef<T>(val: T, delay = 200) {
  let timeout: number
  return customRef((track, trigger) => {
    return {
      get() {
        track()
        return val
      },
      set(newValue: T) {
        clearTimeout(timeout)
        timeout = setTimeout(() => {
          val = newValue
          trigger()
        }, delay);
      }
    }
  })
}

// let m1 = ref(1)
let m1 = useDebouncedRef(1, 2000)
</script>
```

### 13. provide 与 inject 

- provide`和`inject`提供依赖注入，功能类似 2.x 的`provide/inject
- 实现跨层级组件(祖孙)间通信

```vue
// App.vue 
<template>
  <div>
    <p>{{ color }}</p>
    <button @click="color = 'red'">red</button>
    <button @click="color = 'yellow'">yellow</button>
    <button @click="color = 'green'">green</button>
    <hr>
    <Child />
  </div>
</template>
<!-- provide inject -->
<script lang="ts" setup>
import { ref, provide } from "vue"
import Child from "./components/Child.vue";

const color = ref('red')

provide('color', color)
</script>

<style lang="less" scoped>

</style>
```



```vue
// Child.vue 
<template>
  <div>
    <p>Child</p>
    <GrandSon />
  </div>
</template>

<script lang="ts" setup>
import GrandSon from './GrandSon.vue'
import { ref } from "vue"

</script>

<style lang="less" scoped>

</style>
```



```vue
// GrandSon.vue
<template>
  <div>
    <p :style="{ color }">GrandSon</p>
  </div>
</template>

<script lang="ts" setup>
import { ref, inject } from "vue"

const color: string | undefined = inject('color')

</script>

<style lang="less" scoped>

</style>
```

### 14. 响应式数据的判断

```vue
<template>
  <div>
  </div>
</template>
<!-- 响应式数据的判断 -->
<!-- 
  isRef: 检查一个值是否为一个 ref 对象
  isReactive: 检查一个对象是否是由 reactive 创建的响应式代理
  isReadonly: 检查一个对象是否是由 readonly 创建的只读代理
  isProxy: 检查一个对象是否是由 reactive 或者 readonly 方法创建的代理 
 -->
<script lang="ts" setup>
import { ref, isRef, isReactive, reactive, readonly, isReadonly, isProxy } from "vue"
console.log(isRef(ref({})));
console.log(isReactive(reactive({})));
console.log(isReadonly(readonly({})));
console.log(isProxy(reactive({})));
console.log(isProxy(readonly({})));
</script>

```

### 15. teleport

- Teleport 提供了一种干净的方法, 让组件的html在父组件界面外的特定标签(很可能是body)下插入显示

```vue
// App
<template>
  <div>
    <p>App</p>
    <ModalButton />
  </div>
</template>
<script lang="ts" setup>
// import { ref } from "vue"
import ModalButton from './ModalButton.vue'
</script>
<style lang="sass" scoped>

</style>
```

```vue
// ModalButton.vue
<template>
  <div>
    <button @click="modalOpen = true">
      Open full screen modal! (With teleport!)
    </button>

    <teleport to="body">
      <div v-if="modalOpen" class="modal">
        <div>
          I'm a teleported modal!
          (My parent is "body")
          <button @click="modalOpen = false">
            Close
          </button>
        </div>
      </div>
    </teleport>
  </div>
</template>
<script lang="ts" setup>
import { ref } from "vue"
const modalOpen = ref(false)
</script>
<style scoped>
.modal {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-color: rgba(0, 0, 0, .5);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.modal div {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: white;
  width: 300px;
  height: 300px;
  padding: 5px;
}
</style>
```



### 16. suspense

- 它们允许我们的应用程序在等待异步组件时渲染一些后备内容，可以让我们创建一个平滑的用户体验

```vue
// App.vue
<template>
  <div>
    <Suspense>
      <template v-slot:default>
        <AsyncAddress />
      </template>
      <template v-slot:fallback>
        <h1>LOADING...</h1>
      </template>
    </Suspense>
  </div>
</template>
<script lang="ts" setup>
import { ref } from "vue"
import AsyncAddress from './components/AsyncAddress.vue'

</script>
<style scoped>

</style>
```

```vue
// AsyncAddress.vue
<template>
  <h2>{{ data }}</h2>
</template>
  
<script lang="ts">
import axios from 'axios'
export default {
  async setup() {
    const result = await axios.get('/data/address.json')
    return {
      data: result.data
    }
  }
}
</script>
```


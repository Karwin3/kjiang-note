## 1. vue prop 传值类型

```js
  props: {
    demoString: {
      type: String,
      default: ''
    },
    demoNumber: {
      type: Number,
      default: 0
    },
    demoBoolean: {
      type: Boolean,
      default: true
    },
    demoArray: {
      type: Array,
      default: () => []
    },
    demoObject: {
      type: Object,
      default: () => ({})
    },
    demoFunction: {
      type: Function,
      default: function () { }
    }
  }
```


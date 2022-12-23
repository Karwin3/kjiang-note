## 1.flex布局

### 1.flex 主轴

 	flex布局分为主轴和侧轴

​    默认主轴是 x 轴，元素是跟着主轴来排列

​    两个轴都分为正反两个方向

```css
 			 /* 主轴为 x 轴  默认 */
             flex-direction: row; 
            /* 主轴为 y 轴 */
             flex-direction: column; 
            /* x 轴翻转 */
            flex-direction: row-reverse;
```



### 2. justify-content 设置主轴子元素排列方式

```css
			 /* 默认 从头部开始，如果主轴是x轴，从左往右
            /* justify-content: start; 
            /* 从尾部开始 */
             justify-content: end; 
            /* 主轴居中对齐 */
             justify-content: center; 
            /* 平分剩余空间 */
             justify-content: space-around; 
            /* 两边贴边，再平分剩余空间 */
             justify-content: space-between;
```



### 3. flex-wrap 设置主轴元素是否换行

```css
            /* 默认不换行 */
             flex-wrap: nowrap; 
            /* 换行 */
             flex-wrap: wrap; 
```



### 4. align-items 设置侧轴上的子元素排列方式（单行）

```css
            /* 默认 从上往下 */
             align-items: flex-start; 
            /* 从下往上 */
             align-items: flex-end; 
            /* 垂直居中 */
            align-items: center;
            /* 拉伸 设置该属性时，子元素不可设高度(or宽度)*/
             align-items: stretch; 
```

### 5. align-content 设置侧轴上子元素排列方式(多行)

```css
            /* 默认 在侧轴头部开始排列 */
             align-content: start; 
            /* 在侧轴的尾部开始排列 */
             align-content: end; 
            /* 在侧轴中间显示 */
             align-content: center; 
            /* 子项在侧轴平分剩余空间 */
             align-content: space-around; 
            /* 子项在侧轴分布在两端，在平分剩余空间 */
             align-content: space-between; 
            /* 设置子项元素高度平分父元素高度 子元素不可设高度(or宽度)*/
            align-content: stretch;
```

### 6. flex-flow 复合属性

```css
			/* flex-direction、flex-wrap 的简写*/
            flex-flow: column wrap;
```

### 7. flex子项 flex份数

```css
flex属性定义子项目分配剩余空间，用flex来表示占多少份数
.item {
	flex:<number>; /* default 0*/
}
```

### 8.  flex子项 align-self和order

```css
 			align-self 控制子项自己在侧轴上的排列方式

            order 属性定义项目的排列顺序
            数值越小排列越靠前，默认为0
```


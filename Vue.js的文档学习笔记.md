# Vue.js的文档学习笔记

## 深入了解组件
### 组件注册
#### 全局注册
```javascript
Vue.component('my-component-name', {
  // ... 选项 ...
})
```

这样的方式是全局注册

**注册之后可以用在任何新创建的 Vue 根实例 (new Vue) 的模板中。**

**在所有子组件中也是如此**

#### 局部注册

1. 通过一个普通的 JavaScript 对象来定义组件
```javascript
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }
```
2. 然后在 components 选项中定义你想要使用的组件：
```javascript
new Vue({
  el: '#app',
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```
> 注意局部注册的组件在其子组件中不可用

#### 在模块系统中局部注册
1. 应该使用一个components文件夹来保存所有的组件
2. 需要在局部注册之前导入每个你想要使用的组件
例如，在一个假设的 ComponentB.js 或 ComponentB.vue 文件中：
```javascript
import ComponentA from './ComponentA'
import ComponentC from './ComponentC'

export default {
  components: {
    ComponentA,
    ComponentC
  },
  // ...
}
```
现在 `ComponentA` 和 `ComponentC` 都可以在 `ComponentB` 的模板中使用了。

#### 基础组件的自动化全局注册
1. 要求webpack中的Vue CLI 3+
2. 要求统一的前缀。
3. 在main.js中使用以下代码

```javascript
import Vue from 'vue'
import upperFirst from 'lodash/upperFirst'
import camelCase from 'lodash/camelCase'

const requireComponent = require.context(
  // 其组件目录的相对路径
  './components',
  // 是否查询其子目录
  false,
  // 匹配基础组件文件名的正则表达式
  /Base[A-Z]\w+\.(vue|js)$/
)

requireComponent.keys().forEach(fileName => {
  // 获取组件配置
  const componentConfig = requireComponent(fileName)

  // 获取组件的 PascalCase 命名
  const componentName = upperFirst(
    camelCase(
      // 获取和目录深度无关的文件名
      fileName
        .split('/')
        .pop()
        .replace(/\.\w+$/, '')
    )
  )

  // 全局注册组件
  Vue.component(
    componentName,
    // 如果这个组件选项是通过 `export default` 导出的，
    // 那么就会优先使用 `.default`，
    // 否则回退到使用模块的根。
    componentConfig.default || componentConfig
  )
})
```

## Prop
props这个是组件对象的一个属性，它可以让组件可以接受并使用外部传过来的数据
```javascript
Vue.component('blog-post', {
  // 在 JavaScript 中是 camelCase 的
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
```
> 这里可以认为是约定使用post-title这个属性接受数据,我们要注意属性的命名规则，最好全部小写 重申一次，如果你使用字符串模板，那么这个限制就不存在了。

```html
<!-- 在 HTML 中是 kebab-case 的 -->
<blog-post post-title="hello!"></blog-post>
```
### Prop的类型
props的使用方式：
```javascript
props: ['title', 'likes', 'isPublished', 'commentIds', 'author']
```
我们还可以指定传进来的值的类型
```
props: {
  title: String,
  likes: Number,
  isPublished: Boolean,
  commentIds: Array,
  author: Object,
  callback: Function,
  contactsPromise: Promise // or any other constructor
}
```
### 传递静态或动态的Prop
静态传递：
```html
<blog-post title="My journey with Vue"></blog-post>
```
动态传递：
```html
<!-- 动态赋予一个变量的值 -->
<blog-post v-bind:title="post.title"></blog-post>

<!-- 动态赋予一个复杂表达式的值 -->
<blog-post
  v-bind:title="post.title + ' by ' + post.author.name"
></blog-post>
```
### 传入一个数字
```html
<!-- 即便 `42` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:likes="42"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:likes="post.likes"></blog-post>
```
### 传入一个布尔值
```html
<!-- 包含该 prop 没有值的情况在内，都意味着 `true`。-->
<blog-post is-published></blog-post>

<!-- 即便 `false` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:is-published="false"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:is-published="post.isPublished"></blog-post>
```
> 传入一个对象，传入一个数组，都应该使用v-bind 来告诉 Vue这是一个 JavaScript 表达式而不是一个字符串

### 传入一个对象的所有的属性
我们可以直接把对象传入，然后就可以使用这个对象正常访问它的所有的属性

### 单向数据流
1. 这个 prop 用来传递一个初始值；这个子组件接下来希望将其作为一个本地的 prop 数据来使用。
```javascript
props: ['initialCounter'],
data: function () {
  return {
    counter: this.initialCounter
  }
}
```
2. 这个 prop 以一种原始的值传入且需要进行转换。在这种情况下，最好使用这个 prop 的值来定义一个计算属性：
```javascript
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```
> 注意在 JavaScript 中对象和数组是通过引用传入的，所以对于一个数组或对象类型的 prop 来说，在子组件中改变这个对象或数组本身将会影响到父组件的状态。

### Prop验证
注意那些 prop 会在一个组件实例创建之前进行验证，所以实例的属性 (如 data、computed 等) 在 default 或 validator 函数中是不可用的。

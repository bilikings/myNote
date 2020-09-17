# VUE

## VUE基础

+ 安装vue-cli然后生成webpace

  ```powershell
  npm install --global vue-cli
  vue init webpack
  
  //启动
  npm start
  ```

  ​	

### 生命周期

<img src="https://cn.vuejs.org/images/lifecycle.png" alt="Vue å®ä¾çå½å¨æ" style="zoom: 50%;" />



### 模板，数据和指令

Vue的核心是将数据显示在页面上，这一功能通过模板实现。

```html
<template></template>

<script></script>

<style></style>
```

### 箭头函数

不要在选项属性或回调上使用**箭头函数**，因为箭头函数并没有 `this`，`this`会作为变量一直向上级词法作用域查找，直至找到为止，经常导致 

### v-if 和v-show

+ v-if指令的值为假，这个元素将不会插入到DOM。（即为动态生成，然而v-show是生成了再隐藏）

+ 两个条件语句和v-if有关：v-else-if和v-else。它们的行为非常符合预期：

  当state值为loading时会显示第一个div, state值为error时会显示第二个，state为其他任意值时会显示第三个div。同一时间只有一个元素会显示。

  ```vue
  <div v-if= "state==='loading'">加载中</div>
  <div v-else-if= "state==='error'">加载出错</div>
  <div v-else= 内容</div>
  ```

+ 使用v-if会有性能开销。每次插入或者移除元素时都必须要生成元素内部的DOM树，这在某些时候是非常大的工作量。

+ v-show除了在初始创建开销时之外没有额外的开销。如果希望频繁地切换某些内容，那么v-show会是最好的选择。

+ 此外，如果元素包含任何图片，那么仅使用CSS隐藏父节点可以使浏览器在图片显示之前就加载它，这意味着一旦v-show变为真值，图片就可以显示出来。如果是v-if指令，图片会直到要显示时才开始加载。

### 模板中的循环 v-for

+ 对数组的遍历：

    ```html
    
    <div id="app">
      <ol>
        <li v-for="dog in dogs">
          {{ dog }}
        </li>
      </ol>
    </div>
    
    <script>
    new Vue({
      el: '#app',
      data: {
        dogs:['A','B','C','D']
      }
    })
    </script>
    ```
    
+ 对象的遍历：

  ```html
  
      <div id ="app">
    <ul>
      <li v-for="value in object">
      {{ value }}
      </li>
    </ul>
  </div>
  
  <script>
  new Vue({
    el: '#app',
    data: {
      object: {
        name: 'A',
        age: '3',
        sex: ' 1'
      }
    }
  })
  </script>
  ```
  
  
  
  ### 属性绑定 v-bind
  
  v-bind指令用于将一个值绑定到一个HTML属性上。
  
  ```html
  <button v-bind:type='BT'>
      Test bind
  </button>
  ```
  
  一种简写方式是可以省略v-bind部分，而用冒号( : )代替。
  
  
  
### 响应式

在数据更新时自动地更新页面上显示的内容

### 双向数据绑定 v-modle

  使用v-model指令，它作用于输入框元素，将输入框的值绑定到data对象的对应属性上，因此输入框不但会接收data上的初始值，而且当输入内容更新时，data上的属性值也会更新。

### 方法

+ 使用Vue的方法，从而可以在模板里调用函数来处理数据。
  
  ```vue
  <script>
      new VUE({
          el:'//',
          data:{
          //
      },
              mathods:{
              function(){
          return true;        
      }
              };
      })
  </script>
  ```
  
  #### this
  
  this指向该方法所属的组件。可以使用this访问对象的属性和其他方法

### 计算属性

+ 计算属性介于data对象的属性和方法两者之间：可以像访问data对象的属性那样访问它，但需要以函数的方式定义它

+ 计算属性会被缓存：如果在模板中多次调用一个方法，方法中的代码在每一次调用时都会执行一遍；但如果计算属性被多次调用，其中的代码只会执行一次，之后的每次调用都会使用被缓存的值。

  ```vue
      new VUE({
          el:'//',
          data:{
          //
      },
              computed:{
              function(){
          return true;        
      }
              };
      })
  ```

  

### data对象、方法、计算属性

+ data对象适合纯粹的数据：如果想将数据放在某处，然后在模板、方法或者计算属性中使用，那么可以把它放在data对象中。后面也许还会更新它。
+ 当你希望为模板添加函数功能时，最好使用方法：给方法传递数据，然后它们会对数据进行处理，最终可能返回不同的数据结果。计算属性适用于执行更加复杂的表达式，这些表达式往往太长或者需要频繁地重复使用，所以不想在模板中直接使用。
+ 计算属性往往和其他计算属性或者data对象一起使用，基本上就像是data对象的一个扩展和增强版本。

### 监听器

监听data对象属性或者计算属性的变化

设置数据然后监听它的变化相比，使用一个带有setter的计算属性会是更好的方式。

监听的属性发生变化时，侦听器会被传入两个参数：

```vue
watch:{
    inputValue(val,oldVal){
        //其中val等于this.inputValue
}
}
```

使用侦听器来监听data对象的属性或计算属性的变化，并且在变化发生时进行一些处理——但是通常情况下应该避免使用侦听器，计算属性往往是不错的选择。

### 过滤器

方便在模板中处理数据，适合对字符串和数字进行简单的显示变化

```html

<template>
  <div class="filters">
    <h1 v-text="filtersTitle"></h1>
    <input v-model="filtersText"/>
    <div>{{filtersText | filtersTextChange}}</div>
  </div>
</template>
<script>
  let vm = {};
  export default {
    data() {
      vm = this;
      return {
        filtersTitle: 'vue过滤器学习，filters',
        arrayList: [
          {
            "code": "1",
            "value": "北京市"
          },
          {
            "code": "2",
            "value": "上海市"
          },
          {
            "code": "3",
            "value": "广州市"
          },
        filtersText: '1',
      }
    },
    filters: {
      filtersTextChange: function (dataStr) {
        let arrayList = vm.arrayList;
        let value = '没有这个城市 ';
        for (let b of arrayList) {
          if (b.code == dataStr) {
            value = b.value;
            break;
          }
        }
        return value;
      }
    }
  }
</script>
```

### 输入和事件

可以用 v-on（缩写 @）来绑定click等事件

事件也有修饰符：

+ @click.prevent=“happen” //当链接被单击时阻止页面的跳转
+ @click.stop = "happen"  //阻止事件继续传播，以避免在父级元素上触发事件
+ @click.once = "happen" //想要只在第一次触发事件的时候触发事件侦听器

### 自定义指令 directive

一共有5个钩子函数，分别是bind、inserted、update、componentUpdated和unbind。

+ bind钩子函数会在指令绑定到元素时被调用。
+ inserted钩子会在绑定的元素被添加到父节点时被调用——但和mounted一样，此时还不能保证元素已经被添加到DOM上。可以使用this.$nextTick来保证这一点。
+ update钩子会在绑定该指令的组件节点被更新时调用，但是该组件的子组件可能此时还未更新。
+ componentUpdated钩子和updated钩子类似，但它会在组件的子组件都更新完成后调用。
+ unbind钩子用于指令的拆除，当指令从元素上解绑时会被调用。

做一个`v-blink`,让内容会1s闪烁一次

```javascript
Vue.directive(‘blink’,{
    bind(el){
        let inVisiable = true;
        setInterval(() => {
            isVisible = ! isVisible;
            el.style.visibility = isVisible?'visible':'hidden';
        },1000);
    }
});
```

#### 钩子函数参数

通过v-my-directive:example.one.two="someExpression"来使用指令时候

传入钩子函数的第二个参数**binding**来访问所有的这些内容,即`binding.XXX`

+ name属性是指令的名称，但不包含v-。在这个例子中，name的值为my-directive。
+ value属性是传入指令的值。在这个例子中，它将会是someExpression表达式的计算值。例如，假设data对象等于{ someExpression: hello world }，那么value的值就是helloworld。
+ oldValue属性是上一次传入指令的值，它只有在update和componentUpdated钩子函数中才可以使用。在例子中，如果改变someExpression的值，update钩子就会被调用，此时value属性为新的值，oldValue属性为旧的值。
+ expression属性是指令表达式的字符串形式，之后会对该表达式求值。在这个例子中，它的值为someExpression。· arg属性是传入指令的参数，在这个例子中也就是example。
+ modifiers属性是一个包含所有传入指令的修饰符的对象。在这个例子中它等于{one: true,two: true }。

```javascript
Vue.directive(‘blink’,{
    bind(el){
        let inVisiable = true;
        setInterval(() => {
            isVisible = ! isVisible;
            el.style.visibility = isVisible?'visible':'hidden';
        },binding.value||1000);
    }
});
```

### CSS过渡

+ transition组件

```html
<transition name='fade'>
<div v-if = "divVis">
    is hide
    </div>
</transition>

<style>
    .fade-enter-active,fade-leave-active{
        transition: opacity .5s;    }
</style>
```

当单击按钮切换显示状态时，元素会在页面上渐现或渐隐，而不像之前那样立即被添加或移除。

+ **{name}-enter**这个类名会在元素被插入DOM时加入，然后在下一帧立刻移除。可以使用它来设置那些需要在元素开始进入过渡时移除的CSS属性。*使用这个类名设置在进入过渡阶段需要过渡的CSS属性*
+ **{name}-enter-active**这个类名会在元素整个动画阶段应用。它和-enter类名同时被添加，然后在动画完成时被移除。这个类适用于设置transition这个CSS属性，以设置过渡的时间长度、过渡的属性和使用的曲线函数。*使用这个类名设置进入过渡的 transition CSS属性。*
+ **{name}-enter-to**这个类名会在-enter类名从元素上移除的同时添加到元素上。它适合用来设置那些在元素开始进入过渡时添加的CSS属性，但我通常发现，在-enter类上设置与之相反的过渡属性会更好用一些。
+ **{name}-leave**在离开过渡中，这个类名相当于进入过渡中的-enter类名。它在离开过渡触发时被添加，然后在下一帧被移除。和-enter-to类名类似，这个类名不太有用：更好的做法是使用-leave-to进行与之相反的过渡动画。
+ **{name}-leave-active**在离开过渡中，这个类名相当于进入过渡中的-enter-active。它应用于离开过渡的整个阶段。*使用这个类名设置离开过渡的 transition CSS属性。*
+ **{name}-leave-to**在离开过渡中，这个类名相当于进入过渡中的-enter-to。它在离开过渡被触发之后下一帧生效（与此同时-leave被删除），在过渡完成之后才被移除。*使用这个类名设置在离开过渡阶段需要过渡的CSS属性。*

### JavaScript动画

```html
<transition
    v- on :before -enter="hanleBeforEnter" //适合设置初始值。
    v- on : enter= "handleEnter"           //使用done回调来标明动画已经完成。
    @ leave="hanleLeave">
    <div v-if='divVisible'>
        ...
    </div>
</transition>
```

**使用JavaScript动画，可以创建比使用CSS过渡复杂得多的动画效果，包括多步动画、或者每次显示不同的过渡效果。但是CSS过渡通常性能更好，所以尽量使用CSS过渡，除非你需要的效果无法用纯CSS过渡实现。**



## Vue.js组件

组件是一段**独立的、代表了页面的**一个部分的代码片段。它拥有自己的数据、JavaScript脚本，以及样式标签。

**components**配置对象，将组件传入

### 定义组件

```html
<div id="app">
    <customButton></customButton>
</div>
<script>
	const CusButton ={
        template:'<button>a button</button>
    };
    new Vue({
        el:'#app',
        components:{
            CusButton
        }
    });
</script>
```

也可以注册全局组件

```javascript
 Vue.component('customButton',{
     tempate:'<button>a button</button>'
 });
```

### 数据、方法、计算属性

组件中的data属性是一个函数。

```javascript
data(){
    return{
        numbers:[1,2,3]
    };
}
```

### 传递数据

使用**props**属性来传递数据，Props是通过**HTML属性**传入组件的，然后，在组件内部就可以通过this.color来获取属性的值了。

#### Prop验证

Prop也是响应式的

```javascript
	Vue.component('price-display',{
        pros:{
            price:Number,
            unit:String
        }
    })
```

如上，如果price不是一个数字，或者unit不是一个字符串，Vue就会抛出一个警告。


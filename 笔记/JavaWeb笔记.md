# 计算属性：

#### 1.定义：

要用的属性不存在，要通过已有属性计算得来。
已有属性必须存在于vm上,脱离vm的数据没有进行数据绑定所以不会引起计算属性的变化)

#### 2.原理：

底层借助了Objcet.defineproperty方法提供的getter,和setter。

#### 3.get函数什么时候执行？

​    (1).初次读取时会执行一次。
​    (2).当依赖的数据发生改变时会被再次调用。

#### 4.优势：

与methods实现相比，内部有缓存机制（复用），效率更高，调试方便。

#### 5.备注：

​    1.计算属性最终会出现在vm上，直接读取使用即可。
​    2.如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生

#### 计算属性的简写:

1.用变量名作为函数名直接返回计算后的值(将函数当做一个getter函数用,效果一致),因为改变所使用的属性值就可以改变计算属性的值，所以大部分情况下是用不到set方法的

# 监视属性watch:

#### 1.当被监视的属性变化时，回调函数自动调用，进行相关操作

#### 2.监视的属性必须存在，才能进行监视！！

#### 3.监视的两种写法：

​    (1).new Vue时传入watch配置
​    (2).通过vm.$watch监视

#### 深度监视：

​    (1).Vue中的watch默认不监测对象内部值的改变（一层）。
​    (2).配置deep:true可以监测对象内部值改变（多层）。

#### 备注：

(1).Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以！
(2).使用watch时根据数据的具体结构，决定是否采用深度监视。

# 条件渲染：

#### 1.v-if

写法：
    (1).v-if="表达式
    (2).v-else-if="表达式
    (3).v-else="表达式"
适用于：切换频率较低的场景。
特点：不展示的DOM元素直接被移除。
注意：v-if可以和：v-else-if、v-else一起使用，
但要求结构不能被“打断”。

#### 2.v-show

写法：v-show="表达式”
适用于：切换频率较高的场景。
特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉

#### 3.备注：

使用v-if的时，元素可能无法获取到，而使用v-show一定可以获取到。

# 列表渲染:

#### v-for指令

1.用于展示列表数据
2.语法：v-for="(item,index)in xxx"key="yyy"
3.可遍历：数组、对象、字符串（用的很少）、指定次数（用的很少）

#### 面试题：

#### react、vue中的key有什么作用？

(key的内部原理)

##### 1.虚拟DOM中key的作用：

​    key是虚拟DOM对象的标识，当状态中的数据发生变化时，Vue会根据【新数据】生成【新的虚拟DOM】
​    随后Vue进行【新虚拟DOM】与【旧虚拟DOM】的差异比较，比较规则如下：

##### 2.对比规则：

(1).旧虚拟DoM中找到了与新虚拟DOM相同的key:
    1.若虚拟DOM中内容没变，直接使用之前的真实DOM
    2.若虚拟DOM中内容变了，则生成新的真实DOM,随后替换掉页面中之前的真实DOM。
(2).旧虚拟DoM中未找到与新虚拟DOM相同的key,创建新的真实DOM,随后渲染到到页面。

##### 3.用index作为key可能会引发的问题：

​    1.若对数据进行：逆序添加、逆序删除等破坏顺序操作,会产生没有必要的真实D0M更新==>界面效果没问题，但效率低。
​    2.如果结构中还包含输入类的DOM,会产生错误DOM更新==>界面有问题。

##### 4.开发中如何选择key?:

​    1.最好使用每条数据的唯一标识作为key,比如id、手机号、身份证号、学号等唯一值。
​    2.如果不存在对数据的逆序添加、逆序则除等破坏顺序操作，仅用于渲染列表用于展示，
​    使用index作为key是没有问题的。



# 列表过滤

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="root">
    <input type="text" placeholder="输入文字过滤列表" v-model="keyWord">
    <ul v-for="(p,index) in newPersons" :key="p.id">
        <li>{{p.name}}-{{p.age}}-{{p.sex}}</li>
    </ul>
</div>
<script type="text/javascript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el: '#root',
        data: {
            name: 'dech53',
            persons: [
                {id: 1, name: "马冬梅", age: 19, sex: "女"},
                {id: 2, name: "周冬雨", age: 20, sex: "女"},
                {id: 3, name: "周杰伦", age: 21, sex: "男"},
                {id: 4, name: "温兆伦", age: 22, sex: "男"}
            ],
            keyWord: ''
        },
        methods: {},
        computed: {//计算属性实现
            newPersons() {
                return this.persons.filter((p) => {
                    return (p.name.indexOf(this.keyWord) !== -1)//filter的返回值是一个数组
                })
            }
        },
        watch: {//监视属性实现
            // keyWord: {
            //     immediate: true,
            //     handler(val) {
            //         this.newPersons = this.persons.filter((p) => {
            //             return (p.name.indexOf(val) !== -1)
            //         })
            //     }
            // }
        }
    })
</script>
</body>
</html>
```

# 列表排序

```html
data: {
            name: 'dech53',
            persons: [
                {id: 1, name: "马冬梅", age: 30, sex: "女"},
                {id: 2, name: "周冬雨", age: 31, sex: "女"},
                {id: 3, name: "周杰伦", age: 18, sex: "男"},
                {id: 4, name: "温兆伦", age: 19, sex: "男"}
            ],
            keyWord: '',
            sortType: 0
        }
computed: {//计算属性实现
    newPersons() {
        const arr = this.persons.filter((p) => {
            return (p.name.indexOf(this.keyWord) !== -1)//filter的返回值是一个数组
        })
        if (this.sortType) {
            arr.sort((a, b) => {
                return this.sortType === 1 ? b.age - a.age : a.age - b.age
            })
        }
        return arr
    }
}
```

计算属性中使用到的数据发生改变都会重新调用计算属性中的方法

# Vue的更新监测

#### 1.不比较内存地址的改变，只监测数据的改变

#### 2.导致栈溢出

```html
let data = {
    name: 'dech53'
}
Object.defineProperty(data, 'name', {
    get(){
        return data.name
    },
    set(value){
        data.name = value
        console.log('name的值被修改')
    }
})
```

#### 3.data[key]与data.key的区别

前者是根据key的值来动态访问属性，而后者的'.'后跟着的字符串是不被当做变量来读取的

# Vue.set()的用法

#### 1.使用set方法为data中的对象添加响应式的属性值

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="root">
    <h2>{{name}}</h2>
    <h2>{{address}}</h2>
    <br>
    学生信息
    <h2>
        {{student.name}}-{{student.age.rage + '-' + student.age.sage}}
        <br>
        性别:{{student.sex}}
    </h2>
    <hr>
    <input type="text" placeholder="名字" v-model="input.name">
    <input type="text" placeholder="年龄" v-model="input.age">
    <button @click="add">添加</button>
    <br>
    朋友
    <ul >
        <li v-for="(p,index) in student.friends" :key="p.name">{{p.name}}-{{p.age}}</li>
    </ul>
</div>
<script type="text/javascript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el: '#root',
        data: {
            name: 'dech53',
            address: '6',
            student: {
                name: 'D',
                age: {
                    rage: 18,
                    sage: 10
                },
                friends: [
                    {name: 'J', age: 17},
                    {name: 'X', age: 12}
                ]

            },
            input:{
                name:'',
                age:''
            }
        },
        methods: {
            add(){
                vm.$set(this.student,[this.input.name],this.input.age.toString())
            }
        },
        computed: {}
    })
</script>
</body>
</html>
```

#### 2.两者功能相同

```html
vm.$set(this.student,'sex','男')
Vue.set(this.student,'sex','男')
```

#### 3.set方法的局限性

1.只能为_data内部的对象添加属性值，无法在data和vm身上添加属性

#### 4.官网描述

### [Vue.set( target, propertyName/index, value )](https://v2.cn.vuejs.org/v2/api/#Vue-set)

- **参数**：

  - `{Object | Array} target`
  - `{string | number} propertyName/index`
  - `{any} value`

- **返回值**：设置的值。

- **用法**：

  向响应式对象中添加一个 property，并确保这个新 property 同样是响应式的，且触发视图更新。它必须用于向响应式对象上添加新 property，因为 Vue 无法探测普通的新增 property (比如 `this.myObject.newProperty = 'hi'`)

  注意对象不能是 Vue 实例，或者 Vue 实例的根数据对象。

#### 5.数组的监测

##### 1.官网描述

### [变更方法](https://v2.cn.vuejs.org/v2/guide/list.html#变更方法)

Vue 将被侦听的数组的变更方法进行了包裹，所以它们也将会触发视图更新。这些被包裹过的方法包括：

此处的包裹并不是重新封装，而是在原有方法的基础上添加新的机制

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

你可以打开控制台，然后对前面例子的 `items` 数组尝试调用变更方法。比如 `example1.items.push({ message: 'Baz' })`。

### [替换数组](https://v2.cn.vuejs.org/v2/guide/list.html#替换数组)

变更方法，顾名思义，会变更调用了这些方法的原始数组。相比之下，也有非变更方法，例如 `filter()`、`concat()` 和 `slice()`。它们不会变更原始数组，而**总是返回一个新数组**。当使用非变更方法时，可以用新数组替换旧数组：

```html
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

你可能认为这将导致 Vue 丢弃现有 DOM 并重新渲染整个列表。幸运的是，事实并非如此。Vue 为了使得 DOM 元素得到最大范围的重用而实现了一些智能的启发式方法，所以用一个含有相同元素的数组去替换原来的数组是非常高效的操作。

### [注意事项](https://v2.cn.vuejs.org/v2/guide/list.html#注意事项)

由于 JavaScript 的限制，Vue **不能检测**数组和对象的变化。[深入响应式原理](https://v2.cn.vuejs.org/v2/guide/reactivity.html#检测变化的注意事项)中有相关的讨论。

# Vue监测更新总结



#### 1.vue会监视data中所有层次的数据。

#### 2.如何监测对象中的数据？

##### 通过setter实现监视，且要在new Vuel时就传入要监测的数据。

​	(1).对象中后追加的属性，Vue默认不做响应式处理
​	(2).如需给后添加的属性做响应式，请使用如下API:
​	Vue.set(target,propertyName/index,value)或vm.$set(target,propertyName/index,value)

#### 3如何监测数组中的数据？

##### 通过包裹数组更新元素的方法实现，本质就是做了两件事：

​	(1).调用原生对应的方法对数组进行更新。
​	(2).重新解析模板，进而更新页面。

#### 4.在Vue修改数组中的某个元素一定要用如下方法：

​	1.使用这些API:push()、pop()、shift()、unshift()、splice()、sort()、reverse()
​	2.Vue.set()vm.$set()

### 特别注意：Vue.set()和vm.$set()不能给vm或vm的根数据对象添加属性！！

## 相关代码

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="root">
    <button @click="student.age++">年龄+1岁</button>
    <br/>
    <button @click="addsex">添加性别属性，默认值：男</button>
    <br>
    <button @click="student.sex = '未知'">修改性别属性</button>
    <br/>
    <button @click="addFriends">在列表首位添加一个朋友</button>
    <br/>
    <button @click="changFriends">修改第一个朋友的名字为：张三</button>
    <br/>
    <button @click="removeSmoke">过滤掉爱好中的抽烟</button>
    <br/>
    <button @click="addHobby">添加一个爱好</button>
    <br/>
    <button @click="changeHobby">修改第一个爱好为：开车</button>
    <br/>
    <h2>
        学生信息:
        {{student.name}}
        <br>
    </h2>
    <h2>
        年龄:
        {{student.age}}
        <br>
    </h2>
    <h2 v-if="student.sex">性别:{{student.sex}}</h2>
    <br>
    <hr>
    <br>
    朋友
    <ul>
        <li v-for="(p,index) in student.friends" :key="p.name">{{p.name}}-{{p.age}}</li>
    </ul>
    <br>
    爱好
    <ul>
        <li v-for="(p,index) in student.hobby" :key="index">{{p}}</li>
    </ul>
</div>
<script type="text/javascript">
    Vue.config.productionTip = false
    const vm = new Vue({
        el: '#root',
        data: {
            name: 'dech53',
            student: {
                name: 'D',
                age: 18,
                hobby: ['c', 'd', 't'],
                friends: [
                    {name: 'J', age: 17},
                    {name: 'X', age: 12}
                ]
            }
        },
        methods: {//一定要加s
            addsex() {
                this.$set(this.student, 'sex', '男')
                Vue.set(this.student, 'sex', '男')
            },
            addFriends() {
                this.student.friends.unshift({name: 'G', age: 20})
            },
            changFriends() {
                this.student.friends[0].name = '张三'
            },
            addHobby() {
                this.student.hobby.push('f')
            },
            changeHobby() {
                // this.student.hobby.splice(0,1,'开车')
                // Vue.set(this.student.hobby,0,'开车')
                this.$set(this.student.hobby, 0, '开车')
            },
            removeSmoke() {
                this.student.hobby = this.student.hobby.filter((h) => {
                   return h !== 't'
                })
            }
        }
    })
</script>
</body>
</html>
```

# 生命周期：

1.又名：生命周期回调函数、生命周期函数、生命周期钩子。
2.是什么：Vue在关键时刻帮我们调用的一些特殊名称的函数。
3.生命周期函数的名字不可更改，但函数的具体内容是程序员根据需求编写的。
4.生命周期函数中的this指向是vm或组件实例对象。

```html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="UTF-8" />
    <script type="text/javascript" src="../js/vue.js"></script>
  </head>
  <body>
    <div id="root">
      <h2 :style="{opacity}">dech53</h2>
    </div>
    <script type="text/javascript">
      Vue.config.productionTip = false;
      const vm = new Vue({
        el: "#root",
        data: {
          name: "dech53",
          opacity: 1,
        },
        methods: {},
        computed: {},
        mounted() {
            console.log("定时器调用");
          setInterval(() => {
            this.opacity -= 0.01;
            if (this.opacity <= 0) {
              this.opacity = 1;
            }
          }, 16);
        },
      });
    </script>
  </body>
</html>
```

mounted仅在第一遍真实DOM解析完毕后被调用一次，之后不会再被调用



# 文件上传

```html
<body>
    <input type="file">
    <button>上传文件</button>
    <img src="" alt="">
    <script>
        document.querySelector("button").addEventListener('click',function(){
            var inputFile = document.querySelector("input").files[0]
            var imgElement = document.querySelector("img")
            var sliceBlob = inputFile.slice(0,1024*200,inputFile.type)
            var reader = new FileReader()
            reader.onload = function(e){
                imgElement.src = e.target.result
                imgElement.style.display = "block"
            }
            reader.readAsDataURL(sliceBlob)
        })
    </script>
</body>
```

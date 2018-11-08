## 1. 前言

本文档，作为前端开发人员开发vue项目的参考文档，主要是大家公共约定的代码规则，注意点。

## 2 代码规范

### 2.1 [强制] 代码风格统一

解释：

统一代码风格，减少阅读时间

1. vscode 统一安装 eslint、vetur、vue-format 、git history、 gitlens、beautify、beautify css/sass/less/scss 插件；
2. 



### 2.2 [强制] template 中 html标签访问data 、props、computed 等变量是无需使用 this 关键字

解释： 

有无 this 关键字对结果没有影响，这里统一代码风格，不写。

``` html
<!-- bad  -->
<template>
    <div>
        {{this.name}}
    </div>
</template> 

<!-- good -->
<template>
    <div>
        {{name}}
    </div>
</template> 
```

### 2.3 [强制] Javascript 代码中使用ES6箭头函数替代 var that = this写法

解释：

代码不够优雅，专业

``` javascript
// bad
export default {
    data(){
         name: '长腿欧巴'
    },
    methods:{
        showName(){
            let that = this;
            rt.getName('dataurl')
                .then((name) => {
                    that.name = res.name;
                    console.log(that.name);
                })
                .catch(error){

                };
        }
    }
}

// good
export default {
    data(){
         name: '长腿欧巴'
    },
    methods:{
        showName(){
            rt.getName('dataurl')
                .then( (name) => {
                    this.name = res.name;
                    console.log(this.name);
                })
                .catch(error){

                };
        }
    }
} 

```

### 2.4 [建议] 使用 async await 方式替代 then 的异步回调方式

解释：
1. 代码结构更加合理优雅； 
2. 同步的代码书写方式更利于代码的阅读和理解;
3. 不强制推荐在于会存在一定的性能损失， 同时初学者对方法掌握不够会适得其反;

``` javascript
// bad
export default {
    data(){
         name: '长腿欧巴'
    },
    methods:{
        showName(){
            rt.getName('dataurl')
                .then( (name) => {
                    this.name = res.name;
                    console.log(this.name);
                })
                .catch(error){

                };
        }
    }
} 
// better
export default {
    data(){
         name: '长腿欧巴'
    },
    methods:{
        async showName(){
            try{
                let res = await rt.getName('dataurl');
                this.name = res.name;
                console.log(this.name);
            }catch(error){
                
            } 
        }
    }
}
```

### 2.5 [强制] Javascript 代码中方法声明使用ES6简化写法

解释：

代码不够优雅，专业

``` javascript
// bad
export default {
    data(){
         name: '长腿欧巴'
    },
    methods:{
        showName: function(a){
            return a;
        }
    }
}

// good
export default {
    data(){
         name: '长腿欧巴'
    },
    methods:{
        showName(a){
            return a;
        }
    }
} 

```

### 2.6 [强制] Javascript 代码中变量声明使用ES6 let写法

解释：
let声明特点
1. let声明的变量仅在该变量所在的作用域有效
2. 不存在变量提升
3. 不可以在同一作用域内重复声明
4. 暂时性死区

``` javascript
// bad
export default {
    data(){
         name: '长腿欧巴'
    },
    methods:{
        var a = this.name;
        showName: function(a){
            return a;
        }
    }
}

// good
export default {
    data(){
         name: '长腿欧巴'
    },
    methods:{
        let a = this.name;
        showName(a){
            return a;
        }
    }
} 

```

### 2.7 [强制] 项目中使用 vue-xmobile 等依赖库时不得在node_modules中修改其文件内容

解释： 
1. 如果修改 node_modules 内容， git仓库无法记录修改，不利于团队开发
2. 每次 yarn install 后都要再次手动修改内容
3. 不再代码仓库中管理不利于后续的维护

解决方案：
1. 确定为组件库 bug 的，反馈给组件库开发人员
2. 定制化样式修改的， 在自己项目中修改


### 2.8 [强制] v-bind 正确使用方式

```html

<template>
    <!-- bad -->
    <div>
        <rt-input :placeholder="'请输入姓名'" />
        <!-- waring 以下代码 props 做类型检查时会抛出 warning -->
        <rt-input disabled = "true" />    
        <rt-input max-length = "50” /> 

    </div>

    <!-- good --> 
    <div>
        <rt-input placeholder="请输入姓名" />
        <!-- good -->
        <rt-input :disabled="true" /> 
        <rt-input :max-length = "50” /> 
        <!-- better -->
        <rt-input disabled /> 
    </div> 
</template> 
```
**说明**
1. `:` 是 `v-bind` 指令的缩写， 使用该指令的时候，传入的是 变量、表达式;
2. 在传入字符串时无需添加该指令；

### 2.9 [建议] v-if v-bind v-show 等指令不宜使用复杂的表达式

解释：
1. html代码结构臃肿； 
2. 代码问题不易查找，无法调试；

```html
<template>
    <div>
        <div>
            <!-- bad -->
            <div v-if=" testobj !=null && testobj.company === 'rektec' &&  testobj.personNum > 300 ">
                {{ testobj.name }}
            </div>
            <div> no data </div>

            <!-- good -->
            <div v-if="showdata">
                {{ testobj.name }}
            </div>
            <div> no data </div>
        </div> 
    </div
</template> 

```

```javascript
export default{
    data(){
        testobj:{
            id: 1,
            name: 'xiaozuo',
            company: {
                name: 'rektec',
                personNum: 500
            }
        },
        computed:{
            showdata(){
                // 必要的验证，和表达式逻辑;
            }
        }
    },
}

```

**思考**
正确的业务场景多是对字段非空判断， 将表达式放置到 computed 中， 我们能否提供一个全局的帮助函数判空。



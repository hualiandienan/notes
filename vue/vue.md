# VUE NOTES

## 基础知识点

### LIFECYCLE(生命周期钩子)
* vue实体的生命周期示意图，如下:![VUE LIFTCYCLE](./vueLifecycle.png)

### 事件修饰符
将DOM事件的细节使用修饰符处理，方法关注数据逻辑
* .stop 阻止事件继续传播
* .prevent 阻止默认事件处理
* .capture 使用事件捕获方式
* .self 仅当触发元素为监听元素时才调用事件处理
* .once 仅触发一次事件（该修饰符还可用于自定义组件事件）
* .passive 表示监听函数中是否不会调用preventDefault(2.3及以上)

PS：事件修饰符可串联使用

### 按键修饰符
主要是常用按键别名
* .enter
* .tab
* .delete（捕获“删除”和“退格”键）
* .esc
* .space
* .up
* .down
* .left
* .right

自定义按键修饰符：可通过全局config.keyCodes对象定义

    Vue.config.keyCodes.f1 = 112;

PS: 2.5以后可将键盘事件的key属性暴露的任意有效键名转化为kebab-case（短横线命名方式）作为修饰符(可能引起兼容性问题)

    <!-- 事件处理函数仅在$event.key === "PageDown"时调用 -->
    <input @keyup.page-down="onPageDown">

### 系统修饰符
* .ctrl
* .alt
* .shift
* .meta（系统按键，Mac上对应command键，windows上对应Win键）

精确匹配修饰符：.exact修饰符要求由精确的系统修饰符组合触发的事件.示例如下:

    <!-- 即使ALT或者SHIFT一同按下也会触发 -->
    <buttton @click.ctrl="onClick">A</button>
    
    <!-- 有且仅有ctrl被按下的时候才会触发 -->
    <button @click.ctrl.exact="onCtrlClick">A</button>

    <!-- 没有任何系统修饰符被按下的时候才触发 -->
    <button @click.exact="onClick">A</button>

### 鼠标按钮修饰符
* .left
* .right
* .middle

### 表单
表单修饰符如下：
* .lazy v-model默认情况下同input事件触发后同步绑定数据，添加lazy转变为change事件进行同步
* .number 自动减用户的输入值转为数值类型
* .trim自动过滤用户输入的首位空白符

### 组件
#### 通信
在 Vue 中，父子组件的关系可以总结为 prop 向下传递，事件向上传递。父组件通过 prop 给子组件下发数据，子组件通过事件给父组件发送消息。

非父子关系组件中的通信方式：
1. 使用一个空的Vue实例作为事件总线；
2. 使用专门的状态管理模式；

#### 修饰
组件中的修饰符：
* 组件绑定原生事件，使用.native；
* .sync修饰符，被拓展为自动更新傅祖建属性的v-on监听器

默认情况下，v-model等效props的value及input事件。model选项可重新定义v-model的等效属性

    Vue.component("my-checkbox", {
        model: {
            props: "checked",
            event: "change"
        },
        props: {
            checked: Boolean,
            value: String
        }
    })

#### 嵌入
1. 普通嵌入

        <!-- 组件模板 -->
        <div class="container>
            <header>
                <slot name="header"></slot>
            </header>
            <main>
                <slot></slot>
            </main>
            <footer>
                <slot name="footer"></slot>
            </footer>
        </div>

        <!-- 父组件模板 -->
        <app-layout>
            <h1 slot="header">这里可能是一个页面标题</h1>
            <p>主要内容的一个段落。</p>
            <p>另一个主要段落。</p>
            <p slot="footer">这里有一些联系信息</p>
        </app-layout>

        <!-- 渲染后结果 -->
        <div class="container">
            <header>
                <h1>这里可能是一个页面标题</h1>
            </header>
            <main>
                <p>主要内容的一个段落</p>
                <p>另一个主要段落</p>
            </main>
            <footer>
                <p>这里有一些联系信息</p>
            </footer>
        </div>

1. slot-scope将建立一个含有作用域的嵌入

        <!-- 子组件 -->
        <div class="child">
            <slot text="hello from child"></slot>
        </div>

        <!-- 父组件 -->
        <div class="parent">
            <child>
                <tempalte slot-scope="props">
                    <span>hello from parent</span>
                    <span>{{ props.text }}</span>
                </template>
            </child>
        </div>

        <!-- 渲染结果 -->
        <div class="parent">
            <div class="child">
                <span>hello from parent</span>
                <span>hello from child</span>
            </div>
        </div>

#### 组件缓存
* keep-alive包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们（activated和deactivated两个生命周期钩子触发）

#### 子组件引用
使用ref为子组件指定一个引用ID

    <div id="parent">
        <user-profile ref="profile"></user-profile>
    </div>

    var parent = new Vue({ el: "#parent" })
    var child = parent.$ref.profile

#### 组件间循环引用
在其中一个组件中知会将会引用另一组件，可在beforeCreate中注册引用

    <!-- 在tree-folder中知会引用tree-folder-contents -->
    beforeCreate: function() {
        this.$options.components.TreeFolderContents = require('./tree-folder-contents.vue').default;
    }

#### 模板定义
* 内联模板：当子组件有inline-template特性，组件将把它的内容作为模板而不是嵌入（模板的作用域是子组件而不是父组件）
* X-Template：使用text/template类型的script脚本，并设置ID

### 过渡动画
transition组件封装，可给元素和组件添加entering/leaving。过渡适应情形如下：
* 条件渲染（使用v-if）
* 条件渲染（使用v-show）
* 动态组件
* 组件根节点
1. 单元素/组件的过渡
    * 过渡周期类见下图![VUE TRANSITION](./vueTransition.png)
    * duration属性可自定义过渡持续时间(:duration="{ enter: 500, leave: 800 }")
    * v-是前缀，可以使用name属性设置
    * 自定义过渡的类名，可通过以下特性来定义
        * enter-class
        * enter-active-class
        * enter-to-class
        * leave-class
        * leave-active-class
        * leave-to-class
    * JS钩子，transition过渡动画存在如下钩子
        * before-enter
        * enter
        * after-enter
        * enter-cancelled
        * before-leave
        * leave
        * after-leave
        * leave-cancelled
    * 初始化渲染的过渡，appear特性可设置节点在初始渲染的过渡。过渡类设置及钩子类似进入/离开过渡
2. 多元素/组件的过渡

    当切换元素的标签名相同时，可通过key区分，给<transition>组件中的多个元素设置key是一个更好的实践
    * 过渡模式：默认行为是离开/进入同时发生，可通过mode属性改变行为。设置项如下
        * in-out，新元素先进行过渡，完成后当前元素过渡离开
        * out-in，当前元素先进行过渡，完成之后新元素过渡进入
3. 列表过渡

    使用<transition-group>组件解决同时渲染整个列表问题。值得注意的是，<transition-group>会实例化为一个真实元素，默认为<span>（可通过tag属性更改），且内部元素*总是需要*提供唯一的key属性值

    通过data属性可与JS通信

        <li 
            v-for="(item, index) in computedList" 
            :key="item.msg" 
            :data-index="index">
            {{ item.msg }}
        </li>

        // 获取index
        el.dataset.index

### 混入
#### 合并
当组件和混入对象含有同名选项时：
* 数据对象在内部将进行浅合并，冲突时以组件数据优先
* 钩子函数将混合为一个数组，都将被调用。且混入对象的钩子将先于组件钩子被调用

自定义选项的合并策略，使用默认策略（覆盖已有值）。自定义自定义选项的逻辑合并可向Vue.config.optionMergeStrategies添加一个函数
#### 全局混入
全局混入将会影响到所有之后创建的Vue实例。

    Vue.mixin({
        created: function() {
            let myOption = this.$options.myOption
            if (myOption) {
                console.log(myOption)
            }
        }
    })

    new Vue({
        myOption: 'hello!'
    })

### 指令
#### 定义
* 全局注册

        Vue.directive('focus', {
            inserted: function(el) {
                el.focus()
            }
        })

* 局部注册

        directives: {
            focus: {
                inserted: function(el) {
                    el.focus()
                }
            }
        }

#### 钩子
指令对象提供了如下几个钩子：
* bind，指令第一次绑定到元素时调用（once）
* inserted，被绑定元素插入父节点时调用（未必插入文档中）
* update，所在组件的VNode更新时调用，可能发生在其子VNode更新之前。可通过更新前后值比较来避免不必要的更新
* componentUpdated，指令所在组件的VNode及其子Node全部更新后调用
* unbind，指定与元素解绑时调用（once）

钩子函数参数如下：
* el，绑定的元素
* binding，对象，包含以下属性：
    * name-指令名，不包含前缀
    * value-指定绑定的值
    * oldValue-指令绑定的前个值，仅在*update*和*componentUpdated*可用
    * expression-字符串形式的指令表达式
    * arg-传给指令的参数
    * modifiers-一个包含修饰符的对象
* vnode，Vue虚拟DOM
* oldVnode，前个虚拟DOM，仅在update和componentUpdated钩子中可用

### 插件
插件通常为全局功能，一般有以下几种：
1. 添加全局方法或者属性，如vue-custom-element
2. 添加全局资源：指令/过滤器/过渡等，如vue-touch
3. 通过全局mixin方法添加一些组件选项，如vue-router
4. 添加Vue实例方法，然后通过将他们添加到Vue.prototype上实现
5. 创建一个库，提供自己的API，同时提供上述中的一个或多个功能，如vue-router

Vue的插件有一个公开方法install。该方法的第一个参数为Vue构造器。

    MyPlugin.install = function (Vue, options) {
        // 1. 添加全局方法或属性
        Vue.myGlobalMethod = function () {
            // 逻辑...
        }

        // 2. 添加全局资源
        Vue.directive('my-directive', {
            bind (el, binding, vnode, oldVnode) {
            // 逻辑...
            }
            ...
        })

        // 3. 注入组件
        Vue.mixin({
            created: function () {
            // 逻辑...
            }
            ...
        })

        // 4. 添加实例方法
        Vue.prototype.$myMethod = function (methodOptions) {
            // 逻辑...
        }
    }

通过全局方法Vue.use())即可使用插件

### 过滤器
过滤器使用场景：双花括号插值和v-bind表达式。使用“|”管道表示。
* 全局定义
    
        Vue.filter('capitalize', function(val) {
            if (!val) return ''
            val = val.toString()
            return val.charAt(0).toUpperCase() + val.slice(1) 
        })

* 局部定义

        filters: {
            capitalize: function(val) {
                if (!val) return ''
                val = val.toString()
                return val.charAt(0).toUpperCase() + val.slice(1) 
            }
        }

过滤器设置参数，过滤器函数的第一个参数为value，第二个参数开始为设置的过滤器参数

    {{ message | filterA('arg1', arg2) }}
    

## 单元测试

## vuex状态管理
### store
store管理state树，每个应用仅存在一个store实例。state作为“唯一数据源”存在。
1. 组件中获取state
    * 在计算属性中返回状态

            const Counter = {
                template: '<div>{{ count }}</div>',
                computed: {
                    count() {
                        return store.state.count
                    }
                }
            }
    
    * Vuex通过store选项，提供了一种机制将状态从根组件“注入”到每一个子组件中，需调用Vue.use(Vuex)。子组件能通过*this.$store*访问到。

            const app = new Vue({
                el: '#app',
                store,
                components: { Counter },
                template: '<div class="app"><counter></counter></div>'
            })

2. Getter可设置派生状态（关于store的公共方法）

        const store = new Vuex.Store({
            // 初始化
            state: {
                todos: [
                    { id: 1, text: '...', done: true },
                    { id: 2, text: '...', done: false }
                ]
            },
            getters: {
                doneTodos: state => state.todos.filter(todo => todo.done),
                doneTodosCount: (state, getters) => getters.doneTodos.length
            }
        })

        // 外部引用
        store.getters.doneTodosCount

3. Mutation更新store状态

    更新store的状态唯一方法是提交mutation。每个mutation（类似reducer）都有一个字符串的事件类型（type）和一个回调函数（handler）。回调函数接受state作为第一个参数。

        const store = new Vuex.Store({
            state: {
                count: 1
            },
            mutations: {
                increment(state) {
                    state.count++
                }
            }
        })

    mutation处理函数由store.commit触发。commit可传入额外的参数，作为Mutation的payload。

    mutation需注意如下规则：
    1. 尽量在store中初始化所有所需属性
    2. 需要在对象上添加新属性时，使用Vue.set(obj, 'newProp', 123)或者利用拓展运算符返回新对象

4. Action异步提交Mutation
    1. Action函数接受一个与store实例具有相同方法和属性的context
    2. Action可通过store.dispatch方法触发
    3. store.dispatch可处理并返回action处理函数返回的Promise

            // Promise
            actions: {
                actionA({ commit }) {
                    return new Promise((resolve, reject) => {
                        setTimeout(() => {
                            commit('someMutation')
                            resolve()
                        }, 1000)
                    })
                },
                actionB({ dispatch, commit }) {
                    return dispatch('actionA').then(() => {
                        commit('someOtherMutation')
                    })
                }
            }

            // async/await
            actions: {
                async actionA({ commit }) {
                    commit('gotData', await getData())
                },
                async actionB({ dispatch, commit }) {
                    await dispatch('actionA')
                    commit('gotOtherData', await getOtherData())
                }
            }

5. store映射组件
    1. state映射——mapState函数帮助将状态声明为计算属性
    2. getter映射——mapGetters辅助函数将getter映射到局部计算属性
    3. mutation映射——mapMutations辅助函数将组件中的methods映射为store.commit调用（需在根节点注入store）
    4. action映射——mapActions辅助函数将组件中的methods映射为store.dispatch调用（需在根节点注入store）

6. Module切割store
    * 每个module都拥有自己的state、mutation、action、getter、甚至是嵌套的子模块。
    * 模块除了使用modules属性注册，还可通过store.registerModule方法注册模块（PS:动态注册的模块可通过store.unregisterModule(modelName)解注册）

            // 注册模块'myModule'
            store.registerModule('myModule', {

            })
            // 注册嵌套模块'nested/myModule'
            store.registerModule(['nested', 'myModule'], {

            })
            

    * 对于模块的mutation和getter，接收的第一个参数是模块的局部状态对象。且getter的第三个参数为根节点的状态
    * 对于模块的action，局部状态通过context.state曝露出来，根节点状态通过context.rootState获取
    * 默认情况下，模块的action、mutation和getter注册在全局命名空间（重名时响应同一个mutation或action）；设置namespaced: true，模块的所有getter、action、mutation都会自动根据模块注册的路径调整命名
    * 模块action需dispatch全局action或者commit全局mutation，则可设置{ root: true }作为第三参数给dispacth或commit

### 严格模式
在严格模式下，发生了mutation函数引起的状态变更将会抛错。

*注意：*在发布环境中不要开启严格模式，以免性能损失

## VUE ROUTER
### 导航
vue router的导航API与history的API功能相似
1. 跳转到导航的方式（非替换）
    * 声明式：<router-link :to="...">
    * 编程式：router.push(location, onComplete?, onAbort?)

            // 当提供了path时，params会被忽略，规则同样适用于声明式中的to
            router.push({ name: 'user', params: { userId: 123 } })  // -> /user/123
            router.push({ path: `/user/${userId}` }) // -> /user/123

            router.push({ path: 'register', query: { plan: 'private' } }) // -> /register?plan=private
    
    *注意：*当对应组件对应多个路由路径或者路由仅参数发生变化时，组件不会重加载，可以使用beforeRouteUpdate来响应该变化
2. 替换式跳转（替换当前history记录）
    * 声明式：加上replace属性
    * 编程式：router.replace()

### 视图
1. 多视图同时展示，可使用命名视图实现

        <router-view class="view one"><router-view>
        <router-view class="view two" name="a"></router-view>
        <router-view class="view three" name="b"></router-view>

        const router = new VueRouter({
            routes: [
                {
                    path: '/',
                    components: {
                        default: Foo,
                        a: Bar,
                        b: Baz
                    }
                }
            ]
        })

2. 嵌套视图

    {
        path: '/settings',
        component: UserSettings,
        children: [
            {
                path: 'emails',
                component: UserEmailsSubscriptions
            },
            {
                path: 'profile',
                components: {
                    default: UserProfile,
                    helper: UserProfilePreview
                }
            }
        ]
    }
3. 传参
    props设置为true时，route.params将会被设置为组件属性；当props设置为对象时，他会被按原样设置为组件属性；props也可以为一个返回props的函数，这样就可以将静态值与路由的值结合。

    const User = {
        props: ['id'],
        template: '<div>User {{ id }}</div>'
    }

    const router = new VueRouter({
        routes: [
            { path: '/user/:id', component: User, props: true },
            {
                path: '/user/:id',
                components: { default: User, sidebar: Sidebar },
                props: {
                    default: true, sidebar: false
                }
            }
        ]
    })

3. 导航钩子
* router.beforeEach注册前置钩子

        const router = new VueRouter({ ... })
        router.beforeEach((to, from, next) => {
            // next参数
            // 1. next()——进行管道中的下一个钩子
            // 2. next(false)——中断当前的导航，若是URL改变，则回退到from
            // 3. next('/')或next({ path: '/' })跳转到一个不同的地址
            // 4. next(error)若传入Error实例，则导航终端且该错误将传递给router.onError()注册的处理函数
        })

* 全局解析钩子(2.5.0+)
    使用router.beforeResolve注册，与beforeEach类似。解析守卫在所有组件内守卫与异步路由组件被解析之后被调用

* 全局后置钩子
    使用router.afterEach((to, from) => {})注册。后置钩子函数无法改变导航本身
* 单路由守卫
    可以在路由配置上为某个路由定义独享的beforeEnter守卫，钩子函数参数同全局前置守卫
* 组件内的守卫

        const Foo = {
            template: '...',
            beforeRouteEnter(to, from, next) {
                // 在渲染该组件对应的路由被确认前调用
                // 不能获取获取组件实例的this！！！
                // 在该函数执行前，组件实例还未创建
                // 可使用next注册一个回调函数，在导航被确认时调用。该回调函数的第一个参数为组件实例
            },
            beforeRouteUpdate(to, from, next) {
                // 当前路由改变，在该组件被复用时调用
                // 可以访问组件实例this
            },
            beforeRouteLeave(to, from, next) {
                // 离开该组件对应的路由时调用
                // 可以方位组件实例this
            }
        }

### 导航解析流程
（后续绘图）
1. 导航被触发。
2. 在失活的组件里调用离开守卫。
3. 调用全局的 beforeEach 守卫。
4. 在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。
5. 在路由配置里调用 beforeEnter。
6. 解析异步路由组件。
7. 在被激活的组件里调用 beforeRouteEnter。
8. 调用全局的 beforeResolve 守卫 (2.5+)。
9. 导航被确认。
10. 调用全局的 afterEach 钩子。
11. 触发 DOM 更新。
12. 用创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数。

## 原理
### Vue响应式

## 源码分析
创建实例 初始化Vue ----> new Vue() ---->
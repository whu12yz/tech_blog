### wepy框架介绍
> 腾讯内部开发，目前比较成熟的mvvm小程序开发框架

#### 着重介绍两个重要组成部分
- [ ] wepy
- [ ] compiler
***

### wepy
- 脏检查机制
- 组件通信方式
- 支持mixins混合，自定义事件等
***
##### 脏检查机制
> 借鉴了angular的实现, 当调用$apply时执行一个$digest, 更新当前组件内的数据, 不同于angular1.x的全局检查所带来的性能瓶颈,同时优化了setData，定向更新变化数据,按需执行。

##### 组件通信方式
- props传值
> props传值跟vue保持一致( props down, event up)
- 组件事件通信
```javascript
    $on (evtName, fn) {
        if (typeof evtName === 'string') {
            (this.$events[evtName] || (this.$events[evtName] = [])).push(fn);
        } else if (Array.isArray(evtName)) {
            evtName.forEach(k => {
                this.$on(k, fn);
            });
        } else if (typeof evtName === 'object') {
            for (let k in evtName) {
                this.$on(k, evtName[k]);
            }
        }
        return this;
    }
```

> 采用观察者模式实现组件间事件通信，相应的pub可以参考源代码$broast和$emit的实现，执行事件队列中相应的listener

##### 支持mixins混合，自定义事件等
```javascript
        ['data', 'events', 'components'].forEach((item) => {
            Object.getOwnPropertyNames(this[item]).forEach((k) => {
                if (k !== 'init' && !parent[item][k])
                    parent[item][k] = this[item][k];
            });
        });
    }
```
> 和vue的实现基本一致，支持mixin的插入
***

### compiler
> 编译器也是wepy的核心部分，所有的代码最终都会编译成小程序原生的模式, 这部分功能有wepy-cli提供，当然wepy还支持其他编译插件。
***
- template/config/style/script
> 将template中wepy提供的语法编译成小程序原生支持的模式，同理，config中的配置以及script里面methods，components，mixins等wepy形式的代码也会通过编译器转化成原生模式。
- ES2015(async/class)(babel编译)
> 使用babel编译，同时支持npm包的按需引入, 支持使用es6,es7的语法
- 提供less/stylus等编译器
> 多样化选择css预处理语言，可以下载相应插件进行预编译


### 主要缺点
- 不支持过滤器，transition，路由管理等在vue中比较重要的功能
- 不支持ref或者$element，这就意味着不能像在vue和angular中对dom疯狂操作。不过这也是因为小程序原生禁止了对dom的操作。

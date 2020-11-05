# PROJECT EXPERIENCE

## watch

#### 回调函数

handler(newVal,oldVal){}:新值和旧值

注意：handler不要使用箭头函数会改变this的指向

```javascript
data() {
    return {
        apply:{
            apply_start_lm: "",
            apply_end_lm: ""
        }
    }
}
watch: {
    "apply.apply_start_lm": { //监听data中apply下的apply_start_lm
        handler(newVal, oldVal) {
            this.apply.apply_start_lm = newVal
        }
    }
}
```

#### 选项deep

为了发现对象内部值的变化，可以在选项参数中指定deep:true。监听数组的变动时不需要这么做

```javascript
watch: {
    "a": {
        immedidate: true
    }
}
// 立即以 'a' 的当前值触发回调
```

#### 选项immediate

在选项参数指定immediate:true将立即以表达式的当前值触发回调。

#### 注销watch

组件是经常要被销毁的,比如我们跳一个路由,从一个页面跳到另外一个页面,那么原来的页面的 watch 其实就没用了,这时候我们应该注销掉原来页面的 watch 的,不然的话会导致内置溢出。写在组件中的watch随着组件的销毁而销毁，但如果是下面这种形式就需要手动注销

```javascript
const unWatch = app.$watch('text', (newVal, oldVal) => { 
    console.log(`${newVVal} : ${oldVal}`); 
}) 
unWatch(); // 手动注销watch
```

#### 实例:利用computed结合watch完成监听多个值的变化

场景：apply_start_lm是起始路码 apply_end_lm是结束路码 js_km为计算公里数 两个值相减为公里数 需要实时更新

```javascript
computed: {
    list() {
        const { apply_start_lm, apply_end_lm } = this.apply;
        return {
            apply_start_lm,
            apply_end_lm
        }
    }
}
watch: {
    "apply.apply_start_lm": {
      handler(newVal, oldVal) {
        this.apply.apply_start_lm = newVal;
      },
    },
    "apply.apply_end_lm": {
      handler(newVal, oldVal) {
        this.apply.apply_end_lm = newVal;
      },
    },
    // list 用来监听apply_start_lm与apply_end_lm是否发生改变，如果改变js_km也将做出改变
    list: { //这里的list是computed中的list
      handler(newVal) {
        newVal.js_km = newVal.apply_end_lm - newVal.apply_start_lm;
        this.details.map((item) => {
          item.js_km = newVal.js_km;
        });
        // 使用Object.assign与$set无法实时更新，遂采用上面的方法
        // this.o1 = Object.assign(v)
        // this.o1.js_km = this.apply.apply_end_lm - this.apply.apply_start_lm;
        // v = Object.assign(this.o1)
        // this.$set(v, 'js_km', this.apply.apply_end_lm - this.apply.apply_start_lm)
        // })
      },
      deep: true
    },
}
```




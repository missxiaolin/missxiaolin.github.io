---
title: vue (五)
date: 2019-08-25 09:16:46
categories: "前端之巅"
tags: '前端'
---

### vue中页面数据改变组件不重新渲染

### 页面中引用 组件 additional-entrust.vue，当 界面传的entrustGold值 改变时，组件状态不重新渲染

~~~
<div class = "test">
<additional-entrust 
                :entrustFlag="entrustFlag"
                :eachIncrease="auctionData.eachIncrease"
                :activityId="auctionData.activityId"
                :entrustGold="entrustGold"
                :entrustSilver="entrustSilver"
                @entrustFlag="getEntrust"
                :consumeBean = "consumeBean"
                
            />
</div> 
~~~

### 组件

additional-entrust.vue

在组件 additional-entrust  中通过watch 监听改变

~~~
export default {
    name: 'additionalEntrust',
    components: {
        uniNumberBox
    },
    props: {
        entrustFlag: {
            type: Number,
            default: 0
        },
        eachIncrease: {
            //每次竞购所消耗点豆数
            type: Number,
            default: 1
        },
        /* 委托金豆余量 */
        entrustGold: {
            type: Number,
            default: 0
        },
        entrustSilver: {
            type: Number,
            default: 0
        },
        consumeBean:{
            type:Number,
            default:0
        },
        /* 活动id */
        activityId: {
            type: String,
            default: ''
        }
    },
    data() {
        return {
            beanCount: 100,
            consumption: 0, //消耗
            goldRest: 0, //委托剩余金豆
            silverRest: 0, //委托剩余银豆
        };
    },
    methods: { },
    watch: {
        entrustGold() {
            /*某一操作重置数据*/
            this.$nextTick(() => {
                this.goldRest = this.entrustGold;
            });
        },
        entrustSilver() {
            this.$nextTick(() => {
                this.silverRest = this.entrustSilver;
            });
        },
        consumeBean (){
            this.$nextTick(() => {
                this.consumption = this.consumeBean;
            });
        }
    }
};
~~~

层级过深强制跟新

~~~
this.$forceUpdate();
或
this.$nextTick(function () {
	this.doSomethingElse()
})
~~~
          


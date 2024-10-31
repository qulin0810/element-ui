<template>
  <div id="wrapper" ref="wrapper" :style="{ maxHeight: height-40 + 'px' }">
    <slot></slot>
    <p class="more" :class="{ hidden: hiddenMore }">上下滑动显示更多</p>
  </div>
</template>

<script>
import IScroll from "iscroll/build/iscroll-probe";
export default {
  name: "ScrollView",
  props: {
    height: {
      type: Number,
      // default: 335,
    },
  },
  data() {
    return {
      iscroll: null,
      hiddenMore: true,
      isCPO: false,
      tableScrollHeight: 0
    };
  },
  created() {
  //  this.isCPOPath();
  },
  methods: {
    // isCPOPath() {
    //   // 路径判断，返回true表示是CPO路径
    //   if (this.$route.path.includes("gm")) {
    //     this.isCPO = true;
    //   }
    // },
    destory() {
      this.iscroll.destroy();
    },
    iScrollClick() {
      if (/iPhone|iPad|iPod|Macintosh/i.test(navigator.userAgent)) return false;
      if (/Chrome/i.test(navigator.userAgent))
        return /Android/i.test(navigator.userAgent);
      if (/Silk/i.test(navigator.userAgent)) return false;
      if (/Android/i.test(navigator.userAgent)) {
        var s = navigator.userAgent.substr(
          navigator.userAgent.indexOf("Android") + 8,
          3
        );
        return parseFloat(s[0] + s[3]) < 44 ? false : true;
      }
    },
    refresh() {
      this.iscroll.refresh();
      this.isShowMore(this.iscroll);
      this.iscroll.scrollTo(0, 0, 1);
    },
    isShowMore(scroll) {
      debugger
      this.iscroll.refresh();
      if (scroll.maxScrollY != 0) {
        if (scroll.maxScrollY - scroll.y >= -20) {
          this.hiddenMore = true;
        } else {
          this.hiddenMore = false;
        }
      } else {
        this.hiddenMore = true;
      }
    },
    init() {
      let that = this;
      this.iscroll = new IScroll(this.$refs.wrapper, {
        mouseWheel: false,
        scrollbars: false,
        //解决滚动卡顿
        scrollX: false, //x方向不能滚动
        scrollY: true, //y方向可以滚动
        disablePointer: true,
        disableTouch: false,
        disableMouse: true,
        click: that.iScrollClick(), //添加点击事件
        preventDefault: false,
        // taps: true,
      });
      that.refresh();
      this.iscroll.on("scrollEnd", function () {
        that.isShowMore(this);
      });

      //1.创建一个观察者对象
      /*
      MutationObserver构造函数只要监听到了指定内容发生了变化，就会执行传入的回调函数
      mutationList:发送变化的数组
      observer:观察者对象
       */
      // eslint-disable-next-line no-unused-vars
      let observer = new MutationObserver((mutationList, observer) => {
        this.iscroll.refresh(); //重新设置滚动范围
      });

      //2.告诉观察者对象我们需要观察什么内容

      let config = {
        childList: true, //观察目标子节点的变化，添加或删除
        subtree: true, //默认为false ，设置为true可以观察后代节点
        attributeFilter: ["height", "offsetHeight"], //观察特点属性
      };
      //3.告诉观察者对象，我们需要观察谁，需要观察什么内容
      /*第一个参数:告诉观察者对象我们需要观察谁
        第二个参数:告诉观察者对象我们需要观察什么内容
      */
      observer.observe(this.$refs.wrapper, config);
      this.iscroll.scrollTo(0, 0, 1);
    },
  },
};
</script>

<style scoped lang="scss">
#wrapper {
  /* width: 100%;
    height: 100%; */
  position: relative;
  overflow: hidden;
  .more {
    position: absolute;
    right: 0;
    bottom: 0;
    left: 0;
    z-index: 9;
    padding: 4px 0 7px;
    color: #4a5686;
    font-size: 10px;
    text-align: center;
    background: #fff;
    transition: opacity ease 200ms;
    opacity: 1;
    pointer-events: none;
  }
  .hidden {
    opacity: 0;
  }
}
</style>

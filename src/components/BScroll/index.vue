<template>
  <div ref="wrapper" class="list-wrapper">
    <div class="scroll-content">
      <slot></slot>
      <div>
        <PullingWord v-show="!inPullUp&&dataList.length>0" :loadingWord="beforePullUpWord"></PullingWord>
        <Loading v-show="inPullUp" :loadingWord='PullingUpWord'></Loading>
      </div>
    </div>
    <transition name="pullDown">
      <Loading class="pullDown" v-show="inPullDown" :loadingWord='PullingDownWord'></Loading>
    </transition>
  </div>
</template>
<script>
  import BScroll from 'better-scroll'
  import Loading from './loading/index'
  import PullingWord from './pullingWord/index'
  const PullingUpWord = "正在拼命加载中...";
  const beforePullUpWord = "上拉加载更多";
  const finishPullUpWord = "加载完成";
  const PullingDownWord = "加载中...";
  export default {
    props: {
      /**@description 滚动内容的数据
       * @type 可选参数
       * @param {Array} dataList 默认 [] 
       */
      dataList: {
        type: Array,
        default: []
      },
      /**@description 派发scroll事件的时机
       * @type 当前版本不建议传
       * @param {Number} probeType 默认 3 
       *      1 非实时（屏幕滑动超过一定时间后）派发scroll 事件
       *      2 在屏幕滑动的过程中实时的派发 scroll 事件
       *      3 不仅在屏幕滑动的过程中，而且在 momentum 滚动动画运行过程中实时派发 scroll 事件
       */
      probeType: {
        type: Number,
        default: 3
      },
      /**@description 滚动内容是否可点击
       * @type 可选参数
       * @param {Boolean} click 默认 false 
       *      true 可点击
       *      false 不可点击
       */
      click: {
        type: Boolean,
        default: true
      },
      /**@description 是否启动下拉加载
       * @type 可选参数
       * @param {Boolean || Object} pullDownRefresh 默认 false 
       *      true 开启
       *      false 关闭
       *      Object: {
       *          threshold: 80 // 上拉超出顶部80px时，触发pullingDown事件
       *          stop: 50 // 刷新数据过程中，回弹停留在距离顶部50px的位置
       *      }
       */
      pullDownRefresh: {
        type: null,
        default: false
      },
      /**@description 是否启动上拉加载
       * @type 可选参数
       * @param {Boolean || Object} pullUpLoad 默认 false 
       *      true 开启
       *      false 关闭
       *      Object: {
       *          threshold: -50 // 下拉超出底部50px时，触发pullingUp事件
       *      }
       */
      pullUpLoad: {
        type: null,
        default: false
      },
      /**@description 是否显示滚动条
       * @type 可选参数
       * @param {Boolean} scrollbar 默认 true 
       *      true 显示滚动条
       *      false 隐藏滚动条
       */
      scrollbar: {
        type: Boolean,
        default: true
      }
    },
    data() {
      return {
        scroll: null,
        inPullUp: false,
        inPullDown: false,
        beforePullUpWord,
        PullingUpWord,
        PullingDownWord,
      }
    },
    mounted() {
      setTimeout(() => {
        this.initScroll();
        this.scroll.on('pullingUp', () => {
          this.beforePullUp();
          this.$emit("onPullUp", "当前状态：上拉加载");
        });
        this.scroll.on('pullingDown', () => {
          this.beforePullDown();
          this.$emit("onPullDown", "当前状态：下拉加载更多");
        });
      }, 20)
    },
    methods: {
      initScroll() {
        if (!this.$refs.wrapper) {
          return
        }
        this.scroll = new BScroll(this.$refs.wrapper, {
          probeType: this.probeType,
          click: this.click,
          pullDownRefresh: this.pullDownRefresh,
          pullUpLoad: this.pullUpLoad,
          scrollbar: this.scrollbar
        })
      },
      beforePullUp() {
        this.PullingUpWord = PullingUpWord;
        this.inPullUp = true;
      },
      beforePullDown() {
        this.disable();
        this.inPullDown = true;
      },
      finish(type) {
        this["finish" + type]();
        this.enable();
        this["in" + type] = false;
      },
      disable() {
        this.scroll && this.scroll.disable()
      },
      enable() {
        this.scroll && this.scroll.enable()
      },
      refresh() {
        this.scroll && this.scroll.refresh()
      },
      finishPullDown() {
        this.scroll && this.scroll.finishPullDown()
      },
      finishPullUp() {
        this.scroll && this.scroll.finishPullUp()
      },
    },
    watch: {
      dataList() {
        this.$nextTick(() => {
          this.refresh();
        })
      }
    },
    components: {
      Loading,
      PullingWord
    }
  }

</script>
<style  scoped>
  .list-wrapper {
    position: absolute;
    left: 0;
    top: 0;
    bottom: 0;
    right: 0;
    overflow: hidden;
    background: #fff;
  }

  .list-content {
    position: relative;
    z-index: 10;
    background: #fff;
  }

  .list-item {
    height: 60px;
    line-height: 60px;
    font-size: 18px;
    padding-left: 20px;
    border-bottom: 1px solid #e5e5e5;
  }

  .pulldown-wrapper {
    position: absolute;
    width: 100%;
    left: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    transition: all;
  }


  .pullup-wrapper {
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 16px 0;
  }

  .pullDown {
    position: absolute;
    top: 0;
    left: 0;
  }

  .pullDown-enter-active {
    transition: all 0.2s;
  }

  .pullDown-enter,
  .pullDown-leave-active {
    transform: translateY(-100%);
    transition: all 0.2s;
  }

</style>

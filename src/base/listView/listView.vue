<template>
  <scroll class="listview" :data="data" ref="listview">
    <!--   @scroll="scroll" :listen-scroll="listenScroll"
              :probe-type="probeType"
                -->
    <ul>
      <li v-for="group in data" class="list-group" ref="listGroup">
        <!--      <li :key="group.title">-->
        <h2 class="list-group-title">{{ group.title }}</h2>
        <ul>
          <li v-for="item in group.items" class="list-group-item">
            <!--          <li @click="selectItem(item)" :key="item.name">-->
            <img class="avatar" v-lazy="item.avatar" :alt="item.name">
            <!--            <img v-lazy="item.avatar" alt="" src="">-->
            <span class="name">{{ item.name }}</span>
          </li>
        </ul>
      </li>
    </ul>
    <!--    快速定位栏-->
    <div class="list-shortcut"
         @touchstart="onShortcutTouchStart"
         @touchmove.stop.prevent="onShortcutTouchMove">
      <!--         @touchstart.stop.prevent="onShortcutTouchStart">-->
      <!--       @touchend.stop
                       -->
      <ul>
        <li v-for="(item, index) in shortcutList" class="item" :data-index="index">
          <!--         :key="item"
                           :class="{'current':currentIndex===index}"     -->
          {{ item }}
        </li>
      </ul>
      <!--    </div>-->
      <!--    <div class="list-fixed" ref="fixed" v-show="fixedTitle">-->
      <!--      <div class="fixed-title">{{ fixedTitle }}</div>-->
      <!--    </div>-->
      <!--    <div v-show="!data.length" class="loading-container">-->
      <!--      <Loading></Loading>-->
    </div>
  </scroll>
</template>

<script type="text/ecmascript-6">
import Scroll from '../scroll/scroll.vue'
// import Loading from '@/base/loading/loading'
import {getData} from '../../common/js/dom.js'
//
// const TITLE_HEIGHT = 30
const ANCHOR_HEIGHT = 18

export default {
//   name: 'ListView',
  props: {
    data: {
      type: Array,
      default: []
      // () => {
      //         return []
      //       }
    }
  },
  components: {
    Scroll
    //     Loading
  },
  //   data () {
  //     return {
  //       scrollY: -1,
  //       currentIndex: 0,
  //       diff: -1
  //     }
  //   },
  computed: {
    // 快速入口的列表
    shortcutList () {
      return this.data.map((group) => {
        // “热门”中取第一个字就好了
        return group.title.substring(0, 1)
      })
    }
    //     fixedTitle () {
    //       if (this.scrollY > 0) {
    //         return ''
    //       }
    //       return this.data[this.currentIndex] ? this.data[this.currentIndex].title : ''
  },
  //   },
  created () {
    //     this.probeType = 3
    //     this.listenScroll = true
    this.touch = {}
    //     this.listHeight = []
  },
  methods: {
    //     selectItem (item) {
    //       this.$emit('select', item)
    //     },
    onShortcutTouchStart (e) {
      let anchorIndex = getData(e.target, 'index')
      this.$refs.listview.scrollToElement(this.$refs.listGroup[anchorIndex], 0)
      let firstTouch = e.touches[0]
      this.touch.y1 = firstTouch.pageY
      this.touch.anchorIndex = anchorIndex
      //       this._scrollTo(anchorIndex)
    },
    onShortcutTouchMove (e) {
      let firstTouch = e.touches[0]
      this.touch.y2 = firstTouch.pageY
      // 向下取整，就是直接或零？？？
      let delta = (this.touch.y2 - this.touch.y1) / ANCHOR_HEIGHT | 0
      // 因为取到的属性是字符串，所以这里需要进行处理，解析为数字
      let anchorIndex = parseInt(this.touch.anchorIndex) + delta
      this._scrollTo(anchorIndex)
    },
    //     refresh () {
    //       this.$refs.listview.refresh()
    //     },
    //     scroll (pos) {
    //       this.scrollY = pos.y
    //     },
    //     _calculateHeight () {
    //       this.listHeight = []
    //       const list = this.$refs.listGroup
    //       let height = 0
    //       this.listHeight.push(height)
    //       for (let i = 0; i < list.length; i++) {
    //         let item = list[i]
    //         height += item.clientHeight
    //         this.listHeight.push(height)
    // }
    //     },
    _scrollTo (index) {
      //       if (!index && index !== 0) {
      //         return
      //       }
      //       if (index < 0) {
      //         index = 0
      //       } else if (index > this.listHeight.length - 2) {
      //         index = this.listHeight.length - 2
      //       }
      this.$refs.listview.scrollToElement(this.$refs.listGroup[index], 0)
      //       this.scrollY = this.$refs.listview.scroll.y
    }
    //   },
    //   watch: {
    //     data () {
    //       setTimeout(() => {
    //         this._calculateHeight()
    //       }, 20)
    //     },
    //     scrollY (newY) {
    //       const listHeight = this.listHeight
    //       // 当滚动到顶部，newY>0
    //       if (newY > 0) {
    //         this.currentIndex = 0
    //         return
    //       }
    //       // 在中间部分滚动
    //       for (let i = 0; i < listHeight.length - 1; i++) {
    //         let height1 = listHeight[i]
    //         let height2 = listHeight[i + 1]
    //         if (-newY >= height1 && -newY < height2) {
    //           this.currentIndex = i
    //           this.diff = height2 + newY
    //           return
    //         }
    //       }
    //       // 当滚动到底部，且-newY大于最后一个元素的上限
    //       this.currentIndex = listHeight.length - 2
    //     },
    //     diff (newVal) {
    //       let fixedTop = (newVal > 0 && newVal < TITLE_HEIGHT) ? newVal - TITLE_HEIGHT : 0
    //       if (this.fixedTop === fixedTop) {
    //         return
    //       }
    //       this.fixedTop = fixedTop
    //       this.$refs.fixed.style.transform = `translate3d(0,${fixedTop}px,0)`
    //     }
  }
}
</script>
<style lang="stylus" scoped>
@import "~common/stylus/variable"
.listview
  position: relative
  width: 100%
  height: 100%
  overflow: hidden
  background: $color-background

  .list-group
    padding-bottom: 30px

    .list-group-title
      height: 30px
      line-height: 30px
      padding-left: 20px
      font-size: $font-size-small
      color: $color-text-l
      background: $color-highlight-background

    .list-group-item
      display: flex
      align-items: center
      padding: 20px 0 0 30px

      .avatar
        width: 50px
        height: 50px
        border-radius: 50%

      .name
        margin-left: 20px
        color: $color-text-l
        font-size: $font-size-medium

  //绝对定位到右侧的快捷栏

  .list-shortcut
    position: absolute
    z-index: 30
    right: 0
    top: 50%
    transform: translateY(-50%)
    width: 20px
    padding: 20px 0
    border-radius: 10px
    text-align: center
    background: $color-background-d
    font-family: Helvetica

    .item
      padding: 3px
      line-height: 1
      color: $color-text-l
      font-size: $font-size-small

      &.current
        color: $color-theme

  .list-fixed
    position: absolute
    top: -2px
    left: 0
    width: 100%

    .fixed-title
      height: 30px
      line-height: 30px
      padding-left: 20px
      font-size: $font-size-small
      color: $color-text-l
      background: $color-highlight-background

  .loading-container
    position: absolute
    width: 100%
    top: 50%
    transform: translateY(-50%)
</style>

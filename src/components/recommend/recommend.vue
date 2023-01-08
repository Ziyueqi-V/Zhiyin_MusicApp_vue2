<template>
  <div class="recommend" ref="recommend">
    <Scroll ref="scroll" class="recommend-content" :data="discList">
      <div>
        <!--      有数据了再处理-->
        <div v-if="recommends.length" class="slider-wrapper">
          <slider>
            <div v-for="(item,index) in recommends" :key="index">
              <a :href="item.linkUrl">
                <img class="needsclick" @load="loadImage" :src="item.picUrl" alt="">
              </a>
            </div>
          </slider>
        </div>
        <!--          <Slider>-->
        <!--            <div v-for="item in recommends" :key="item.id">-->
        <!--              <a :href="item.linkUrl">-->
        <!--                <img class="needsclick" @load="loadImage" v-lazy="item.picUrl" alt="" src=""/>-->
        <!--              </a>-->
        <!--            </div>-->
        <!--          </Slider>-->
        <!--        </div>-->
        <div class="recommend-list" ref="recommendList">
          <h1 class="list-title">热门歌单推荐</h1>
          <ul>
            <li v-for="item in discList" class="item">
              <!--                      :key="item.dissid" @click="selectItem(item)">-->
              <div class="icon">
                <img width="60" height="60" v-lazy="item.imgurl" alt=""/>
              </div>
              <div class="text">
                <h2 class="name" v-html="item.creator.name"></h2>
                <p class="desc" v-html="item.dissname"></p>
              </div>
            </li>
          </ul>
        </div>
      </div>
      <div class="loading-container" v-show="!discList.length">
        <Loading></Loading>
      </div>
    </scroll>
    <router-view></router-view>
  </div>
</template>

<script type="text/ecmascript-6">
import Slider from '../../base/slider/slider.vue'
import Scroll from '../../base/scroll/scroll.vue'
import Loading from '../../base/loading/loading.vue'
import {getRecommend, getDiscList} from '../../api/recommend.js'
import {ERR_OK} from '../../api/config.js'
// import {playlistMixin} from '@/assets/js/mixin'
// import {mapMutations} from 'vuex'

export default {
//   name: 'Recommend',
//   mixins: [playlistMixin],
  data () {
    return {
      recommends: [],
      discList: []
    }
  },
  components: {
    Slider,
    Scroll,
    Loading
  },
  created () {
    this._getRecommend()
    this._getDiscList()
  },
  methods: {
    // handlePlaylist (playlist) {
    //   const bottom = playlist.length > 0 ? '60px' : ''
    //   this.$refs.recommend.style.bottom = bottom
    //   this.$refs.scroll.refresh()
    // },
    //     selectItem (item) {
    //       this.$router.push({
    //         path: `/recommend/${item.dissid}`
    //       })
    //       this.setDisc(item)
    //     },
    loadImage () {
      // 只要有一张图片，容器就会被撑开，所以做个逻辑判断，避免重复执行
      if (!this.checkloaded) {
        this.checkloaded = true
        this.$refs.scroll.refresh()
      }
    },
    _getRecommend () {
      getRecommend().then((res) => {
        if (res.code === ERR_OK) {
          // console.log(res.data.slider)
          this.recommends = res.data.slider
        }
      })
      //       .catch(err => {
      //         // eslint-disable-next-line no-console
      //         console.log(err)
      //       })
    },
    _getDiscList () {
      getDiscList().then((res) => {
        if (res.code === ERR_OK) {
          this.discList = res.data.list
        }
        //       }).catch(err => {
        //         // eslint-disable-next-line no-console
        //         console.log(err)
      })
    },
    //     ...mapMutations({
    //       setDisc: 'SET_DISC'
    //     })
  }
}
</script>

<style lang="stylus" scoped>
@import "~common/stylus/variable"
.recommend
  position: fixed
  width: 100%
  top: 88px
  bottom: 0

  .recommend-content
    height: 100%
    overflow: hidden

    .slider-wrapper
      position: relative
      width: 100%
      overflow: hidden

    .recommend-list
      .list-title
        height: 65px
        line-height: 65px
        text-align: center
        font-size: $font-size-medium
        color: $color-theme

      .item
        display: flex
        box-sizing: border-box
        align-items: center // 侧轴(垂直)方向居中
        padding: 0 20px 20px 20px

        .icon
          flex: 0 0 60px
          width: 60px
          padding-right: 20px

        .text
          display: flex
          flex-direction: column
          justify-content: center
          flex: 1
          line-height: 20px
          overflow: hidden
          font-size: $font-size-medium

          .name
            margin-bottom: 10px
            color: $color-text

          .desc
            color: $color-text-d
//绝对定位指定loading展示位置
    .loading-container
      position: absolute
      width: 100%
      top: 50%
      transform: translateY(-50%)
</style>

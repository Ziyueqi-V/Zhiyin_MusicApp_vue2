<template>
  <div class="singer" ref="singer">
        <list-view @select="selectSinger" :data="singers" ref="list"></list-view>
    <router-view></router-view>
  </div>
</template>

<script>
import Singer from '../../common/js/singer.js'
import ListView from '../../base/listView/listView.vue'
// import {mapMutations} from 'vuex'
import {getSingerList} from '../../api/singer.js'
import {ERR_OK} from '../../api/config.js'
// import {playlistMixin} from '@/assets/js/mixin'

const HOT_SINGER_LEN = 10
const HOT_NAME = '热门'

export default {
//   name: 'Singer',
//   mixins: [playlistMixin],
  data () {
    return {
      singers: []
    }
  },
  // 注册组件
  components: {
    ListView
  },
  created () {
    this._getSingerList()
  },
  methods: {
//     handlePlaylist (playlist) {
//       const bottom = playlist.length > 0 ? '60px' : ''
//       this.$refs.singer.style.bottom = bottom
//       this.$refs.list.refresh()
//     },
//     ...mapMutations({setSinger: 'SET_SINGER'}),
//     selectSinger (singer) {
//       this.$router.push({
//         path: `singer/${singer.id}`
//       })
//       this.setSinger(singer)
//     },
    _getSingerList () {
      getSingerList()
        .then((res) => {
          if (res.code === ERR_OK) {
            // this.singers = res.data.list
            this.singers = this._normalizeSinger(res.data.list)
          }
        })
//       .catch(err => {
//         // eslint-disable-next-line no-console
//         console.log(err)
//       })
    },
    // 对后端返回的数据进行处理，得到我们所需要的数据
    _normalizeSinger (list) {
      let map = {
        hot: {
          title: HOT_NAME,
          items: []
        }
      }
      list.forEach((item, index) => {
        if (index < HOT_SINGER_LEN) {
          map.hot.items.push(
            new Singer({
              // 歌手名
              name: item.Fsinger_name,
              // 用于获取头像数据
              id: item.Fsinger_mid
            })
          )
        }
        const key = item.Findex
        if (!map[key]) {
          map[key] = {
            title: key,
            items: []
          }
        }
        map[key].items.push(
          new Singer({
            name: item.Fsinger_name,
            id: item.Fsinger_mid
          }))
      })
      // 到这步得到的对象还是无序的，怎么处理得到有序的呢
      // 为了得到有序列表，我们需要处理 map
      // 其余的
      let ret = []
      // 热门的
      let hot = []
      for (let key in map) {
        let val = map[key]
        // 这里用到了正则校验
        if (val.title.match(/[a-zA-Z]/)) {
          ret.push(val)
        } else if (val.title === HOT_NAME) {
          hot.push(val)
        }
      }
      // 对数据进行排序
      ret.sort((a, b) => {
        return a.title.charCodeAt(0) - b.title.charCodeAt(0)
      })
      return hot.concat(ret)
    }
  }
}
</script>

<style lang="stylus" scoped>
//固定位置，为之后的滚动做准备
.singer
  position: fixed
  top: 88px
  bottom: 0
  width: 100%
</style>

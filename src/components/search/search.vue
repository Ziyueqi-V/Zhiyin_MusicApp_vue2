<template>
  <div class="search">
    <div class="search-box-wrapper">
      <SearchBox ref="searchBox" @query="onQueryChange"></SearchBox>
    </div>
    <div class="shortcut-wrapper" v-show="!query" ref="shortcutWrapper">
      <Scroll class="shortcut" :data="shortCut" ref="shortcut">
        <div>
          <div class="hot-key">
            <h1 class="title">热门搜索</h1>
            <ul>
              <li class="item" v-for="(item, index) in hotKey" :key="index" @click="addQuery(item.k)">
                <span>{{ item.k }}</span>
              </li>
            </ul>
          </div>
          <div class="search-history" v-show="searchHistory.length">
            <h1 class="title">
              <span class="text">搜索历史</span>
              <span class="clear" @click="showConfirm">
                                <i class="icon-clear"></i>
                            </span>
            </h1>
            <SearchList :searches="searchHistory" @select="addQuery" @delete="deleteSearchHistory"></SearchList>
          </div>
        </div>
      </Scroll>
    </div>
    <div class="search-result" v-show="query" ref="searchResult">
      <Suggest :query="query" @listScroll="blurInput" @select="saveSearch" ref="suggest"></Suggest>
    </div>
    <Confirm ref="confirm" text="是否清空所有搜索历史" confirmButtonText="清空" @confirm="clearSearchHistory"></Confirm>
    <router-view></router-view>
  </div>
</template>

<script>
import Scroll from '@/base/scroll/scroll'
import SearchList from '@/base/searchList/searchList'
import Confirm from '@/base/confirm/confirm'
import Suggest from '@/components/suggest/suggest'
import SearchBox from '@/base/searchBox/searchBox'
import {getHotSearch} from '@/api/search'
import {ERR_OK} from '@/api/config'
import {playlistMixin, searchMixin} from '@/assets/js/mixin'
import {mapActions} from 'vuex'

export default {
  name: 'Search',
  mixins: [playlistMixin, searchMixin],
  data () {
    return {
      hotKey: []
    }
  },
  components: {
    SearchBox,
    Suggest,
    SearchList,
    Confirm,
    Scroll
  },
  computed: {
    shortCut () {
      return this.hotKey.concat(this.searchHistory)
    }
  },
  created () {
    this._getHotSearch()
  },
  methods: {
    handlePlaylist (playlist) {
      const bottom = playlist.length > 0 ? '60px' : ''
      this.$refs.shortcutWrapper.style.bottom = bottom
      this.$refs.shortcut.refresh()
      this.$refs.searchResult.style.bottom = bottom
      this.$refs.suggest.refresh()
    },
    _getHotSearch () {
      getHotSearch().then(res => {
        if (Object.is(res.code, ERR_OK)) {
          const result = res.data.hotkey
          this.hotKey = result.slice(1, 10)
        }
      })
    },
    showConfirm () {
      this.$refs.confirm.show()
    },
    ...mapActions([
      'clearSearchHistory'
    ])
  },
  watch: {
    query (newQuery) {
      if (!newQuery) {
        setTimeout(() => {
          this.$refs.shortcut.refresh()
        })
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/variable'
@import '~common/stylus/mixin'
.search
  .search-box-wrapper
    margin 20px

  .shortcut-wrapper
    position fixed
    top 178px
    bottom 0
    width 100%

    .shortcut
      height 100%
      overflow hidden

      .hot-key
        margin 0 20px 20px 20px

        .title
          margin-bottom: 20px
          font-size: $font-size-medium
          color: $color-text-l

        .item
          display: inline-block
          padding: 5px 10px
          margin: 0 20px 10px 0
          border-radius: 6px
          background: $color-highlight-background
          font-size: $font-size-medium
          color: $color-text-d

      .search-history
        position relative
        margin 0 20px

        .title
          display flex
          align-items center
          height 40px
          font-size $font-size-medium
          color $color-text-l

          .text
            flex 1

          .clear
            extend-click()

            .icon-clear
              font-size $font-size-medium
              color $color-text-d

  .search-result
    position: fixed
    width: 100%
    top: 178px
    bottom: 0
</style>

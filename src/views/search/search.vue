<template>
  <div>
    <el-main>
      <el-row>
        <div class="search">
          <el-input v-model="keywords">
            <el-button slot="append" icon="el-icon-search" @keyup.enter.native="searchInf(input)"></el-button>
          </el-input>
        </div>
      </el-row>
      <div class="tabs">
        <div class="title">搜索"{{keywords}}", 找到</div>
        <el-tabs type="border-card" style="background-color: black" :stretch="true">
          <el-tab-pane label="单曲">
            <songs :songs="searchResult[0].songs"></songs>
          </el-tab-pane>
          <el-tab-pane label="歌手">
            <div class="flex">
              <template v-for="(item, index) in searchResult[1].artists">
                <singer-outline :key="index" length="150px" height="150px">
                  <template v-slot:img>
                    <img :src="item.img1v1Url" alt="" @click="goSinger(item.id)">
                  </template>
                  <template v-slot:sentence>
                    <div>{{item.name}}</div>
                  </template>
                </singer-outline>
              </template>
            </div>
          </el-tab-pane>
          <el-tab-pane label="专辑">
            <div class="grid-4">
              <template v-for="(item, index) in searchResult[2].albums">
                <album-outline :key="index" length="150px" height="150px">
                  <template v-slot:img>
                    <img :src="item.blurPicUrl" alt="" @click="goAlbum(item.id)">
                  </template>
                  <template v-slot:sentence>
                    <div>{{item.name}}</div>
                  </template>
                </album-outline>
              </template>
            </div>
          </el-tab-pane>
          <el-tab-pane label="视频">
            <div class="grid-4">
              <template v-for="(item, index) in searchResult[3].videos">
                <album-outline :key="index" length="190px" height="120px">
                  <template v-slot:img>
                    <img :src="item.coverUrl" alt="" @click="goMv(item.vid, item.type)">
                  </template>
                  <template v-slot:sentence>
                    <div>{{item.title}}</div>
                  </template>
                </album-outline>
              </template>
            </div>
          </el-tab-pane>
          <el-tab-pane label="歌词">
            <el-collapse v-model="activeNames" @change="handleChange">
              <template v-for="(item , index) in searchResult[4].songs">
                <lyric :key="index" :song="item"></lyric>
              </template>
            </el-collapse>
          </el-tab-pane>
          <el-tab-pane label="歌单">
            <songmenu :songs="searchResult[5].playlists"></songmenu>
          </el-tab-pane>
        </el-tabs>
      </div>
    </el-main>
  </div>
</template>

<script>
import albumOutline from '../../components/songOutline/albumOutline'
import singerOutline from '../../components/songOutline/singerOutline'
import songmenu from '../../components/songTable/songmenu'
import lyric from '../../components/lyric/lyric'
import songs from '../../components/songTable/songs'
export default {
  name: 'search',
  components: {
    songs,
    lyric,
    songmenu,
    singerOutline,
    albumOutline
  },
  created () {
    this.search(1, 0)
    this.search(100, 1)
    this.search(10, 2)
    this.search(1014, 3)
    this.search(1006, 4)
    this.search(1000, 5)
    this.$nextTick(() => {
      this.getActiveNames()
    })
  },
  data () {
    return {
      searchResult: [
        {}, {}, {}, {}, {}, {}
      ],
      activeNames: []
    }
  },
  methods: {
    // 根据type值，来获取搜索信息的type数据
    search (type, i) {
      this.$http.get(`/search?keywords=${this.keywords}&type=${type}`).then(data => {
        this.$set(this.searchResult, i, data.result)
        // this.searchResult.splice(i, 0, data.result)
      })
    },
    // 去往歌手详细信息
    goSinger (id) {
      this.$router.push({ name: 'singerInformation', params: { sid: id } })
    },
    handleChange (val) {
      console.log(val)
    },
    getActiveNames () {
      console.log(this.searchResult[3])
    },
    // 去往专辑页面
    goAlbum (id) {
      this.$router.push({
        path: '/album',
        query: {
          id
        }
      })
    },
    // 去往mv页面
    goMv (id, type) {
      if (type === 1) {
        this.$router.push({
          path: '/videos',
          query: {
            id,
            type
          }
        })
      } else {
        this.$router.push({
          path: '/mv',
          query: {
            id,
            type
          }
        })
      }
    }
  },
  computed: {
    keywords () {
      return this.$route.query.keywords
    }
  },
  watch: {
  }
}
</script>

<style lang="stylus" scoped>
  .el-main
    padding 0
    .search
      width 400px
      margin 10px auto
  .el-input
    :focus
      border-color: red
    .title
      color #909399
      font-size 14px
      margin-bottom 6px
      padding-left 10px
  .el-collapse
    padding 10px
</style>

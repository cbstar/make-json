<template>
  <section>
    <navigation></navigation>
    <div class="container">
      <catalog :catContent="catContent"></catalog>
      <tag :tagAllList="tagAllList"></tag>
    </div>
  </section>
</template>
<script>
  import { getTagList, getCat } from '../static/api.js'
  import Nav from '../components/Navigation.vue'
  import Tag from '../components/NewTag.vue'
  import Catalog from '../components/Catalog.vue'
  import { mapActions, mapState } from 'vuex'
  export default {
    data: function () {
      return {
        catContent: {}
      }
    },
    components: {
      navigation: Nav,
      tag: Tag,
      catalog: Catalog
    },
    methods: {
      ...mapActions(['pipeIsMainPage', 'pipePageNum']),
      getCateList (page, param) {
        let type = param && param[0]
        let itemName = param && param[1]
        if (itemName === '全部') {
          type = itemName = null
        }
        getCat(page, type, itemName)
          .then((data) => {
            this.catContent = data
            let pageTotal = 1
            this.pipePageNum([1, pageTotal])
          }).catch(err => {
            console.log(err)
          })
      }
    },
    computed: {
      ...mapState(['tagParam'])
    },
    watch: {
      tagParam (now) {
        this.getCateList(1, this.tagParam)
      }
    },
    created () {
      this.pipeIsMainPage(true)
      this.catContent = this.tagAllList[2]
      let pageTotal = this.catContent.pageTotal
      this.pipePageNum([1, pageTotal])
    },
    asyncData (context) {
      let tag = getTagList('tag').then((res) => {
        return [{'tag': '全部'}].concat(res)
      }).catch(err => {
        console.log(err)
      })
      let cate = getTagList('category').then((res) => {
        return [{'category': '全部'}].concat(res)
      }).catch(err => {
        console.log(err)
      })
      let cat = getCat(1).then((res) => {
        return res
      }).catch(err => {
        console.log(err)
      })
      return Promise.all([tag, cate, cat]).then((res) => {
        return {tagAllList: res}
      })
    }
  }
</script>
<style lang="less" scoped>
  .container {
    margin: 0 auto;
    max-width: 1450px;
  }
</style>

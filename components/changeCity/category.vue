<!--
 * @Author: zhoulin
 * @Date: 2020-04-25 18:32:45
 * @LastEditors: your name
 * @LastEditTime: 2020-04-30 19:31:19
 * @Description: file content
 -->
<template>
  <div>
    <dl class="m-category">
      <dt>按拼音首字母选择：</dt>
      <dd v-for="item in list" :key="item">
        <a :href="'#city-'+item">{{ item }}</a>
      </dd>
    </dl>
    <dl v-for="item in block" :key="item.title" class="m-category-section">
      <dt :id="'city-' + item.title">{{ item.title }}</dt>
      <dd>
        <span v-for="c in item.city" :key="c" @click="selectCity">{{ c }}</span>
      </dd>
    </dl>
  </div>
</template>

<script>
import pyjs from 'js-pinyin'
export default {
  data () {
    return {
      list: 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split(''),
      block: [{ title: '', city: [] }]
    }
  },
  async mounted () {
    const self = this
    const blocks = []
    const { status, data: { city } } = await self.$axios.get('/geo/city')
    if (status === 200) {
      let p
      let c
      const d = {}
      city.forEach((item) => {
        p = pyjs.getFullChars(item.name).toLocaleLowerCase().slice(0, 1)
        c = p.charCodeAt(0)
        if (c > 96 && c < 123) {
          // A-Z 65-90   a-z 97-122
          if (!d[p]) {
            d[p] = []
          }
          d[p].push(item.name)
        }
      })
      for (const [k, v] of Object.entries(d)) {
        blocks.push({
          title: k.toUpperCase(),
          city: v
        })
      }
      blocks.sort((a, b) => a.title.charCodeAt(0) - b.title.charCodeAt(0))
      self.block = blocks
    }
  },
  methods: {
    selectCity (e) {
      // this.$store.commit('geo/setPosition', { city: e.target.innerHTML, province: '' })
      // // location.href = '/' 会丢失vuex中数据
      // this.$router.push({ path: '/' })
    }
  }
}
</script>

 <style lang="scss">
  @import "@/assets/css/changeCity/category.scss"
 </style>

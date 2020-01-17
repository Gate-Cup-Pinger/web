<template>
  <master>
    <card class = "coc-dark-background-bg coc-background-border coc-margin-y-3perc">
      <p 
        class = "coc-warning-text coc-text-title" 
        style="font-weight: 200">
        This App Was Built With
        <span class = "knocks-heart3 red-text" />
        And
      </p>
      <div class = "row">
        <div
          v-coc-mouse-over = "'coc-text-md-1'"
          v-for = "(tool, t) in tools"
          :key = "t"
          class="col s12 coc-smooth-font-size">
          <div class="col ">
            <coc-avatar
              :source = "tool.src"
              :child-classes = "['coc-white-bg coc-padding-5px']"
              border-radius = "circle" />
          </div>
          <div class="col s10">
            <p class="coc-text-bold coc-warning-text coc-text-md-2">{{ tool.name }}</p>
            <p class = "coc-border-text">{{ tool.desc }}</p>
          </div>
        </div>
      </div>
    </card>
  </master>
</template>

<script>
import Master from '~/components/common/master'
export default {
  name: 'Index',
  components: {
    Master
  },

  data() {
    return {
      tools: [
        {
          name: 'VueJs',
          src: '/snaps/vue.png',
          desc: 'Javascript framework for building progressive web apps'
        },
        {
          name: 'NuxtJS',
          src: '/snaps/nuxt.png',
          desc: 'VueJs framework for Server Side Rendering (SSR)'
        },
        {
          name: 'iView UI',
          src: '/snaps/iview.svg',
          desc: 'UI framework based on VueJs'
        },
        {
          name: 'CocJs',
          src: '/snaps/coc.png',
          desc: 'Javascript assistant based on iView and Coc Engine'
        },
        {
          name: 'WebPack',
          src: '/snaps/webpack.png',
          desc: 'Javascript bundling framework'
        },
        {
          name: 'ApexCharts',
          src: '/snaps/apex.png',
          desc: 'Javascript library for canvas and charts rendering'
        },
        {
          name: 'NodeJS',
          src: '/snaps/nodejs.png',
          desc: 'Chromium platform based on JS and C++ for network apps'
        },
        {
          name: 'MongoDB',
          src: '/snaps/mongodb.png',
          desc: 'NoSQL database that works with json'
        },
        {
          name: 'ExpressJs',
          src: '/snaps/express.png',
          desc: 'Javascript framework for nodejs apps routing'
        },
        {
          name: 'Mongoose',
          src: '/snaps/mongoose.jpg',
          desc: 'ORM library works with nodejs-mongodb environment'
        },
        {
          name: 'ElectronJS',
          src: '/snaps/electron.png',
          desc: 'Javascript framework for building cross platform desktop apps'
        }
      ]
    }
  },
  mounted() {},
  methods: {}
}
</script>

<style lang="css" scoped>
.t-container{
  min-height: 70vh;
  max-height: 70vh;
  overflow-y: scroll;
}
.t-container-tab{
  min-height: 60vh;
  max-height: 60vh;
  overflow-y: scroll;
}
.t-container-sm{
  min-height: 43vh;
  max-height: 43vh;
  overflow-y: scroll;
}
</style>

<template>
  <div>
    <nav
      :class = "bleeders"
      class = "coc-padding-10px" 
      style="background-color: #212b31">
      <p class = "center">
        <icon 
          v-coc-mouse-over = "'pulse infinite glow-warning'"
          v-coc-mouse-leave = "'rotateIn'"
          type = " knocks-shield5 coc-text-super  " 
          class="coc-padding-10px coc-border-2 coc-circle-border-radius coc-warning-text animated coc-dark-background-bg"
          style = "height: 100px; width: 100px; line-height: 85px"
          @click = "$router.push('/')" /><br>
        <span class = "coc-warning-text coc-text-bold">
          <icon type = " knocks-star4" />
          <icon type = " knocks-star2" />
          <icon type = " knocks-star2" />
          <icon type = " knocks-star2" />
          GATE CUP
          <icon type = " knocks-star2" />
          <icon type = " knocks-star2" />
          <icon type = " knocks-star2" />
          <icon type = " knocks-star4" />
        </span>
      </p>
    </nav>
    <div
      class = "container coc-padding-top-1rem main-bod">
      <slot />
    </div>
    <Footer class = "coc-dark-background-bg">
      <div class="container">
        <div class="row">
          <div class="col l4 s12 ">
            <p class="">
              Recruitment Department Of The Egyptian Armed Forces<br>
              Information Systems Department<br>
              JAN 2020
            </p>
          </div>
          <div class="col l4 s12 center">
            <nuxt-link
              to = "https://github.com/mamr-moussa"
              class = "center">
              <span class= "knocks-brand84 white-text" />
              <span class="white-text">mamr-moussa</span>
            </nuxt-link><br>
            <nuxt-link
              to = "https://twitter/mamr_moussa"
              class = "center">
              <span class= "knocks-brand268 white-text" />
              <span class="white-text">mamr_moussa</span>
            </nuxt-link><br>
            <span
              class = "center">
              <span class= "knocksapp-mail" />
              <span class="">muhammad.amro.moussa@gmail.com</span>
            </span>
          </div>
          <div class="col l4 s12 right">
            <nuxt-link
              to = "/contribute"
              class = "right coc-warning-text">Contribute</nuxt-link>
          </div>
        </div>
      </div>
    </Footer>
  </div>
</template>

<script>
export default {
  name: 'Master',
  props: {
    bleeding: {
      type: [String, Boolean],
      default: false
    }
  },
  data() {
    return {}
  },
  computed: {
    bleeders() {
      let result = []
      if (this.bleeding) {
        result.push({})
        result[0][`glow-${this.bleeding}`] = true
      }
      return result
    }
  }
}
</script>

<style lang="css" scoped>
.glow-warning {
   box-shadow: 1px 1px 10px 1px #ff9900;
}
.glow-error {
  box-shadow: 1px 1px 10px 1px red;
}
.glow-success {
  box-shadow: 1px 1px 10px 1px #19be6b;
}

.main-bod{
  min-height: 80vh;
}
</style>

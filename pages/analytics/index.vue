<template>
  <master>
    <h3>Analyics</h3>
    <div class="row">
      <div class="col s12 l4 m6 coc-margin-y-10px">
        <Card class = "anltc-card blue">
          <nuxt-link
            to = "/analytics/jobs"
            class = "coc-text-lg-1 white-text coc-text-bold">
            <span class = "tcsc-maintenance-icon" />
            JOBS
          </nuxt-link>
        </Card>
      </div>
      <div class="col s12 l4 m6 coc-margin-y-10px">
        <Card class = "anltc-card amber darken-3">
          <nuxt-link
            to = "/analytics/jobs"
            class = "coc-text-lg-1 white-text coc-text-bold">
            <span class = "tcsc-stock-1-icon" />
            STOCK
          </nuxt-link>
        </Card>
      </div>
      <div class="col s12 l4 m6 coc-margin-y-10px">
        <Card class = "anltc-card teal darken-1">
          <nuxt-link
            to = "/analytics/jobs"
            class = "coc-text-lg-1 white-text coc-text-bold">
            <span class = "tcsc-volkswagen-beetle-icon" />
            AUTOMOBILES
          </nuxt-link>
        </Card>
      </div>
      <div class="col s12 l4 m6 coc-margin-y-10px">
        <Card class = "anltc-card pink darken-2">
          <nuxt-link
            to = "/analytics/jobs"
            class = "coc-text-lg-1 white-text coc-text-bold">
            <span class = "tcsc-motor-11-icon" />
            PARTS
          </nuxt-link>
        </Card>
      </div>
      <div class="col s12 l4 m6 coc-margin-y-10px">
        <Card class = "anltc-card purple darken-3">
          <nuxt-link
            to = "/analytics/jobs"
            class = "coc-text-lg-1 white-text coc-text-bold">
            <span class = "tcsc-car-6-icon" />
            VENDORS
          </nuxt-link>
        </Card>
      </div>
      <div class="col s12 l4 m6 coc-margin-y-10px">
        <Card class = "anltc-card pink darken-2">
          <nuxt-link
            to = "/analytics/jobs"
            class = "coc-text-lg-1 white-text coc-text-bold">
            <span class = "tcsc-car-6-icon" />
            REQUIREMENTS
          </nuxt-link>
        </Card>
      </div>
    </div>
  </master>
</template>

<script>
import Master from '~/components/common/master'
export default {
  name: 'AnalyticsIndex',
  components: {
    Master
  },

  data() {
    return {}
  }
}
</script>

<style lang="css" scoped>
.anltc-card {
	height: 180px;
}
</style>

<template>
  <div>
    <nuxt />
  </div>
</template>
<script>
export default {
  async created() {
    this.$axios
      .get('/app')
      .then(res => {
        this.$store.dispatch('setApp', res.data)
      })
      .catch(() => {
        // console.log(err)
      })
    const auth = this.$cookies.get('auth') || null
    if (!auth) {
      // this.$store.state.user.auth = null
    } else {
      this.$store.dispatch('setAuth', auth)
    }
    if (this.$store.state.core.auth) {
      this.$axios.defaults.headers.common[
        'Authorization'
      ] = this.$store.state.core.auth.token
    }
  }
}
</script>
<style>
html,
body {
  background-color: #2d2e2e !important;
}

*,
*:before,
*:after {
  box-sizing: border-box;
  margin: 0;
}

.button--green {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #3b8070;
  color: #3b8070;
  text-decoration: none;
  padding: 10px 30px;
}

.button--green:hover {
  color: #fff;
  background-color: #3b8070;
}

.button--grey {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #35495e;
  color: #35495e;
  text-decoration: none;
  padding: 10px 30px;
  margin-left: 15px;
}

.button--grey:hover {
  color: #fff;
  background-color: #35495e;
}
.coc-sider .ivu-menu-item.ivu-menu-item-active.ivu-menu-item-selected {
  background-color: #293542 !important;
}
</style>

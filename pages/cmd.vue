<template>
  <master>
    <div
      class="row">
      <div 
        class = "coc-dark-background-bg coc-border-1 coc-warning-border coc-standard-border-radius coc-margin-top-10px coc-padding-10px cmd">
        <p 
          v-for = "(message, m) in streamed" 
          :key = "m"
          :class = "[
            { 'coc-success-text': message.type === 'response' && message.status === 'success' },
            { 'coc-error-text': message.type === 'response' && message.status === 'error' },
            { 'coc-info-text': message.type === 'request' }
          ]"
          style="font-family: monospace !important">
          <span style="font-family: monospace !important">{{ message.type === 'request' ? '$ ' : '> ' }}</span>
          <span style="font-family: monospace !important">{{ message.content }}</span>
        </p>
      </div>
      <div v-coc-loading = "isLoading">
        <textarea
          v-model = "command"
          type="textarea"
          style = "font-family: monospace; border-bottom-left-radius: 5px; border-bottom-right-radius: 5px;"
          class = "coc-full-width coc-dark-background-bg coc-warning-border cmd-ta coc-info-text"
          @keyup.enter = "sendCommand"
          @keyup.up = "handleHistory(-1)"
          @keyup.down = "handleHistory(1)"/>
      </div>
      <tag
        :color = "connection.status ? 'success' : 'error'"
        style = "height: auto !important"
        class = "coc-text-md-2 coc-text-bold coc-padding-5px">
        {{ connection.status ? 'Connected' : 'Not Connected' }}
      </tag>
      <tag
        v-if = "cmdError"
        style = "height: auto !important"
        color = "error"
        class = "coc-text-md-2 coc-text-bold coc-padding-5px"><icon type = "ios-alert-outline"/> error</tag>
    </div>
  </master>
</template>

<script>
import Master from '~/components/common/master'
export default {
  name: 'Cmd',
  components: {
    Master
  },
  data() {
    return {
      command: '',
      cmdError: false,
      isLoading: false,
      streamed: [],
      historyPointer: 0,
      connection: { status: false }
    }
  },
  computed: {},
  mounted() {
    this.$axios.get('/nodes/status').then(res => {
      this.connection = res.data
    })
  },
  methods: {
    handleHistory(jumb) {
      if (!this.streamed.length) return
      this.historyPointer += jumb
      this.command = this.streamed.filter(r => r.type === 'request')[
        this.historyPointer
      ].content
    },
    sendCommand() {
      this.cmdError = false
      this.isLoading = true
      this.streamed.push({
        type: 'request',
        status: 'success',
        content: this.command
      })
      this.historyPointer = this.streamed.filter(
        r => r.type === 'request'
      ).length
      this.$axios({
        method: 'post',
        url: '/nodes',
        data: {
          command: this.command
        }
      })
        .then(res => {
          this.cmdError = false
          this.isLoading = false
          this.streamed.push({
            type: 'response',
            status: 'success',
            content: res.data.stdout
          })
          this.command = ''
        })
        .catch(err => {
          this.cmdError = true
          this.isLoading = false
          this.streamed.push({
            type: 'response',
            status: 'error',
            content: err.response.data.stderr
          })
          this.command = ''
        })
    }
  }
}
</script>

<style lang="css" scoped>
.cmd{
  height: 50vh;
  max-height: 50vh;
  overflow-y: auto;
  font-family: monospace;
  border-bottom: 0;
  border-bottom-left-radius: 0;
  border-bottom-right-radius: 0;
}
.cmd-ta{
  padding: 5px;
}
.cmd-ta:focus{
  outline: none;  
}
</style>

<template>
  <master :bleeding = "bleed">
    <audio
      ref = "alertPlayer"
      src = "/alerts/alert.mp3"
      type="audio/mpeg" />
    <tabs
      v-model = "tab"
      class = "t-container coc-dark-background-bg coc-padding-10px coc-margin-y-10px coc-standard-border-radius z-depth-3">
      <tab-pane 
        label = "All Hosts" 
        name = "all">
        <div class = "t-container-tab">
          <div
            v-for = "(host, h) in $_.sortBy(allHosts.filter(k => !k.deleted), s => s.last_status)"
            :key = "h">
            <div
              v-coc-mouse-over = "'coc-text-md-2 coc-warning-text'"
              v-coc-mouse-leave = "'coc-border-text'"
              class="row coc-smooth-font-size coc-border-text">
              <div class="col s8">
                <div class = "coc-text-bold coc-text-lg ">
                  <div class="col s4">
                    {{ host.name }}
                  </div>
                  <div class="col s3">
                    {{ host.ip }}
                  </div>
                </div>
                <p class = "coc-text-md-2 ">
                  <span class=" coc-text-body">
                    {{ host.last_status | CocCapitalizeName }} from {{ $moment(getLocalDate(host.first_record)).fromNow() }}
                    <small class="coc-text-sm-1">{{ $moment(getLocalDate(host.first_record)).format('D/M/YYYY h:m A') }}</small>
                  </span>
                </p>
              </div>
              <div class="col s4">
                <div class="col s12">
                  <tag 
                    :color = "statusToBoolean(host.last_status) ? 'success' : 'error'"
                    type = "dot"
                    class = "right "
                    size = "large">
                    <span>{{ host.last_status | CocCapitalizeName }}</span>
                  </tag>
                </div>
                <div class="col s12">
                  <i-button
                    icon = "ios-trash"
                    type = "text"
                    size = "small"
                    class = "coc-error-text right coc-margin-right-5px"
                    @click = "deleteJob(host)">
                    Delete
                  </i-button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </tab-pane>
      <tab-pane
        label = "Add Host"
        name = "add">
        <div class = "row coc-padding-x-10px t-container-tab">
          <div class="col l8 push-l2 s12">
            <card class = "coc-dark-background-bg coc-background-border">
              <div class="row">
                
                <coc-input
                  v-model = "input.ip"
                  :scope = "['add-host']"
                  :rules = "{ 
                    HasValue: true,
                    MatchesRegex: {
                      args: '^(([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\.){3}([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])$',
                      message: 'Your input must match the IP address formula'
                    }
                  }"
                  placeholder = "IP Address"
                  icon = " knocks-server"
                  light-model
                  labeled />
                <coc-input
                  v-model = "input.name"
                  :scope = "['add-host']"
                  :rules = "{ HasValue: true }"
                  placeholder = "Name"
                  icon = " knocksapp-label"
                  light-model
                  labeled />
                <coc-input
                  v-model = "input.private_key"
                  :scope = "['add-host']"
                  :rules = "{ HasValue: {active: false} }"
                  type = "textarea"
                  placeholder = "Private Key"
                  icon = " knocksapp-lock"
                  light-model
                  labeled />
                <coc-button
                  :scope = "['add-host']"
                  :request = "{ url: '/ping/', method: 'post', xdata: input }"
                  placeholder = "Add Host"
                  class = "right"
                  icon = "ios-add-circle"
                  type = "warning" />
              </div>
            </card>
          </div>
        </div>
      </tab-pane>
      <tab-pane 
        label = "Logs" 
        name = "logs"
        class = "t-container-tab">
        <div>
          <div class="row coc-border-0 coc-border-bottom-1 coc-padding-bottom-10px">
            <div class="col l3">
              <i-select
                v-model = "logsInput.name"
                placeholder = "Host Name.."
                filterable
                clearable>
                <i-option
                  v-for = "(host, h) in allHosts"
                  :key = "h"
                  :label = "host.name"
                  :value = "host.name" />
              </i-select>
            </div>
            <div class="col l3">
              <i-select
                v-model = "logsInput.status"
                placeholder = "Host Status.."
                filterable
                clearable>
                <i-option
                  v-for = "(host, h) in ['online', 'offline']"
                  :key = "h"
                  :label = "host"
                  :value = "host" />
              </i-select>
            </div>
            <div class="col l3">
              <date-picker 
                v-model = "logsInput.date"
                :start-date="$moment().toDate()" 
                type="daterange" 
                placement="bottom-end" 
                placeholder="Select date" 
                style="width: 100%"/>
            </div>
            <div class="col l3">
              <i-button
                icon = "ios-search"
                class = "right"
                @click = "getPingLogs">
                Fetch
              </i-button>
            </div>
          </div>
          <div class = "t-container-sm">
            <div
              v-for = "(host, h) in pingLogs.logs"
              :key = "h">
              <div
                v-coc-mouse-over = "'coc-text-md-2 coc-warning-text'"
                v-coc-mouse-leave = "'coc-border-text'"
                class="row coc-smooth-font-size coc-border-text">
                <div class="col s8">
                  <p class = "coc-text-bold coc-text-lg ">
                    {{ host.name }}
                    <span class="right coc-text-body">
                      Record from {{ $moment(getLocalDate(host.created_at)).fromNow() }}
                    </span>
                  </p>
                  <p class = "coc-text-md-2 ">
                    {{ host.ip }}
                    <span class="right coc-text-body">
                      {{ $moment(getLocalDate(host.created_at)).format('D/M/YYYY h:m A') }}
                    </span>
                  </p>
                </div>
                <div class="col s4">
                  <tag 
                    :color = "statusToBoolean(host.status) ? 'success' : 'error'"
                    type = "dot"
                    class = "right"
                    size = "large">
                    <span>{{ host.status | CocCapitalizeName }}</span>
                  </tag>
                </div>
              </div>
            </div>
          </div>
          <div 
            v-if = "pagination && pingLogs && pingLogs.logs && logsInput.page >= 0"
            style = " padding-top: 10px !important; margin-top: 5px; margin-bottom: 10px;" 
            class="col s12 ">
            <Page
              :total="pagination.pages"
              :page-size = "1"
              :current="pagination.page + 1"
              :styles = "{margin: 'auto', display: 'block', width: 'fit-content'}"
              @on-change = "changePage"/>
            <input-number
              v-model = "logsInput.limit"
              :min = "1"
              :max = "200"
              class = "right"
              size = "small" />
            <span class="right coc-border-text coc-padding-x-10px">Records Per Page</span>
          </div>
        </div>
      </tab-pane>
      <tab-pane 
        v-coc-loading = "onRender"
        coc-loader-bg = "#171d22"
        label = "Visualisation"
        name = "analytics">
        <div class="row coc-border-0 coc-border-bottom-1 coc-padding-bottom-10px">
          <div class="col l3">
            <i-select
              v-model = "logsInput.name"
              placeholder = "Host Name.."
              filterable
              clearable>
              <i-option
                v-for = "(host, h) in allHosts"
                :key = "h"
                :label = "host.name"
                :value = "host.name" />
            </i-select>
          </div>
          <div class="col l3">
            <i-select
              v-model = "logsInput.status"
              placeholder = "Host Status.."
              filterable
              clearable>
              <i-option
                v-for = "(host, h) in ['online', 'offline']"
                :key = "h"
                :label = "host"
                :value = "host" />
            </i-select>
          </div>
          <div class="col l3">
            <date-picker 
              v-model = "logsInput.date"
              :start-date="$moment().toDate()" 
              type="daterange" 
              placement="bottom-end" 
              placeholder="Select date" 
              style="width: 100%"/>
          </div>
          <div class="col l3">
            <i-button
              icon = " knocks-chart3"
              class = "right"
              @click = "getPingLogs({ stats: 'yes' })">Render</i-button>
          </div>
          <div 
            v-if = "!onRender && pingLogs.logs && pingLogs.logs.length" 
            class="row">
            <div class="col s12 coc-padding-top-30px">
              <div id="chart" />
            </div>
            <div class="col s12 coc-padding-top-30px">
              <div id="chart-area" />
            </div>
            <div class="col s12 coc-padding-top-30px">
              <div id="chart-line" />
            </div>
          </div>
        </div>
      </tab-pane>
    </tabs>
    <button-group
      size = "large"
      class = "right">
      <i-button
        :icon = "mute ? ' knocks-volume4' : ' knocks-mute'"
        :type = "mute ? 'success' : 'error'"
        @click = "toggleMute" >
        <span v-if = "mute">Unmute</span>
        <span v-else>Mute</span>
      </i-button>
      <i-button
        :icon = "allowNotifications ? ' knocks-bell-off' : ' knocks-bell4'"
        :type = "allowNotifications ? 'error' : 'success'"
        @click = "toggleNotifications" >
        <span v-if = "allowNotifications">Disable Notifications</span>
        <span v-else>Enable Notifications</span>
      </i-button>
    </button-group>
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
      bleed: false,
      apx: null,
      renders: 0,
      onRender: false,
      drawing: false,
      allowNotifications: true,
      pagination: null,
      logsInput: { page: 0 },
      pingLogs: { logs: [] },
      allHosts: [],
      tab: 'all',
      input: {
        ip: ''
      },
      mute: false
    }
  },
  watch: {
    tab(val) {
      if (val === 'logs') this.getPingLogs()
    }
  },
  mounted() {
    this.getAllHosts()
    setTimeout(() => {
      if (process.client) {
        this.apx = require('apexcharts')
      }
    }, 1000)
  },
  methods: {
    notifyMe(body) {
      if (!this.allowNotifications) return
      if (!window) return
      // Let's check if the browser supports notifications
      if (!('Notification' in window)) {
        alert('This browser does not support desktop notification')
      }

      // Let's check whether notification permissions have already been granted
      else if (Notification.permission === 'granted') {
        // If it's okay let's create a notification
        var notification = new Notification(body)
      }

      // Otherwise, we need to ask the user for permission
      else if (Notification.permission !== 'denied') {
        Notification.requestPermission().then(function(permission) {
          // If the user accepts, let's create a notification
          if (permission === 'granted') {
            var notification = new Notification(body)
          }
        })
      }

      // At last, if the user has denied notifications, and you
      // want to be respectful there is no need to bother them any more.
    },
    getPingLogs(params = {}) {
      this.$axios({
        url: '/ping/host/logs',
        method: 'get',
        params: { ...this.encodedQuery(), ...params }
      }).then(res => {
        this.pingLogs = res.data
        this.pagination = this.$_.omit(res.data, ['logs'])
        this.logsInput.limit = this.pagination.limit
        if (this.tab === 'analytics') {
          this.renderAllCharts()
        }
      })
    },
    getAllHosts() {
      this.$axios({
        url: '/ping',
        method: 'get'
      })
        .then(res => {
          this.allHosts = res.data
          const offline = res.data.filter(h => h.last_status === 'offline')
          const vm = this
          if (offline.length) {
            if (offline.length > 3) {
              this.$refs.alertPlayer.src = '/alerts/alert.mp3'
              this.bleed = 'error'
            } else if (
              offline.filter(
                h =>
                  this.$moment(h => first_record).diff(
                    this.$moment(h => last_check),
                    'm'
                  ) > 5
              ).length
            ) {
              this.$refs.alertPlayer.src = '/alerts/alert.mp3'
              this.bleed = 'error'
            } else {
              this.$refs.alertPlayer.src = '/alerts/quick.m4a'
              this.bleed = 'warning'
            }
            this.$refs.alertPlayer.play()
            this.notifyMe(
              `${offline.map(o => o.name).join(', ')} ${
                offline.length === 1 ? 'is' : 'are'
              } Down`
            )
          } else {
            this.$refs.alertPlayer.pause()
            this.bleed = 'success'
          }
          setTimeout(() => {
            this.getAllHosts()
          }, 15000)
        })
        .catch(() => {
          setTimeout(() => {
            this.getAllHosts()
          }, 15000)
        })
    },
    statusToBoolean(status) {
      return status.toLowerCase() === 'online'
    },
    toggleMute() {
      if (this.mute) {
        this.$refs.alertPlayer.muted = false
        this.mute = false
      } else {
        this.$refs.alertPlayer.muted = true
        this.mute = true
      }
    },
    toggleNotifications() {
      this.allowNotifications = !this.allowNotifications
    },
    changePage(e) {
      this.logsInput.page = e
      this.getPingLogs()
    },
    encodedQuery(input = this.logsInput) {
      const final = this.$_.cloneDeep(input)
      if (final.page > 0) {
        final.page = final.page - 1
      }
      if (final.date) {
        final.date = final.date.join(',')
      }
      if (this.tab === 'analytics') input.stats = 'yes'
      else input.stats = 'no'
      return final
    },
    createPricesChart(args) {
      var options = {
        chart: {
          zoom: {
            enabled: false,
            type: 'x',
            autoScaleYaxis: false,
            zoomedArea: {
              fill: {
                color: '#90CAF9',
                opacity: 0.4
              },
              stroke: {
                color: '#0D47A1',
                opacity: 0.4,
                width: 1
              }
            }
          },
          height: 350,
          type: args.type || 'area'
        },
        title: {
          text: args.title || 'Host Status Timeline',
          align: 'left'
        },
        subtitle: {
          text: args.subtitle || 'Up means Onine, Downs Means Offline',
          align: 'left'
        },
        labels: args.labels || {
          enabled: false
        },
        dataLabel: args.dataLabel,
        dataLabels: args.dataLabels || {},
        stroke: args.stroke || {
          curve: 'smooth'
        },
        markers: args.markers || {},
        yaxis: args.yaxis || {
          opposite: true
        },
        legend: {
          horizontalAlign: 'left'
        },
        series: args.series,

        xaxis: args.xaxis
      }

      const chart = new this.apx.default(
        document.querySelector(args.id),
        options
      )
      chart.render()
    },
    getLocalDate(date) {
      return this.$moment(this.$moment.utc(date)).local()
    },
    deleteJob(job) {
      this.$axios({ url: `/ping/${job._id}`, method: 'delete' }).then(() => {
        this.allHosts.splice(this.allHosts.indexOf(h => h._id === job._id), 1)
      })
    },
    renderAllCharts() {
      this.onRender = true
      setTimeout(() => {
        this.drawing = true
        this.onRender = false
        const dates = this.$_.uniqBy(this.pingLogs.logs, p =>
          this.$moment(this.getLocalDate(p.created_at)).format('D/M h:m A')
        ).map(l =>
          this.$moment(this.getLocalDate(l.created_at)).format('D/M h:m A')
        )
        dates.push(this.$moment().format('Now'))
        const names = this.$_.uniqBy(this.pingLogs.logs, p => p.name).map(
          l => l.name
        )
        let i
        let j
        let temp
        const series = []
        let filtered
        let latestStatus = 'online'
        for (j = 0; j < names.length; j += 1) {
          temp = {
            data: [],
            name: names[j]
          }
          latestStatus = this.pingLogs.logs.filter(p => p.name === names[j])
            .length
            ? this.pingLogs.logs.filter(p => p.name === names[j])[0].status
            : 'online'
          for (i = 0; i < dates.length; i += 1) {
            filtered = this.pingLogs.logs.filter(
              p =>
                p.name === names[j] &&
                this.$moment(this.getLocalDate(p.created_at)).format(
                  'D/M h:m A'
                ) === dates[i]
            )
            latestStatus = filtered.length ? filtered[0].status : latestStatus
            temp.data.push(this.statusToBoolean(latestStatus) ? 1 : 0)
          }
          if (
            this.allHosts.length &&
            this.allHosts.filter(f => f.name === names[j]).length
          ) {
            temp.data.push(
              this.statusToBoolean(
                this.allHosts.filter(f => f.name === names[j])[0].last_status
              )
                ? 1
                : 0
            )
          } else temp.data.push(0)
          series.push(temp)
        }
        console.log(series)
        setTimeout(() => {
          // Days
          this.createPricesChart({
            id: '#chart',
            type: 'radar',
            labels: dates,
            xaxis: {
              type: 'category',
              minHeight: 100,
              style: {
                fontSize: '10px'
              }
            },
            series
          })

          this.createPricesChart({
            id: '#chart-area',
            type: 'area',
            labels: dates,
            xaxis: {
              type: 'category',
              minHeight: 100,
              style: {
                fontSize: '10px'
              },
              formatter: v => v.slice(4)
            },
            series
          })

          this.createPricesChart({
            id: '#chart-line',
            type: 'line',
            labels: dates,
            xaxis: {
              type: 'category',
              minHeight: 100,
              style: {
                fontSize: '10px'
              },
              formatter: v => v.slice(4)
            },
            series
          })

          this.renders += 1
          this.drawing = false
          if (this.$route.query.scroll) {
            setTimeout(() => {
              const eTarget = document.getElementById(this.$route.query.scroll)
              if (eTarget) {
                eTarget.scrollIntoView()
              }
            }, 500)
          }
        }, 1000)
      }, 500)
    }
  }
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

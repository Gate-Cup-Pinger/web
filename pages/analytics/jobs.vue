<template>
  <master @collapse = "boundLogos(1000)">
    <div v-if = "user">
      <div class="row">
        <span class = "coc-text-bold coc-text-md-2">
          Jobs
          <span v-if = "jobs && jobs.length">({{ jobs.length }})</span>
        </span>
        <Button
          icon = "ios-funnel-outline"
          class = "right"
          @click = "config.drawer = true"/>
        <Button
          icon = "ios-refresh"
          class = "right coc-margin-x-5px"
          @click = "formatQuery"/>
      </div>
      <Drawer 
        v-model="config.drawer" 
        title="Filters" 
        width = "80%"
        closable>
        <div 
          v-if = "config.drawer" 
          class="row coc-house-keeper">
          <div class="row coc-section">
            <div class = "col s12  m12 coc-margin-0 coc-padding-x-5px">
              <div class="row coc-house-keeper">
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Search By Number</p>
                  <coc-input
                    slot = "title"
                    v-model = "input.job_no"
                    icon = "ios-grid-outline"
                    placeholder = "Search By Job Number.."
                    light-model/>
                </div>
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Status</p>
                  <coc-select
                    v-model = "input.status"
                    placeholder = "Status.."
                    light-model
                    clearable>
                    <template slot-scope = "{options}">
                      <Option
                        value = "running"
                        label = "Running"/>
                      <Option
                        value = "finished"
                        label = "Finished"/>
                      <Option
                        value = "postponed"
                        label = "Postponed"/>
                    </template>
                  </coc-select>
                </div>
              </div>
              <div class="row coc-house-keeper">
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Sort</p>
                  <coc-select
                    v-model = "input.sort"
                    placeholder = "Sort By.."
                    light-model>
                    <template slot-scope = "{options}">
                      <Option
                        value = "job_no"
                        label = "Job Number"/>
                      <Option
                        value = "created_at"
                        label = "Date"/>
                      <Option
                        value = "status"
                        label = "Status"/>
                    </template>
                  </coc-select>
                </div>
                <div class="col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Time Range</p>
                  <DatePicker 
                    v-model = "input.date"
                    :start-date="$moment().toDate()" 
                    type="daterange" 
                    placement="bottom-end" 
                    placeholder="Select date" 
                    style="width: 100%"/>
                </div>
              </div>
              <div class="row coc-house-keeper">
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Phone</p>
                  <coc-input
                    v-model = "input.phone"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { phone: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.jobs, j => j.client.phone).map(j => j.client.phone)"
                    icon = "ios-search"
                    placeholder = "Search by Phone.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    light-model />
                </div>
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Client Name</p>
                  <coc-input
                    v-model = "input.name"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { name: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.jobs, j => j.client.name).map(j => j.client.name)"
                    icon = "ios-search"
                    placeholder = "Search by client name.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    light-model />
                </div>
              </div>
              <div class="row coc-house-keeper">
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Car Brand</p>
                  <coc-input
                    v-model = "input.brand"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { brand: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.jobs, j => j.car.brand).map(j => j.car.brand)"
                    icon = "ios-search"
                    placeholder = "Search by Phone.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    light-model />
                </div>
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Car Model</p>
                  <coc-input
                    v-model = "input.model"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { model: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.jobs, j => j.car.model).map(j => j.car.model)"
                    icon = "ios-search"
                    placeholder = "Search by car model.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    light-model />
                </div>
              </div>
              <div class="row">
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Price Range</p>
                  <Slider 
                    v-model="input.price"
                    :step = "config.price.step"
                    :max = "config.price.max" 
                    range 
                    show-stops/>
                </div>
                <div class = "col s12 l6 right">
                  <p class = "coc-content-text coc-text-normal-2">Arrangement</p>
                  <button-group
                    shape = "circle"
                    class = "coc-full-width"
                    long>
                    <Button
                      :type = "input.desc === 'no' ? 'info' : 'default'"
                      icon = "md-arrow-round-up"
                      style = "width:50%"
                      @click = "input.desc = 'no'"/>
                    <Button
                      :type = "input.desc === 'yes' ? 'info' : 'default'"
                      icon = "md-arrow-round-down"
                      style = "width:50%"
                      @click = "input.desc = 'yes'"/>
                  </button-group>
                </div>
              </div>
              <div class = "col s12">
                <Button 
                  type = "primary" 
                  icon = "ios-funnel-outline coc-text-lg"
                  long 
                  @click = "formatQuery()">
                  <span class="coc-text-lg">Filter Jobs</span>
                </Button>
              </div>
            </div>
          </div>
        </div>
        <Divider orientation = "right">Search Settings</Divider>
        <p class = "coc-text-md-2">Price Range Maximum</p>
        <input-number 
          v-model = "config.price.max" 
          placeholder = "Price Range Maximum" 
          size = "large"
          style = "width: 100%" />
        <p class = "coc-text-md-2">Price Range Step</p>
        <input-number 
          v-model = "config.price.step" 
          placeholder = "Price Range Step" 
          size = "large"
          style = "width: 100%" />
      </Drawer>
      <div
        v-if = "jobs && jobs.length"
        class="row">
        <div class = "row">
          <p class = "coc-text-title coc-text-bold">Job Status</p>
          <divider />
          <div
            v-coc-mouse-leave = "'jello'"
            v-coc-mouse-over = "'pulse'"
            class="col l4 s12 animated pulse">
            <i-circle
              :percent="jobs.filter(j => j.status.toLowerCase() === 'running').length * 100 / jobs.length"
              :size = "200"
              class = "centerized ">
              <div 
                class="demo-Circle-inner coc-text-md-1" 
                style="">
                <span class = "coc-info-text coc-text-bold coc-padding-bottom-5px">Running</span>
                <p class = "coc-padding-top-5px">Count: {{ jobs.filter(j => j.status.toLowerCase() === 'running').length | CocMigaNumber }}</p>
                <p class = "coc-padding-top-5px coc-text-bold">{{ jobs.filter(j => j.status.toLowerCase() === 'running').length * 100 / jobs.length | CocToFixedOne }}%</p>
              </div>
            </i-circle>
          </div>
          <div
            v-coc-mouse-leave = "'jello'"
            v-coc-mouse-over = "'pulse'"
            class="col l4 s12 animated pulse">
            <i-circle
              :percent="jobs.filter(j => j.status.toLowerCase() === 'finished').length * 100 / jobs.length"
              :size="200"
              stroke-color = "#19be6b"
              class = "centerized">
              <div
                class="demo-Circle-inner coc-text-md-1" 
                style="">
                <span class = "coc-success-text coc-text-bold coc-border-0 coc-border-bottom-1 coc-text-md-2">Finished</span><br>
                <p class = "coc-padding-top-5px">Count: {{ jobs.filter(j => j.status.toLowerCase() === 'finished').length | CocMigaNumber }}</p>
                <p class = "coc-padding-top-5px coc-text-bold">{{ jobs.filter(j => j.status.toLowerCase() === 'finished').length * 100 / jobs.length | CocToFixedOne }}%</p>
              </div>
            </i-circle>
          </div>
          <div
            v-coc-mouse-leave = "'jello'"
            v-coc-mouse-over = "'pulse'"
            class="col l4 s12 animated pulse">
            <i-circle 
              :percent="jobs.filter(j => j.status.toLowerCase() === 'postponed').length * 100 / jobs.length"
              :size = "200"
              stroke-color = "#ff9900"
              class = "centerized">
              <div 
                class="demo-Circle-inner coc-text-md-1" 
                style="">
                <span class = "coc-warning-text coc-text-bold coc-padding-bottom-5px">Postponed</span>
                <p class = "coc-padding-top-5px">Count: {{ jobs.filter(j => j.status.toLowerCase() === 'postponed').length | CocMigaNumber }}</p>
                <p class = "coc-padding-top-5px coc-text-bold">{{ jobs.filter(j => j.status.toLowerCase() === 'postponed').length * 100 / jobs.length | CocToFixedOne }}%</p>
              </div>
            </i-circle>
          </div>
        </div>
        <divider class = "coc-margin-y-10px" />
        <div class="row">
          <p class = "coc-text-title coc-text-bold">Job Profites</p>
          <divider />
          <jobs-stats-charts
            :apx = "ApexCharts"
            :jobs-model = "{jobs, ...pagination}"
            date-format = "yyyy"
            id-offset = "annual"/>
        </div>
        <divider class = "coc-margin-y-10px" />
        <div class="row">
          <p class = "coc-text-title coc-text-bold">Car Makes</p>
          <divider />
          <div
            v-for = "(make, m) in getSortedCars(jobs)"
            :key = "m"
            class="col l4 s12 animated pulse coc-padding-y-10px">
            <div
              :style = "`background: url(/snaps/brands/png/${make.split(' ').join('-').toLowerCase()}.png); background-size: cover`"
              class = "bg-blur" />
            <i-circle 
              :percent="jobs.filter(j => j.car.brand === make).length * 100 / jobs.length"
              :size = "200"
              :style = "`background:rgba(0,0,0,0.85); border-radius: 50%;`"
              class = "centerized"
              stroke-color = "#ed4014">
              <div
                
                class="demo-Circle-inner coc-text-md-1 coc-secondary-text" 
                style="">
                <span class = "coc-error-text coc-text-bold coc-padding-bottom-5px">{{ make | CocCapitalizeName }}</span>
                <p class = "coc-padding-top-5px">Count: {{ jobs.filter(j => j.car.brand === make).length | CocMigaNumber }}</p>
                <p class = "coc-padding-top-5px coc-text-bold">{{ jobs.filter(j => j.car.brand === make).length * 100 / jobs.length | CocToFixedOne }}%</p>
                <p class = "coc-padding-top-5px coc-text-bold">
                  {{ $_.sumBy(jobs.filter(j => j.car.brand === make), o => o.price) | CocToMigaNumber }} LE
                </p>
              </div>
            </i-circle>
          </div>
        </div>
      </div>
    </div>
    <Card v-else>
      <p class = "coc-text-title coc-error-text center">
        <Icon type = "ios-alert-outline"/>
        You Are Not Logged In
      </p>
      <br>
      <p class="center">
        <button-group>
          <Button
            type = "default"
            size = "large"
            shape = "circle"
            style = "width: 120px"
            @click = "askForLogin">
            Login
          </Button>
        </button-group>
      </p>
    </Card>
  </master>
</template>

<script>
import Master from '~/components/common/master'
import JobsStatsCharts from '~/components/jobs/JobsStatsCharts'
export default {
  name: 'JobsAnalyticsIndex',
  components: {
    Master,
    JobsStatsCharts
  },
  data() {
    return {
      ApexCharts: null,
      list: false,
      loaders: {},
      flashers: null,
      preflash: null,
      isLoading: false,
      formattedQuery: {},
      pagination: null,
      jobs: [],
      config: {
        drawer: false,
        price: {
          step: 100,
          max: 7000
        }
      },
      input: {
        job_no: '',
        status: null,
        date: null,
        price: [0, 6900],
        phone: '',
        products: [],
        sort: 'job_no',
        desc: 'yes',
        page: 0,
        limit: 10
      }
    }
  },
  computed: {
    user() {
      return this.$store.state.core.auth
    }
  },
  watch: {
    list(val) {
      if (val && this.input.limit === 10) {
        this.input.limit = 15
      } else if (!val && this.input.limit === 15) {
        this.input.limit = 10
      }
      this.formatQuery()
    }
  },
  mounted() {
    setTimeout(() => {
      this.formatQuery()
      if (process.client) {
        this.ApexCharts = require('apexcharts')
      }
      document.onresize = this.boundLogos
    }, 1000)
  },
  methods: {
    askForLogin() {
      this.$root.$emit('askForLogin')
    },
    getJobs(cb = null) {
      this.isLoading = true
      this.$axios({
        method: 'get',
        url: '/job',
        params: { ...this.formattedQuery, stats: 'yes' }
      })
        .then(({ data }) => {
          this.jobs = data.jobs
          this.config.drawer = false
          this.isLoading = false
          this.pagination = this.$_.omit(data, ['jobs'])
          this.loaders = {}
          // pull logos
          this.boundLogos()
          this.jobs.forEach(j => {
            this.loaders[j._id] = false
          })
          if (cb) {
            cb.handler(cb.args)
          }
        })
        .catch(res => {
          this.isLoading = false
        })
    },
    boundLogos(time = 500) {
      setTimeout(() => {
        if (document) {
          const logos = document.getElementsByClassName('bg-blur')
          let i
          for (i = 0; i < logos.length; i += 1) {
            logos[i].style.left = `${logos[i].parentElement.offsetWidth / 2 -
              85}px`
            console.log('ss')
          }
        }
      }, time)
    },
    encodedQuery(input = this.input) {
      const final = this.$_.cloneDeep(input)
      if (final.page > 0) {
        final.page = final.page - 1
      }
      if (final.date) {
        final.date = final.date.join(',')
      }
      if (final.price) {
        final.price = final.price.join(',')
      }
      return final
    },
    formatQuery(cb = null) {
      this.formattedQuery = this.encodedQuery()
      this.getJobs(cb)
      this.$router.push('/analytics/jobs', { query: this.formattedQuery })
    },
    changePage(e) {
      this.input.page = e
      this.formatQuery()
    },
    updateStatus(job, status) {
      if (job.status === status) return
      this.loaders[job._id] = true
      this.$axios({
        url: `/job/${job._id}`,
        method: 'put',
        data: {
          ...this.$_.pick(job, [
            'car',
            'client',
            'requirements',
            'reciptionist',
            'complain',
            'notes',
            'status',
            'operations'
          ]),
          status
        }
      })
        .then(() => {
          this.loaders[job._id] = false
          this.formatQuery({
            handler: this.flash,
            args: { job: job._id, time: 1000 }
          })
          if (status === 'running' || job.status === 'running')
            this.$root.$emit('updateRunningJobs')
        })
        .catch(() => {
          this.loaders[job._id] = false
        })
    },
    getSortedCars(jobs) {
      return this.$_.reverse(
        this.$_.sortBy(
          this.$_.uniqBy(jobs, j => j.car.brand).map(j => ({
            ...j,
            count: jobs.filter(i => i.car.brand === j.car.brand).length
          })),
          ['count', o => o.car.brand]
        )
      ).map(mp => mp.car.brand)
    },
    flash(args) {
      setTimeout(() => {
        this.flashers = args.job
        setTimeout(() => {
          this.preflash = args.job
          this.flashers = null
        }, args.time || 1000)
      }, 700)
    },
    deleteJob(job) {
      this.loaders[job._id] = true
      this.$axios({ url: `/job/${job._id}`, method: 'delete' })
        .then(() => {
          this.loaders[job._id] = false
          this.formatQuery()
        })
        .catch(() => {
          this.loaders[job._id] = false
        })
    }
  }
}
</script>

<style lang="css" scoped>
.job-status-section {
  max-height: 70vh;
  overflow-y: scroll
}
.bg-blur{
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
  height: 170px;
  width: 170px;
  position: absolute;
  border-radius: 50%;
  transition: left linear 0.4s;
  margin-top: 15px;
}
</style>

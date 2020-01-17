<template>
  <master>
    <div v-if = "user">
      <div class="row">
        <span class = "coc-text-bold coc-text-md-2">
          Move
          <span v-if = "move.move && move.move.length">({{ move.move.length }})</span>
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
                <Divider 
                  class = "coc-margin-y-0" 
                  orientation = "left"><span class = "coc-text-bold coc-info-shade-4-text coc-text-sm">Item Data</span> </Divider>
                <div class = "col s12 l6">
                  <coc-input
                    v-model = "input.part"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/move', params: { part: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.move.filter(s => !!s.item.name), j => j.name).map(j => j.item.name)"
                    labeled
                    icon = "ios-search"
                    placeholder = "Search by Part Name.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    light-model />
                </div>
                <div class = "col s12 l6">
                  <coc-select
                    v-model = "input.category"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/move', params: { categoryac: model.meta.query, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.move.filter(s => !!s.item.category), j => j.item.category).map(j => j.item.category)"
                    placeholder = "Category.."
                    labeled
                    filterable
                    light-model
                    multiple
                    allow-autocomplete
                    clearable />
                </div>
                <div class="col s12 l6">
                  <p class = "coc-content-text coc-text-small">Entery Time</p>
                  <DatePicker 
                    v-model = "input.date"
                    :start-date="$moment().toDate()"
                    labeled 
                    type="daterange" 
                    placement="bottom-end" 
                    placeholder="Select date" 
                    style="width: 100%"/>
                </div>
              </div>
              <div class="row coc-house-keeper">
                <div class = "col s12 l6">
                  <Slider 
                    v-model="input.price"
                    :step = "config.price.step"
                    :max = "config.price.max"
                    labeled 
                    range 
                    show-stops/>
                </div>
                <div class = "col s12 l6">
                  <div class="right">
                    <i-switch 
                      v-model = "input.external"
                      :disabled = "input.ignore_external" 
                      light-model
                      class = "right coc-margin-top-10px"/>   
                    <span class="coc-padding-x-5px right">
                      <span>External Resources</span><br>
                      <small class="right">(salaries, taxs, etc..)</small>
                    </span>
                  </div>
                  <div class="coc-padding-x-20px">
                    <i-switch 
                      v-model = "input.ignore_external" 
                      light-model
                      class = "right coc-margin-top-10px"/>   
                    <span class="coc-padding-x-5px right">
                      <span>Select All</span><br>
                      <small class="right">(select all externals and not externals)</small>
                    </span>
                  </div>
                </div>
              </div>
              <Divider 
                class = "coc-margin-y-0" 
                orientation = "left"><span class = "coc-text-bold coc-info-shade-4-text coc-text-sm">Vendor Data</span> </Divider>
              <div class="row coc-house-keeper">
                <div class = "col s12 l6">
                  <coc-input
                    v-model = "input.phone"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/move', params: { phone: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.move.filter(s => !!s.item.vendor), j => j.item.vendor.phone).map(j => j.item.vendor.phone)"
                    labeled
                    icon = "ios-search"
                    placeholder = "Search by Phone.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    light-model />
                </div>
                <div class = "col s12 l6">
                  <coc-input
                    v-model = "input.name"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/move', params: { name: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.move.filter(s => !!s.item.vendor), j => j.item.vendor.name).map(j => j.item.vendor.name)"
                    labeled
                    icon = "ios-search"
                    placeholder = "Search by vendor name.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    light-model />
                </div>
              </div>
              <Divider 
                class = "coc-margin-y-0" 
                orientation = "left"><span class = "coc-text-bold coc-info-shade-4-text coc-text-sm">Car Compatibility</span> </Divider>
              <div class="row coc-house-keeper">
                <div class = "col s12 l6">
                  <coc-select
                    v-model = "input.brand"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/move', params: { brandac: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.move, j => j.item.car.brand).map(j => j.item.car.brand)"
                    labeled
                    icon = "ios-search"
                    placeholder = "Search by Car Brand.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    filterable
                    multiple
                    light-model />
                </div>
                <div class = "col s12 l6">
                  <coc-select
                    v-model = "input.model"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/move', params: { modelac: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.move, j => j.item.car.model).map(j => j.item.car.model)"
                    labeled
                    icon = "ios-search"
                    placeholder = "Search by car model.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    filterable
                    multiple
                    light-model />
                </div>
                <div class="col s12 l6">
                  <coc-select
                    v-model = "input.release"
                    :rules = "{ HasValue: true }"
                    :data = "generateYears()"
                    labeled
                    placeholder = "Release Year"
                    icon = "ios-calendar"
                    filterable
                    clearable
                    multiple
                    light-model />
                </div>
              </div>
              <Divider 
                class = "coc-margin-y-0" 
                orientation = "left"><span class = "coc-text-bold coc-info-shade-4-text coc-text-sm">Sorting</span> </Divider>
              <div class="row">
                <div class = "col s12 l6">
                  <coc-select
                    v-model = "input.sort"
                    labeled
                    placeholder = "Sort By.."
                    light-model>
                    <template slot-scope = "{options}">
                      <Option
                        value = "created_at"
                        label = "Date"/>
                      <Option
                        value = "price"
                        label = "Price"/>
                      <Option
                        value = "status"
                        label = "Status"/>
                    </template>
                  </coc-select>
                </div>
                <div class = "col s12 l6 right coc-margin-top-20px">
                  <button-group
                    labeled
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
                  <span class="coc-text-lg">Filter</span>
                </Button>
              </div>
            </div>
          </div>
        </div>
        <Divider 
          class = "coc-margin-y-0" 
          orientation = "right">Search Settings</Divider>
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
      <table
        v-if = "isMounted && move && move.move.length"
        class = "coc-full-width">
        <tr>
          <th 
            v-for = "(col, c) in columns" 
            :key = "c"
            class = "coc-primary-bg coc-padding-y-10px coc-background-text">
            {{ col.title }}
          </th>
        </tr>
        <tr 
          v-for = "(move, s) in move.move" 
          :key = "s"
          class = "coc-border-border center coc-info-hover-tint-8-bg">
          <td>
            <icon 
              v-if = "move.count > 0"
              type = "md-arrow-up"
              class = "coc-success-text coc-text-lg" />
            <icon 
              v-else
              type = "md-arrow-down"
              class = "coc-error-text coc-text-lg" />
          </td>
          <td class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px">
            <nuxt-link
              :to = "`/stock/${move.item._id}`">
              {{ move.item.name }}
            </nuxt-link>
          </td>
          <td class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px">
            {{ $moment(move.created_at).format('D/M/YYYY h:m A') }}<br>
            {{ $moment(move.created_at).fromNow() }}
          </td>
          <td class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px">{{ move.price }}</td>
          <td class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px">{{ move.count }}</td>
          <td
            class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px center">
            <Tooltip content = "Export">
              <Button
                icon = "md-arrow-down coc-error-text"
                class = "coc-border-0"
                @click = "importMove(move)" />
            </Tooltip>
            <Tooltip content = "Import">
              <Button
                icon = "md-arrow-up coc-success-text"
                class = "coc-border-0"
                @click = "importMove(move, -1)" />
            </Tooltip>
            <!--             <Tooltip content = "Trash">
              <Button
                icon = "ios-trash coc-error-text"
                class = "coc-border-0"
                @importMove = "(move, -1)" />
            </Tooltip> -->
            <div class="coc-full-width coc-margin-top-5px">
              <Tooltip content = "Count">
                <input-number
                  v-model = "moveCount"
                  :min = "1"/>
              </Tooltip>
            </div>
          </td>
        </tr>
        <tr v-if = "move.move && move.move.length">
          <td />
          <td />
          <td class="center coc-text-bold">Total {{ $_.sumBy(move.move, o => o.price) }} LE</td>
          <td />
        </tr>
      </table>
      <div
        v-else
        class = "coc-text-title center">
        <span class="tcsc-oil-icon"/>
        No Move Available
        <div class="row">
          <Button
            size = "large"
            icon = " tcsc-car-battery-1-icon coc-text-md-2"
            @click = "addMove">Add New</Button>
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
export default {
  name: 'Index',
  components: {
    Master
  },
  data() {
    return {
      isMounted: false,
      isLoading: true,
      moveCount: 1,
      move: { move: [] },
      columns: [
        {
          title: ''
        },
        {
          title: 'Part',
          key: 'name'
        },
        {
          title: 'Date',
          key: 'category'
        },
        {
          title: 'Price',
          key: 'price'
        },
        {
          title: 'Count',
          key: 'count'
        },
        {
          title: 'Actions'
        }
      ],
      config: {
        drawer: false,
        price: {
          step: 100,
          max: 7000
        }
      },
      pagination: null,
      formattedQuery: {},
      input: {
        ignore_external: true,
        category: [],
        status: null,
        date: null,
        price: [0, 6900],
        phone: '',
        products: [],
        sort: 'created_at',
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
  mounted() {
    this.formatQuery(this.$route.query)
    setTimeout(() => {
      this.isMounted = true
    }, 1000)
  },
  methods: {
    addMove() {
      this.$root.$emit('addMove')
    },
    askForLogin() {
      this.$root.$emit('askForLogin')
    },
    generateYears() {
      let i
      let result = []
      const year = parseInt(this.$moment().format('YYYY'), 10) + 1
      for (i = 0; i < 100; i += 1) {
        result.push((year - i).toString())
      }
      return result
    },
    getMove() {
      this.$router.push({
        path: '/moves',
        query: this.encodedQuery()
      })
      this.$axios({
        method: 'get',
        url: '/move',
        params: { ...this.formattedQuery, stats: 'yes' }
      }).then(res => {
        this.move = res.data
        this.config.drawer = false
        this.isLoading = false
        this.pagination = this.$_.omit(res.data, ['move'])
        this.$router.push({
          path: '/moves',
          query: this.encodedQuery()
        })
      })
    },
    decodedQuery(query) {
      const final = this.$_.cloneDeep(query)
      // if (final.external !== undefined) {
      //   final.external = final.external === 'yes'
      // }
      // if (final.ignore_external !== undefined) {
      //   final.ignore_external = final.ignore_external === 'yes'
      // }
      final.ignore_external = 'yes'
      if (final.page !== undefined) {
        final.page = parseInt(final.page, 10) - 1
      }
      // if (final.date) {
      //   final.date = final.date.split(',')
      // }
      if (final.brand) {
        final.brand = final.brand.split(',')
      }
      if (final.model) {
        final.model = final.model.split(',')
      }
      if (final.release) {
        final.release = final.release.split(',')
      }
      if (final.price) {
        final.price = final.price.split(',')
      }
      return final
    },
    encodedQuery(input = this.input) {
      const final = this.$_.cloneDeep(input)
      // if (final.external !== undefined) {
      //   final.external = final.external ? 'yes' : 'no'
      // }
      // if (final.ignore_external !== undefined) {
      //   final.ignore_external = final.ignore_external ? 'yes' : 'no'
      // }
      final.ignore_external = 'yes'
      if (final.page > 0) {
        final.page = final.page - 1
      }
      if (final.date && final.date.join) {
        final.date = final.date.join(',')
      } else {
        final.date = null
      }
      if (final.category && final.category.join) {
        final.category = final.category.join(',')
      } else {
        final.category = null
      }
      if (final.brand && final.brand.join) {
        final.brand = final.brand.join(',')
      } else {
        final.brand = null
      }
      if (final.model && final.model.join) {
        final.model = final.model.join(',')
      } else {
        final.model = null
      }
      if (final.release && final.release.join) {
        final.release = final.release.join(',')
      } else {
        final.release = null
      }
      if (final.price && final.price.join) {
        final.price = final.price.join(',')
      } else {
        final.price = null
      }
      return final
    },
    formatQuery(input = this.input) {
      this.formattedQuery = this.encodedQuery(input)
      this.getMove()
    },
    importMove(move, factor = 1) {
      // some code
      this.isLoading = true
      this.$axios({
        method: 'post',
        url: `/stock/${move.item._id}/move`,
        data: {
          item: move.item,
          count: this.moveCount * factor
        }
      })
        .then(() => {
          this.formatQuery()
          this.isLoading = false
          this.$Message.success('Done')
        })
        .catch(err => {
          this.isLoading = false
          if (err.response.status === 404) {
            this.$Message.error('Stock Not Found')
          }
        })
    }
  }
}
</script>

<style lang="css" scoped>
</style>

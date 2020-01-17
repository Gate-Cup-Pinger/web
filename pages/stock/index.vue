
<template>
  <master>
    <div v-if = "user">
      <div class="row">
        <span class = "coc-text-bold coc-text-md-2">
          Stock
          <span v-if = "stock.stock && !isLoading && !hasError && stock.stock.length && isMounted">({{ stock.stock.length }})</span>
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
                    :autocomplete-remote = "model => ({ method: 'get', url: '/stock', params: { part: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.stock.filter(s => !!s.name), j => j.name).map(j => j.name)"
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
                    :autocomplete-remote = "model => ({ method: 'get', url: '/stock', params: { categoryac: model.meta.query, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.stock.filter(s => !!s.category), j => j.category).map(j => j.category)"
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
                    :autocomplete-remote = "model => ({ method: 'get', url: '/stock', params: { phone: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.stock.filter(s => !!s.vendor), j => j.vendor.phone).map(j => j.vendor.phone)"
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
                    :autocomplete-remote = "model => ({ method: 'get', url: '/stock', params: { name: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.stock.filter(s => !!s.vendor), j => j.vendor.name).map(j => j.vendor.name)"
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
                    :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { brandac: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.stocks, j => j.car.brand).map(j => j.car.brand)"
                    :data = "brands"
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
                  <coc-input
                    v-model = "input.modelac"
                    labeled
                    icon = "ios-search"
                    placeholder = "Search by car model.."
                    class = "coc-house-keeper"
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
      <div 
        v-if = "isMounted && stock && stock.stock.length" 
        class="row coc-house-keeper">
        <table
          style = "max-width: 100%;overflow-x: scroll;display: block;"
          class = "col s12">
          <tr>
            <th 
              v-for = "(col, c) in columns" 
              :key = "c"
              class = "coc-primary-bg coc-padding-y-10px coc-background-text coc-padding-x-5px">
              {{ col.title }}
            </th>
          </tr>
          <tr 
            v-for = "(stock, s) in stock.stock" 
            :key = "s"
            class = "coc-border-border center coc-info-hover-tint-8-bg">
            <td
              class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px coc-padding-x-4px"
              style = "text-align: left">
              <nuxt-link
                :to = "`/stock/${stock._id}`">
                <Tooltip
                  v-if = "stock.quick_add"
                  content = "Added on the fly while editing a job">
                  <span class = "knocksapp-fly3 coc-padding-5px coc-content-bg coc-warning-text coc-circle-border-radius"/>
                </Tooltip>
                <Tooltip
                  v-else
                  content = "Healthy added from Add Stock Form">
                  <span class = "knocksapp-live3 coc-padding-5px coc-content-bg coc-success-text coc-circle-border-radius"/>
                </Tooltip>
                {{ stock.name }}
              </nuxt-link>
            </td>
            <td class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px coc-padding-x-4px">{{ stock.category }}</td>
            <td class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px coc-padding-x-4px">{{ stock.price }}</td>
            <td class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px coc-padding-x-4px">{{ stock.count }}</td>
            <td 
              class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px coc-padding-x-4px" 
              style=" width: 300px;">
              <coc-collapse
                v-if = "stock.car_compatibility && stock.car_compatibility.length"
                :title = "`(${stock.car_compatibility.length}) car/s are compatible`"
                :animations = "{'enter':'animated fadeIn','leave':'animated slideOutUp hidden'}"
                icon = "tcsc-motor-1-icon coc-text-lg-1"
                toggler-class = "row house-keeper pointer transparent coc-border-0"
                regular-class = "coc-text-body coc-primary-text"
                active-class = "coc-text-md-1 coc-primary-text">
                <CellGroup slot = "content">
                  <Cell 
                    v-for = "(car, c) in stock.car_compatibility" 
                    :key = "c" 
                    :label="car.model" 
                    :extra = "car.release"
                    class = "animated slideInLeft">
                    <div class = "row coc-house-keeper">
                      <coc-avatar
                        :source = "`/snaps/brands/png/${car.brand.split(' ').join('-').toLowerCase()}.png`"
                        scale = "25px"
                        class = "col coc-padding-x-2px"/>
                      <span class="col coc-margin-x-0px">
                        {{ car.brand | CocCapitalizeName }}
                      </span>
                    </div>
                  </Cell>
                </CellGroup>
              </coc-collapse>
              <i v-else><small>NONE</small></i>
            </td>
            <td
              :style = "`text-align: ${ stock.vendor ? 'left' : 'center'} !important`"
              class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px coc-padding-x-4px">
              <p
                v-if = "stock.vendor"
                class = "coc-padding-left-5px">
                <Icon type = "ios-person" /> {{ stock.vendor.name }} <br>
                <a :href="`tel:${stock.vendor.phone}`"><Icon type = "ios-call" /> {{ stock.vendor.phone }}</a> <br>
                <Icon type = "ios-navigate" /> {{ stock.vendor.address }} <br>
              </p>
              <i v-else ><small>NONE</small></i>
            </td>
            <td
              class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px center coc-padding-x-7px">
              <Tooltip content = "Export">
                <Button
                  icon = "md-arrow-down coc-error-text"
                  class = "coc-border-0"
                  @click = "importStock(stock)" />
              </Tooltip>
              <Tooltip content = "Import">
                <Button
                  icon = "md-arrow-up coc-success-text"
                  class = "coc-border-0"
                  @click = "importStock(stock, -1)" />
              </Tooltip>
              <Tooltip content = "Edit">
                <Button
                  icon = " knocks-pen-angled coc-warning-text"
                  class = "coc-border-0"
                  @click = "editStock(stock)" />
              </Tooltip>
              <div class="coc-full-width coc-margin-top-5px">
                <Tooltip content = "Count">
                  <input-number
                    v-model = "moveCount"
                    :min = "1"
                    class = "left"/>
                </Tooltip>
              </div>
            </td>
          </tr>
          <tr v-if = "stock.stock && stock.stock.length">
            <td />
            <td />
            <td class="center coc-text-bold">Total {{ $_.sumBy(stock.stock, o => o.price) }} LE</td>
            <td />
          </tr>
        </table>
        <div 
          v-if = "pagination && stock && stock.stock.length && input.page >= 0"
          style = " padding-top: 10px !important; margin-top: 5px; margin-bottom: 10px;" 
          class="col s12 ">
          <Page
            :total="pagination.pages"
            :page-size = "1"
            :current="pagination.page + 1"
            :styles = "{margin: 'auto', display: 'block', width: 'fit-content'}"
            @on-change = "changePage"/>
        </div>
      </div>
      <div
        v-if = "stock && stock.stock && !stock.stock.length && !isLoading && !hasError"
        class = "coc-text-title center">
        <span class="tcsc-oil-icon"/>
        No Stock Available
        <div class="row">
          <Button
            size = "large"
            icon = " tcsc-car-battery-1-icon coc-text-md-2"
            @click = "addStock">Add New</Button>
        </div>
      </div>  
      <div
        v-coc-loading = "true"
        v-if = "isLoading && !hasError"
        style = "height: 40vh"
        class = "row" />
      <div
        v-if = "hasError"
        class = "coc-text-title center coc-error-text">
        <icon type="ios-alert-outline"/>
        Whoops!<br>
        <small class = "coc-text-xs">Something Went Wrong!<br>Try to refresh</small>
        <div class="row">
          <Button
            size = "large"
            type = "error"
            icon = "ios-refresh"
            ghost
            @click = "getStock">Refresh</Button>
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
import brands from '~/plugins/brands'
import Master from '~/components/common/master'
export default {
  name: 'Index',
  components: {
    Master
  },
  data() {
    return {
      brands,
      isMounted: false,
      isLoading: true,
      hasError: false,
      moveCount: 1,
      stock: { stock: [] },
      columns: [
        {
          title: 'Part',
          key: 'name'
        },
        {
          title: 'Category',
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
          title: 'Car Compitiblity',
          key: 'car_compatibility'
        },
        {
          title: 'Vendor',
          key: 'vendor'
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
        limit: 8
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
    this.$root.$on('addStockSuccess', this.formatQuery)
  },
  methods: {
    addStock() {
      this.$root.$emit('addStock')
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
    getStock() {
      this.isLoading = true
      this.$router.push({
        path: '/stock',
        query: { ...this.formattedQuery }
      })
      this.$axios({
        method: 'get',
        url: '/stock',
        params: { ...this.formattedQuery }
      })
        .then(res => {
          this.stock = res.data
          this.config.drawer = false
          this.isLoading = false
          this.pagination = this.$_.omit(res.data, ['stock'])
          this.hasError = false
        })
        .catch(() => {
          this.isLoading = false
          this.hasError = true
        })
    },
    decodedQuery(query) {
      const final = this.$_.cloneDeep(query)
      if (final.external !== undefined) {
        final.external = final.external === 'yes'
      }
      if (final.ignore_external !== undefined) {
        final.ignore_external = final.ignore_external === 'yes'
      }
      if (final.page !== undefined) {
        final.page = parseInt(final.page, 10) + 1
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
      // console.log(final)
      // window.dq = this.decodedQuery
      // window.qq = query
      // window.res = final
      return final
    },
    encodedQuery(input = this.input) {
      const final = this.$_.cloneDeep(input)
      if (final.external !== undefined) {
        final.external = final.external ? 'yes' : 'no'
      }
      if (final.ignore_external !== undefined) {
        final.ignore_external = final.ignore_external ? 'yes' : 'no'
      }
      if (final.page !== undefined) {
        final.page = parseInt(final.page, 10)
        if (final.page > 0) final.page = final.page - 1
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
      this.getStock()
    },
    importStock(stock, factor = 1) {
      // some code
      this.isLoading = true
      this.$axios({
        method: 'post',
        url: `/stock/${stock._id}/move`,
        data: {
          item: stock,
          count: this.moveCount * factor
        }
      })
        .then(() => {
          this.formatQuery()
          this.isLoading = false
          this.$Message.success('Done')
        })
        .catch(() => {
          this.isLoading = false
          this.$Message.error('Stock Not Found')
        })
    },
    editStock(stock) {
      this.$root.$emit('addStock', {
        mode: 'put',
        init: stock
      })
    },
    changePage(e) {
      this.input.page = e
      this.formatQuery()
    }
  }
}
</script>

<style lang="css" scoped>
</style>

<template>
  <section id='address' class="w1200">
    <h3>{{$t('global.token')}} <span>{{info.name}}</span></h3>
    <div class="overview">
      <div class="box">
        <p class="title">{{$t('global.overview')}} <span>[VRC-20]</span></p>
        <ul class="flexBox">
          <!-- <li>
            <p>{{$t('global.balance')}}:</p>
            <div>{{balance | fixNum(6)}}</div>
          </li> -->
          <li>
            <p>{{$t('table.supply')}}:</p>
            <div class="fwb">
              <span class="hashLink">{{info.total}}</span>
              {{info.name}}
            </div>
          </li>
          <li>
            <p>{{$t('table.holder')}}:</p>
            <div>{{info.holders}}</div>
          </li>
          <li>
            <p>{{$t('table.transfer')}}:</p>
            <div>{{info.transfers}}</div>
          </li>
        </ul>
      </div>
      <div class="box">
        <p class="title">{{$t('table.moreInfo')}}</p>
        <ul class="flexBox">
          <li>
            <p>{{$t("global.contract")}}:</p>
            <div>{{info.contract}}</div>
          </li>
          <li>
            <p>{{$t('table.decimals')}}:</p>
            <div>{{info.decimal}}</div>
          </li>
          <li>
            <p>{{$t('table.site')}}:</p>
            <div>{{info.site}}</div>
          </li>
        </ul>
      </div>
    </div>
    <div class="tab box">
      <ul class="hd clearfix">
        <li v-for="(item, index) in contents" :key="item.head" @click="headIndex=index" :class="{'cur': headIndex === index}" v-t="`table.${item.head}`"></li>
      </ul>
      <div class="bd" v-if="headIndex === 0">
        <div class="table_desc">
          <p>{{$t('desc.totalTxn', {total: contents[headIndex].total})}}</p>
          <page :total='contents[headIndex].size' :index='contents[headIndex].index' :url='`/token/${id}`' class="mobile"></page>        
        </div>
        <div class="wrap">
          <div class="loading" v-if="contents[headIndex].data.length===0 && !contents[headIndex].noRecord">
            <img src="~@/assets/img/loading.gif">
          </div>
          <div class="noRecord" v-else-if="contents[headIndex].noRecord">
            <p>{{$t('global.noData')}}</p>
          </div>
          <table cellpadding="0" v-else>
            <thead>
              <tr>
                <th>{{$t("table.transfer")}}</th>
                <th>{{$t("table.age")}}</th>
                <th>{{$t("global.from")}}</th>
                <th>{{$t("global.to")}}</th>
                <th>{{$t("table.quantity")}}</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in contents[headIndex].data" :key='item.hash'>
                <td>
                  <i class="fa fa-exclamation-circle warn" v-if="item.status==='0'"></i>
                  <router-link :to="`/txn/${item.hash}`" class="hashLink">{{item.hash}}</router-link>
                </td>
                <td>
                  <span class="time">{{item.t | formatAgo}}{{$t("global.ago")}}</span>
                </td>
                <td>
                  <router-link :to="`/tkAddress/${id}?a=${item.from}`" class="hashLink to">{{item.from}}</router-link>
                  <div class="goto fr"><i class="fa fa-arrow-right"></i></div>
                </td>
                <td>
                  <router-link :to="`/tkAddress/${id}?a=${item.to}`" class="hashLink to">{{item.to}}</router-link>
                </td>
                <td>
                  <span>{{item.quantity}}</span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
      <div class="bd" v-if="headIndex === 1">
        <div class="table_desc">
          <p>{{$t('desc.listHolder', {total: contents[headIndex].total})}}</p>
          <page :total='contents[headIndex].size' :index='contents[headIndex].index' :url='`/token/${id}`' class="mobile"></page>        
        </div>
        <div class="wrap">
          <div class="loading" v-if="contents[headIndex].data.length===0 && !contents[headIndex].noRecord">
            <img src="~@/assets/img/loading.gif">
          </div>
          <div class="noRecord" v-else-if="contents[headIndex].noRecord">
            <p>{{$t('global.noData')}}</p>
          </div>
          <table cellpadding="0" v-else>
            <thead>
              <tr>
                <th>{{$t("global.rank")}}</th>
                <th>{{$t("table.address")}}</th>
                <th>{{$t("table.quantity")}}</th>
                <th>{{$t("table.percent")}}</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, index) in contents[headIndex].data" :key='item.hash'>
                <td>
                  <span class="time">{{index+1}}</span>
                </td>
                <td>
                  <i class="fa fa-exclamation-circle warn" v-if="item.status==='0'"></i>
                  <router-link to="">{{item.address}}</router-link>
                </td>
                <td>
                  <span>{{item.asset}}</span>
                </td>
                <td>
                  {{item.percentage | fixNum(3)}}%
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'myAddress',
  data () {
    return {
      headIndex: 0,
      contents: [
        {
          head: 'transfer',
          data: [],
          url: "/api/vns/contract/transfersList",
          index: 1,
          total: 1,
          size: 1,
          noRecord: false
        },
        {
          head: 'holder',
          data: [],
          url: "/api/vns/contract/holderList",
          index: 1,
          total: 1,
          size: 1,
          noRecord: false
        }
      ],
      id: '',
      transfers: [],
      holders: [],
      info: {},
      balance: 0,
      currency: '',
      noRecord: false
    }
  },
  created () {
    this.id = this.$route.params.id
    this.getInfo()
    this.getTokens(this.contents[0])
    this.getCurrency()
  },
  methods: {
    getInfo () {
      this.$http({
        method: 'post',
        url: `/api/vns/contract/info`,
        dataType: 'json',
        data: {
          contract: this.id
        }
      }).then((res) => {
        var result = res.data.result
        this.info = result.info
      })
    },
    getTokens (obj) {
      this.$http({
        method: 'post',
        url: obj.url,
        dataType: 'json',
        data: {
          page: obj.index,
          limit: 50,
          contract: this.id
        }
      }).then((res) => {
        var result = res.data.result
        obj.data = result[obj.head+'s']
        obj.total = result.total
        obj.size = result.size
        if (!obj.data || (obj.data && obj.data.length === 0)) {
          obj.noRecord = true
        }
      })
    },
    getCurrency () {
      this.$http({
        method: 'post',
        url: 'http://47.244.61.227:3003/home/currencies',
        dataType: 'json',
        data: {
          currency: 'vns'
        }
      }).then((res) => {
        // console.log(res)
        this.currency = res.data.details
      })
    }
  },
  watch: {
    '$route' (val) {
      if (val.params.id !== this.id) {
        this.id = val.params.id
        this.contents[0].index = 1
        this.getInfo()
        this.getTokens()
      } else if (val.query.page && val.query.page !== this.index) {
        this.contents[this.headIndex].index = val.query.page
        this.getTokens(this.contents[this.headIndex])
      }
    },
    headIndex (val, oldVal) {
      if (val !== oldVal) {
        if (this.contents[val].data.length === 0) {
          this.getTokens(this.contents[val])
        }
      }
    }
  }
}
</script>

<style lang="scss" scoped>
  h3 {
    height: 52px;
    line-height: 52px;
    font-size: 20px;
    margin-top: 4px;
    span {
      display: inline-block;
      color: #77838f;
      font-size: 16px;
    }
  }
  .overview {
    display: flex;
    margin-bottom: 20px;
    .box {
      flex: 1;
      &:first-child {
        margin-right: 20px;
      }
      ul {
        padding: 0 10px;
      }
      li {
        display: flex;
        height: 42px;
        line-height: 42px;
        border-bottom: 1px solid #e7eaf3;
        p {
          flex: 1;
        }
        div {
          flex: 2;
        }
      }
    }
  }
  .hd {
    border-bottom: 1px solid #e7eaf3;
    li {
      padding: 0 10px;
      height: 44px;
      line-height: 44px;
      float: left;
      margin-right: 8px;
      cursor: pointer;
      font-weight: bold;
      &.cur {
        border-bottom: 2px solid #3498db;
        color: #3498db;
        margin-bottom: -1px;
      }
      &:hover {
        color: #3498db;
      }
    }
  }
  .bd {
    padding: 10px 10px;   
  }
  table {
    width: 100%;
    th {
      color: #6c757e;
      background-color: #f8fafd;
      border-bottom: 2px solid #e7eaf3;
      border-top: 1px solid #e7eaf3;
    }
    td {
      border-bottom: 1px solid #e7eaf3;
      padding: 0 10px;
      span, a {
        display: inline-block;
        height: 42px;
        line-height: 42px;
      }
      .warn {
        color: red;
        font-size: 16px;
        margin-right: 5px;
        vertical-align: -2px;
      }
      .goto {
        width: 24px;
        height: 24px;
        margin-top: 9px;
        border-radius: 12px;
        color: #00c9a7;
        background: rgba(0,201,167,.1);
        text-align: center;
        line-height: 24px;
        i {
          display: inline-block;
        }
      }
    }
  }
  .wrap {
    min-height: 200px;
    position: relative;
  }

  @media (max-width: 1200px) {
    .wrap {
      // min-width: 800px;
      width: 100%;
      overflow-x: scroll;
    }
    table {
      .hashLink {
        max-width: 120px;
      }
    }
  }

  @media (max-width: 992px) {
    table {
      min-width: 860px;
      .hashLink {
        max-width: 90px;
      }
    }
  }

  @media (max-width: 768px) {
    .overview {
      .box {
        li {
          align-items: center;
          p {
            flex: 2;
          }
          div {
            flex: 3;
            line-height: 20px;
          }
        }
      }
    }
  }
  @media (max-width: 600px) {
    h3 {
      p {
        font-size: 12px;
        max-width: 240px;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
        display: inline-block;
        vertical-align: middle;
      }
    }
    .overview {
      display: block;
      .box {
        width: 100%;
        display: block;
        margin-bottom: 20px;
      }
    }
    .table_desc {
      display: block;
      p {
        display: block;
        width: 100%;
        margin-bottom: 10px;
      }
      .mobile {
        float: right;
        margin-bottom: 15px;
      }
    }
  }
</style>

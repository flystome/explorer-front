<template>
  <div id="home" class="w1200">
    <h3>{{$t('nav.ecology')}}</h3>
    <section id="lists" class="clearfix">
      <div class="box">
        <h3>{{$t('rank.exchange')}}</h3>
        <div class="scrollBox">
          <!-- <div class="loading" v-if="assets.length===0 && !noRecord1">
            <img src="~@/assets/img/loading.gif">
          </div> -->
          <div class="noRecord" v-if="exchanges.length === 0">
            <p>{{$t('global.noData')}}</p>
          </div>
          <ul class="lists" v-else>
            <li>
              <div class="rank">{{$t("global.name")}}</div>
              <div class="address">{{$t("table.site")}}</div>
            </li>
            <li v-for="item in exchanges" :key='item.number'>
              <div class="rank">{{item.name}}</div>
              <div class="address">
                <a class="hashLink" :href="`${item.website}`" target="_blank">{{ item.website }}</a>
              </div>
            </li>
          </ul>
        </div>
      </div>
      <div class="box">
        <h3>{{$t('rank.minerPool')}}</h3>
        <div class="scrollBox">
          <!-- <div class="loading" v-if="miners.length===0 && !noRecord2">
            <img src="~@/assets/img/loading.gif">
          </div> -->
          <div class="noRecord" v-if="pools.length === 0">
            <p>{{$t('global.noData')}}</p>
          </div>
          <ul class="lists" v-else>
            <li>
              <div class="rank">{{$t("global.name")}}</div>
              <div class="address">{{$t("table.site")}}</div>
            </li>
            <li v-for="item in pools" :key='item.number'>
              <div class="rank">{{item.name}}</div>
              <div class="address">
                <a class="hashLink" :href="`${item.website}`" target="_blank">{{ item.website }}</a>
              </div>
            </li>
          </ul>
        </div>
      </div>
      <div class="box">
        <h3>{{$t('rank.wallet')}}</h3>
        <div class="scrollBox">
          <!-- <div class="loading" v-if="miners.length===0 && !noRecord2">
            <img src="~@/assets/img/loading.gif">
          </div> -->
          <div class="noRecord" v-if="websites.length === 0">
            <p>{{$t('global.noData')}}</p>
          </div>
          <ul class="lists" v-else>
            <li>
              <div class="rank">{{$t("global.name")}}</div>
              <div class="address">{{$t("table.site")}}</div>
            </li>
            <li v-for="item in websites" :key='item.number'>
              <div class="rank">{{item.name}}</div>
              <div class="address">
                <a class="hashLink" :href="`${item.website}`" target="_blank">{{ item.website }}</a>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: 'home',
  data () {
    return {
      exchanges: [
        {
          name: "CEX",
          website: 'https://cex.plus/'
        },
        {
          name: "币次元",
          website: 'https://www.coinx.pro/'
        }
      ],
      pools: [
        {
          name: "矿池1",
          website: "http://118.24.188.205/#/miners"
        },
        {
          name: "矿池2",
          website: "http://www.vnspool.org/#/miners"
        },
        {
          name: "矿池3",
          website: "http://202.182.107.191:81/#/miners"
        }
      ],
      websites: [
        {
          name: "Qpay",
          website: 'http://www.pgyer.com/qpayapp'
        },
        {
          name: "Cointome",
          website: 'http://www.cointome.org/'
        }
      ]
    }
  },
  created () {
    // this.init()
  },
  methods: {
    init () {
      this.getAssets()
      this.getMiners()
    },
    getAssets () {
      this.$http({
        method: 'post',
        url: '/api/vns/stat/rankAsset',
        dataType: 'json',
        data: {
          'page': 1,
          'limit': 10
        }
      }).then(res => {
        this.assets = res.data.result.ranks
        if (this.assets.length === 0) {
          this.noRecord1 = true
        }
      })
    },
    getMiners () {
      this.$http({
        method: 'post',
        url: '/api/vns/stat/rankMiner',
        dataType: 'json',
        data: {
          'page': 1,
          'limit': 10
        }
      }).then(res => {
        this.miners = res.data.result.ranks
        if (this.miners.length === 0) {
          this.noRecord2 = true
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>
h3 {
  height: 40px;
  line-height: 40px;
  font-size: 20px;
  margin-top: 4px;
}
#lists {
  font-size: 13px;
  display: flex;
  justify-content: space-between;
  margin-left: -16px;
  &>div {
    flex: 1;
    margin-left: 16px;
    h3 {
      padding: 0 10px;
      border-bottom: 1px solid #e7eaf3;
      font-size: 14px;
      color: #4a4f55;
      font-weight: bold;
    }
    .scrollBox {
      height: 440px;
      width: calc(100% - 20px);
      background: none;
      overflow: auto;
      margin-right: 0;
      padding-right: 0;
      margin: 0 10px;
      box-sizing: border-box;
      position: relative;
    }
    
  }
  .lists {
    width: 100%;
    height: 400px;
    .hashLink {
      max-width: 200px;
    }
    li {
      padding: 12px 0;
      height: 40px;
      border-bottom: 1px solid #e7eaf3;
      display: flex;
      div {
        flex: 2;
        &:first-child {
          flex: 1;
        }
        &.assets {
          text-align: right;
          color: #4e4e4e;
          span {
            color: #3498db;
          }
        }
      }
      .rank1 {
        color: #fecf01;
      }
      .rank2 {
        color: #c3d1e6;
      }
      .rank3 {
        color: #ecc7b1;
      }
    }
  }
  .all {
    height: 54px;
    padding: 12px 10px;
    a {
      display: block;
      height: 30px;
      line-height: 30px;
      width: 100%;
      text-align: center;
      color: #3498db;
      background: rgba(52,152,219,.1);
      font-size: 12px;
      &:hover {
        color: #fff;
        background: #3498db;
        box-shadow: 0 4px 11px rgba(52,152,219,.35);
      }
    }
  }
}
@media (max-width: 1000px) {
  #lists {
    flex-wrap: wrap;
    &>div {
      flex-basis: 46%;
      margin-bottom: 40px;
      .scrollBox {
        height: auto;
        ul {
          height: auto;
          padding-bottom: 10px;
        }
      }
      .lists {
        .hashLink {
          max-width: 300px;
        }
      }
    }
  }
}
@media (max-width: 720px) {
  #lists {
    &>div {
      flex-basis: 100%;
    }
  }
}
@media (max-width: 480px) {
  #lists {
    &>div {
      .lists {
        .hashLink {
          max-width: 190px;
        }
      }
    }
  }
}
</style>

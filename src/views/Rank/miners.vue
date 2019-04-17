<template>
  <section id="assets" class="w1200">
    <h3>{{$t('rank.miners')}}</h3>
    <div class="box">
      <div class="table_desc">
        <!-- <p>{{$t('global.block')}} #{{blocks[blocks.length - 1] && blocks[blocks.length - 1].number | formatNumber}} {{$t("global.to")}} #{{blocks[0] && blocks[0].number | formatNumber}} ({{$t('desc.totalBlock', {total: total})}})</p> -->
        <page :total='size' :index='curIndex' url='/rank/miners' class="mobile fr"></page>
      </div>
      <div class="wrap">
        <div class="loading" v-if="miners.length===0 && !noRecord">
          <img src="~@/assets/img/loading.gif">
        </div>
        <div class="noRecord" v-else-if="noRecord">
          <p>{{$t('global.noData')}}</p>
        </div>
        <table cellpadding="0" v-else>
          <thead>
            <tr>
              <th>{{$t("global.rank")}}</th>
              <th>{{$t("table.address")}}</th>
              <th>{{$t("block.reward")}}</th>
              <th>{{$t("block.uncleReward")}}</th>
              <th>{{$t("block.totalReward")}}</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in miners" :key='index'>
              <td>
                <span>{{ item.rank }}</span>
              </td>
              <td>
                <router-link :to="`/address/${item.address}`">{{item.address}}</router-link>
              </td>
              <td>
                <span>{{item.blockReward}} Vns</span>
              </td>
              <td>
                <span>{{item.uncleReward}} Vns</span>
              </td>
              <td>
                <span>{{item.totalReward}} Vns</span>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'assets',
  data () {
    return {
      miners: [],
      total: 0,
      size: 0,
      curIndex: 1,
      noRecord: false
    }
  },
  created () {
    this.getData()
  },
  methods: {
    getData () {
      this.$http({
        method: 'post',
        url: '/api/vns/stat/rankMiner',
        dataType: 'json',
        data: {
          'page': this.curIndex,
          'limit': 50
        }
      }).then(res => {
        var result = res.data.result
        this.miners = result.ranks
        this.total = result.total
        this.size = result.size
        if (this.miners.length === 0) {
          this.noRecord = true
        }
      })
    }
  },
  watch: {
    '$route' (val) {
      if (val.query.page !== this.curIndex) {
        this.curIndex = val.query.page
        this.getData()
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
  }
  .box {
    padding: 10px;
  }
  .table_desc {
    display: block;
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
    }
  }
  .perBg {
    width: 100%;
    height: 2px;
    background-color: #e7eaf3;
    margin-top: 4%;
    .per {
      height: 2px;
      background: #3498db;
    }
  }
  .dbLine {
    height: 13px;
    font-size: 13px;
  }
  .wrap {
    min-height: 200px;
    position: relative;
  }
  @media (max-width: 1200px) {
    .wrap {
      width: 100%;
      overflow-x: scroll;
    }
    table {
      .hashM {
        max-width: 120px;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
        display: inline-block;
        vertical-align: middle;
      }
    }
  }
  @media (max-width: 992px) {
    table {
      min-width: 860px;
    }
  }
  @media (max-width: 600px) {
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

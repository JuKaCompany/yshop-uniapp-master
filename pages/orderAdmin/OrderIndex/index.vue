<template>
  <view class="my-promotion">
    <view class="header">
      <view class="name acea-row row-center-wrapper">
        <view>当前佣金</view>
        <view class="record" @click="goCashRecord()">
          提现记录
          <text class="iconfont icon-xiangyou"></text>
        </view>
      </view>
      <view class="num">{{ Info.commissionCount }}</view>
      <view class="profit acea-row row-between-wrapper">
        <view class="item">
          <view>累积已提</view>
          <view class="money">{{ Info.extractCount }}</view>
        </view>
      </view>
    </view>
    <view class="bnt bg-color-red" @click="toCash">立即提现</view>
    <view class="list acea-row row-between-wrapper">
      <view class="item acea-row row-center-wrapper row-column" @click="goPoster()">
        <text class="iconfont icon-erweima"></text>
        <view>ProductMa</view>
      </view>
      <view class="item acea-row row-center-wrapper row-column" @click="goPromoterOrder()">
        <text class="iconfont icon-dingdan"></text>
        <view>CategoMa</view>
      </view>
    </view>
  </view>
</template>
<script>
import { getSpreadInfo } from "@/api/user";
export default {
  name: "UserPromotion",
  components: {},
  props: {},
  data: function() {
    return {
      Info: {
        lastDayCount: 0,
        extractCount: 0,
        commissionCount: 0
      }
    };
  },
  mounted: function() {
    this.getInfo();
    this.getList();
  },
  methods: {
    goPoster() {
       this.$yrouter.push("/pages/user/promotion/Poster/index");
    },
    goCashRecord() {
      this.$yrouter.push("/pages/user/promotion/CashRecord/index");
    },
    goPromoterOrder() {
      this.$yrouter.push("/pages/user/promotion/PromoterOrder/index");
    },
    toCash: function() {
      this.$yrouter.push({ path: "/pages/user/promotion/UserCash/index" });
    },
    getInfo: function() {
      let that = this;
      getSpreadInfo().then(
              res => {
                that.Info = res.data;
              },
              function(err) {
                uni.showToast({
                  title: err.msg || err.response.data.msg|| err.response.data.message,
                  icon: 'none',
                  duration: 2000
                });
              }
      );
    },
    goStatistics(query) {
      this.$yrouter.push({
        path: "/pages/orderAdmin/Statistics/index",
        query
      });
    },
    goAdminOrderList(types) {
      this.$yrouter.push({
        path: "/pages/orderAdmin/AdminOrderList/index",
        query: { types }
      });
    },
    getList: function() {
      var that = this;
      if (that.loading || that.loaded) return;
      that.loading = true;

    }
  }
};
</script>

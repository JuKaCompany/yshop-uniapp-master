<template>
  <view class="my-account">
    <view class="wrapper">
      <view class="header">
        <view class="headerCon">
          <view class="account acea-row row-top row-between">
            <view class="assets">
              <view>总资产(元)</view>
              <view class="money">{{ now_money }}</view>
            </view>
          </view>
          <view class="cumulative acea-row row-top">
            <view class="item">
              <view>累计消费(元)</view>
              <view class="money">{{ orderStatusSum }}</view>
            </view>
          </view>
        </view>
      </view>
      <view class="nav acea-row row-middle">
        <view class="item" @click="goUserBill(0)">
          <view class="pictrue">
            <image src="@/static/images/record1.png" />
          </view>
          <view>账单记录</view>
        </view>
        <view class="item" @click="goUserBill(1)">
          <view class="pictrue">
            <image src="@/static/images/record2.png" />
          </view>
          <view>消费记录</view>
        </view>
      </view>
      <view class="advert acea-row row-between-wrapper"></view>
    </view>
    <Recommend></Recommend>
  </view>
</template>
<script>
import Recommend from "@/components/Recommend";
import { getActivityStatus, getBalance } from "@/api/user";
export default {
  name: "UserAccount",
  components: {
    Recommend
  },
  props: {},
  data: function() {
    return {
      now_money: 0,
      orderStatusSum: 0,
      recharge: 0,
      activity: {
        is_bargin: false,
        is_pink: false,
        is_seckill: false
      }
    };
  },
  mounted: function() {
    this.getIndex();
    this.getActivity();
  },
  methods: {
    goUserBill(types) {
      this.$yrouter.push({ path: "/pages/user/UserBill/index", query: { types } });
    },
    getIndex: function() {
      let that = this;
      getBalance().then(
        res => {
          that.now_money = res.data.now_money;
          that.orderStatusSum = res.data.orderStatusSum;
          that.recharge = res.data.recharge;
        },
        err => {
          uni.showToast({
				title: err.msg || err.response.data.msg|| err.response.data.message,
				icon: 'none',
				duration: 2000
			});
        }
      );
    },
    getActivity: function() {
      let that = this;
      getActivityStatus().then(
        res => {
          that.activity.is_bargin = res.data.is_bargin;
          that.activity.is_pink = res.data.is_pink;
          that.activity.is_seckill = res.data.is_seckill;
        },
        err => {
          uni.showToast({
				title: err.msg || err.response.data.msg|| err.response.data.message,
				icon: 'none',
				duration: 2000
			});
        }
      );
    }
  }
};
</script>

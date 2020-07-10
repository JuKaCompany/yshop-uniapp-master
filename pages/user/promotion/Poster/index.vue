<template>
  <view
    class="address-management"
    :class="productList.length < 1 && page > 1 ? 'on' : ''"
    ref="container"
  >
    <view class="line" v-if="productList.length > 0">
      <image src="@/static/images/line.jpg" />
    </view>
	
	 <view class="item" v-for="(item, productListIndex) in productList" :key="productListIndex">
	      <view class="address">
	        <view class="consignee">
	          商品名称：{{ item.storeName }}
	        </view>
			<view class="img-box">
				<image  class="imagesize" :src="item.image" mode="widthFix"/>
			</view>
			<view class="money font-color-red">
				<text>￥{{ item.price }}</text>
			</view>
	      </view>
	      <view class="operation acea-row row-between-wrapper">
	        <view class="acea-row row-middle">
	          <view @click="editAddress(productListIndex)">
	            <text class="iconfont icon-bianji"></text>查看或编辑
	          </view>
	          <view @click="delAddress(productListIndex)">
	            <text class="iconfont icon-shanchu"></text>删除
	          </view>
	        </view>
	      </view>
	</view>
    <Loading :loaded="loadend" :loading="loading"></Loading>
	<view class="noCommodity" v-if="productList.length < 1 && page > 1">
	  <view class="noPictrue">
	    <image src="@/static/images/noAddress.png" class="image" />
	  </view>
	</view>
    <view style="height:100rpx;"></view>
    <view class="footer acea-row row-between-wrapper">
      <view class="addressBnt on bg-color-red" @click="addProduct">
        <text class="iconfont icon-tianjiadizhi"></text>添加商品
      </view>
    </view>
  </view>
</template>
<style scoped lang="less">
.address-management.on {
  background-color: #fff;
  height: 100vh;
}

.imagesize {
	width: 440upx;
	height: 340upx;
}
</style>
<script type="text/babel">
import {
  getProductList,
  getAddressRemove,
  getProductDefaultSet,
  postProduct
} from "@/api/user";
import Loading from "@/components/Loading";

export default {
  components: {
    Loading
  },
  data() {
    return {
      page: 1,
      limit: 20,
      productList: [],
      loadTitle: "",
      loading: false,
      loadend: false
    };
  },
  mounted: function() {
    this.ProductList();
  },
  onReachBottom() {
    !this.loading && this.ProductList();
  },
  onShow: function() {
    this.refresh();
  },
  methods: {
    refresh: function() {
      this.productList = [];
      this.page = 1;
      this.loadend = false;
      this.ProductList();
    },
    /**
     * 获取列表
     *
     */
    ProductList: function() {
      let that = this;
      if (that.loading) return; //阻止下次请求（false可以进行请求）；
      if (that.loadend) return; //阻止结束当前请求（false可以进行请求）；
      that.loading = true;
      getProductList({ page: that.page, limit: that.limit }).then(res => {
        that.loading = false;
        //apply();js将一个数组插入另一个数组;
        that.productList.push.apply(that.productList, res.data);
        that.loadend = res.data.length < that.limit; //判断所有数据是否加载完成；
        that.page = that.page + 1;
      });
    },
    /**
     * 新增
     */
    addProduct: function() {
      this.$yrouter.push({
              path: "/pages/user/promotion/AddProduct/index"
            });
    },
	/**
	 * 编辑
	 */
	editAddress: function(index) {
		console.log("prodIndex============" + this.productList[index].id);
	  this.$yrouter.push({
	    path: "/pages/user/promotion/AddProduct/index",
	    query: { id: this.productList[index].id }
	  });
	},
	/**
	 * 删除
	 */
	delAddress: function(index) {
	  let that = this;
	  let product = this.productList[index];
	  let id = product.id;
	  console.log("that.id---------------->" + id);
	  getAddressRemove(id).then(function() {
	    uni.showToast({
	      title: "删除成功!",
	      icon:"success",
	      duration: 2000,
	      complete: () => {
	        that.productList.splice(index, 1);
	        that.$set(that, "productList", that.productList);
	      }
	    });
	  });
	},
    getProduct() { }
  }
};
</script>

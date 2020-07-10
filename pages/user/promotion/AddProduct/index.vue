<template>
  <view class="addAddress absolute">
    <view class="list">
      <view class="item acea-row row-between-wrapper">
        <view class="name">名称</view>
        <input type="text" placeholder="请输入名称" v-model="product.storeName" />
		<view class="name">商品价格</view>
		<input type="number" placeholder="请输入价格" v-model="product.price" />
		<view class="name">市场价</view>
		<input type="number" placeholder="请输入市场价" v-model="product.otPrice" />
		<view class="name">成本价</view>
		<input type="number" placeholder="请输入成本" v-model="product.cost" />
		<view class="name">销量</view>
		<input type="number" placeholder="请输入销量" v-model="product.sales" />
		<view class="name">库存</view>
		<input type="number" placeholder="请输入库存" v-model="product.stock" />
      </view>
	  
	  <!-- 图片上传 -->
	  		<view class='upload-image-view'>
	  			<!-- <view class='title'>上传xxxx图片</view> -->
	  			<block v-for="(image,index) in imageList" :key="index">
	  				<view class='image-view'>
	  					<image :src="image" :data-src="image" @tap="previewImage"></image>
	  					<view class='del-btn' :data-index="index" @tap='deleteImage'>
	  						<view class='baicha'></view>
	  					</view>
	  				</view>
	  			</block>
	  			<view class='add-view' v-if="imageList.length < imageLength" @tap="chooseImage">
	  				<view class="cross">
	  					<view class="transverse-line"></view>
	  					<view class="vertical-line"></view>
	  				</view>
	   
	  			</view>
	  			<!-- <view class='info'>上传证书/证件，不超过{{imageLength}}张。(非必填)</view> -->
	  		</view>
			
    </view>
    <view class="keepBnt bg-color-red" @click="submit">保存</view>
  </view>
</template>
<script type="text/babel">
import { getAddress,postAddress } from "@/api/user";
 var sourceType = [
		['camera'], //拍照
		['album'], //相册
		['camera', 'album'] //拍照或相册
	]
	var sizeType = [
		['compressed'], //压缩
		['original'], //原图
		['compressed', 'original'] //压缩或原图
	]
export default {
  components: {
  },
  data() {
    return {
      id: 0,
	  image: '',
      product: { isDel: 0,
	   price: '',
	   otPrice: '',
	   cost: '',
	   sales: '',
	   stock: '',
	   image: ''
	   },
	   imageList: [], //保存图片路径集合
	  				imageLength: 1, //限制图片张数
	  				sourceTypeIndex: 2, //添加方式限制
	  				sizeTypeIndex: 2 //图片尺寸限制 
    };
  },
  onUnload() {},
  mounted: function() {
    let id = this.$yroute.query.id;
    this.id = id;
    this.getUserAddress();
  },
  methods: {
	  //选择图片
	  			 chooseImage: async function() {
	  					uni.chooseImage({
	  						sourceType: sourceType[this.sourceTypeIndex],
	  						// #ifdef MP-WEIXIN
	  						sizeType: sizeType[this.sizeTypeIndex],
	  						// #endif
	  						count: this.imageLength - this.imageList.length,
	  						success: (res) => {
								console.log("-------path------" + res.tempFilePaths);
	  							this.imageList = this.imageList.concat(res.tempFilePaths);
									console.log("imageList------>" + this.imageList);
	  						}
	  					})
	  				},
	  				//预览图片
	  				previewImage: function(e) {
						console.log("preview=====");
	  					var current = e.target.dataset.src
	  					uni.previewImage({
	  						current: current,
	  						urls: this.imageList
	  					})
	  				},
	  				//删除图片
	  				deleteImage: function(e) {
	  					var index = e.target.dataset.index;
	  					var that = this;
	  					var images = that.imageList;
	  					images.splice(index, 1);
	  					that.imageList = images;
	  				},
    getUserAddress: function() {
		console.log("fale------------" + this.id);
      if (!this.id) return false;
	  console.log("flag-------------");
	  let that = this;
	  getAddress(that.id).then(res => {
		  that.product = res.data;
		  that.imageList[0] = res.data.image;
		  console.log(".......data.image---" + that.product.image);
		  //that.product.image = res.data.image;
	  });
    },
    async submit() {
		let image = this.imageList[0];
		console.log("======image====" + image);
	   let that = this;
	    if (!that.id) {
			console.log("======!!!that.id==*****==" + that.id);
			that.data = {
					//id: that.id,
					storeName: that.product.storeName,
					image: image,
					price: that.product.price,
					otPrice: that.product.otPrice,
					cost: that.product.cost,
					sales: that.product.sales,
					stock: that.product.stock
			};
		}else{
			console.log("======that.id==*****==" + that.id);
			that.data = {
					id: that.id,
					storeName: that.product.storeName,
					image: image,
					price: that.product.price,
					otPrice: that.product.otPrice,
					cost: that.product.cost,
					sales: that.product.sales,
					stock: that.product.stock
			};
		}
	  postAddress(that.data).then(function() {
		  console.log("=======this.id===" + that.id);
	            if (that.id) {
					console.log("0.0");
	              uni.showToast({
	                title: "修改成功",
	                icon: "none",
	                duration: 2000
	              });
	            } else {
					console.log("ok");
	              uni.showToast({
	                title: "已取消绑定",
	                icon: "none",
	                duration: 2000
	              });
	            }
	            that.$yrouter.go(-1);
	          });
    }
  }
};
</script>
<style>
/* 第一套图片上传样式（内部图标相机） */
.upload-image-view {
	width: 100%;
	margin: 20upx 0 20upx 0;
	display: flex;
	flex-wrap: wrap;
	align-items: center;
}
 
.upload-image-view .title {
	width: 100%;
	font-family: PingFang-SC-Regular;
	font-size: 24upx;
	color: #4a4a4a;
	margin-bottom: 15upx;
	line-height: 100%;
}
 
.upload-image-view .info {
	width: 100%;
	font-family: PingFang-SC-Regular;
	font-size: 24upx;
	color: #ff4259;
	height: 24upx;
	margin-top: 15upx;
	line-height: 24upx;
}
 
.upload-image-view .image-view {
	height: 130upx;
	width: 130upx;
	position: relative;
	margin: 15upx 15upx 15upx 0upx;
	border-radius: 8upx;
}
 
.upload-image-view .image-view image {
	height: 100%;
	width: 100%;
	border-radius: 8upx;
}
 
.upload-image-view .image-view .del-btn {
	background-color: #f67371;
	border-radius: 50%;
	width: 25upx;
	height: 25upx;
	position: absolute;
	top: -12upx;
	right: -12upx;
	z-index: 2;
	display: flex;
	justify-content: center;
	align-items: center;
}
 
.upload-image-view .image-view .del-btn .baicha {
	display: inline-block;
	width: 20upx;
	height: 5upx;
	background: #fff;
	line-height: 0;
	font-size: 0;
	vertical-align: middle;
	-webkit-transform: rotate(45deg);
}
 
.upload-image-view .image-view .del-btn .baicha:after {
	content: '/';
	display: block;
	width: 20upx;
	height: 5upx;
	background: #fff;
	-webkit-transform: rotate(-90deg);
}
 
.upload-image-view .add-view {
	height: 115upx;
	width: 115upx;
	margin: 15upx 15upx 15upx 0upx;
	display: flex;
	align-items: center;
	justify-content: center;
	background: rgba(255, 255, 255, 0.00);
	border: 3upx dashed #979797;
	border-radius: 8upx;
}
 
/* 相机 */
 
.upload-image-view .add-view .xiangji {
	height: 40upx;
	width: 48upx;
	display: flex;
	justify-content: center;
	flex-wrap: wrap;
}
 
.upload-image-view .add-view .xiangji .tixing {
	width: 10upx;
	height: 7upx;
	background-color: #fff;
	border-right: 10upx solid #fff;
	border-bottom: 7upx solid #b2b2b2;
	border-left: 10upx solid #fff;
}
 
.upload-image-view .add-view .xiangji .changfx {
	height: 32upx;
	width: 48upx;
	border-radius: 5%;
	background-color: #b2b2b2;
	display: flex;
	align-items: center;
	justify-content: center;
}
 
.upload-image-view .add-view .xiangji .changfx .yuan1 {
	height: 20upx;
	width: 20upx;
	border-radius: 50%;
	background-color: #fff;
	display: flex;
	align-items: center;
	justify-content: center;
}
 
.upload-image-view .add-view .xiangji .changfx .yuan2 {
	height: 10upx;
	width: 10upx;
	border-radius: 50%;
	background-color: #b2b2b2;
}
 
/* 第二套图片上传样式（内部图标 十字架）*/
 
/* 十字架 */
.upload-image-view .add-view .cross {
	height: 48upx;
	width: 48upx;
	display: flex;
	flex-wrap: wrap;
	position: relative;
}
 
.upload-image-view .add-view .cross .transverse-line {
	height: 100%;
	width: 48%;
	position: absolute;
	border-right: 3upx solid #5A5A5A;
	top: 0;
	left: 0;
}
 
.upload-image-view .add-view .cross .vertical-line {
	height: 48%;
	width: 100%;
	position: absolute;
	border-bottom: 3upx solid #5A5A5A;
	top: 0;
	left: 0;
}
</style>
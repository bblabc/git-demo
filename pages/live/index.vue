<template>
	<view>
		<view class="container">
			<view class="miaosha-container">
				<block v-for="(item, index) in aliveRooms" :key="index">
					<view class="miaosha-goods-list" @click="goLiveRoom(item)">
						<image :src="item.share_img" class="image" mode="aspectFill" lazy-load="true" />
						<view class="r">
							<view class="goods-title">{{item.name}}</view>
							<view class="label">
								<u-icon name="account" size="30rpx"></u-icon><text>{{item.anchor_name}}</text>
							</view>
							<view class="label">
								<u-icon name="clock" size="30rpx"></u-icon><text>{{item.start_time_str}}</text>
							</view>

							<view class="miaosha-price-btn">
								<u-button
									v-if="item.live_status == 107 || item.live_status == 106 || item.live_status == 104"
									type="primary" size="small" block round plain>已过期</u-button>
								<u-button v-else-if="item.live_status == 102" type="error" size="small" block round>即将开播
								</u-button>
								<u-button v-else-if="item.live_status == 103" type="warning" size="small" block round>
									直播结束</u-button>
								<u-button v-else type="success" size="small" block round>正在直播</u-button>
							</view>
						</view>
					</view>
				</block>
			</view>
		</view>
	</view>
</template>

<script>
	Date.prototype.format = function(format) {
		var date = {
			"M+": this.getMonth() + 1,
			"d+": this.getDate(),
			"h+": this.getHours(),
			"m+": this.getMinutes(),
			"s+": this.getSeconds(),
			"q+": Math.floor((this.getMonth() + 3) / 3),
			"S+": this.getMilliseconds()
		};
		if (/(y+)/i.test(format)) {
			format = format.replace(RegExp.$1, (this.getFullYear() + '').substr(4 - RegExp.$1.length));
		}
		for (var k in date) {
			if (new RegExp("(" + k + ")").test(format)) {
				format = format.replace(RegExp.$1, RegExp.$1.length == 1 ?
					date[k] : ("00" + date[k]).substr(("" + date[k]).length));
			}
		}
		return format;
	}

	export default {
		data() {
			return {
				aliveRooms: undefined
			};
		},
		onReady() {},
		onLoad(e) {
			this._wxaMpLiveRooms()
		},
		mounted() {},
		methods: {
			async _wxaMpLiveRooms() {
				uni.showLoading({
					title: ''
				})
				const res = await this.$wxapi.wxaMpLiveRooms()
				uni.hideLoading()
				if (res.code == 0) {
					res.data.forEach(ele => {
						if (ele.start_time) {
							ele.start_time_str = new Date(ele.start_time * 1000).format('yyyy-MM-dd h:m:s')
						}
					})
					this.aliveRooms = res.data
				}
			},
			goLiveRoom(item) {
				console.log(item);
				if (item.live_status == 107 || item.live_status == 106 || item.live_status == 104) {
					return
				}
				// #ifdef MP-WEIXIN
				wx.navigateTo({
					url: `plugin-private://wx2b03c6e691cd7370/pages/live-player-plugin?room_id=${item.roomId}`
				})
				// #endif
				uni.showToast({
					title: '暂时无法跳转直播间'
				})
			}
		}
	};
</script>

<style lang="scss" scoped>
	.shops-container {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin: 16rpx;
	}

	.shops-container .l {
		display: flex;
		align-items: center;
	}

	.shops-container .l text {
		color: #666;
		margin-left: 16rpx;
	}

	.shops-container .l image {
		width: 40rpx;
		height: 40rpx;
	}

	.shops-container .r {
		display: flex;
		align-items: center;
	}

	.shops-container .r text {
		color: #666;
	}

	.shops-container .r image {
		width: 40rpx;
		height: 40rpx;
	}

	.search {
		position: absolute;
		top: 32rpx;
		left: 25rpx;
		width: 700rpx;
		height: 66rpx;
		display: block;
		box-sizing: border-box;
	}

	.search input {
		display: block;
		box-sizing: border-box;
		background: rgba(255, 255, 255, 0.8);
		border: 1rpx solid #e3e3e3;
		width: 700rpx;
		height: 66rpx;
		border-radius: 30rpx;
		padding-left: 32rpx;
	}

	.search image {
		width: 35rpx;
		height: 35rpx;
		position: absolute;
		top: 16rpx;
		right: 32rpx;
		z-index: 99;
	}

	.goodsDynamic {
		position: absolute;
		top: 110rpx;
		left: 25rpx;
		width: 650rpx;
		height: 66rpx;
		line-height: 66rpx;
		background: rgba(0, 0, 0, 0.6);
		color: #fff;
		padding: 0 16rpx;
		border-radius: 32rpx;
		font-size: 24rpx;
	}

	.goodsDynamic .swiper2 {
		width: 100%;
		height: 100%;
	}

	.goodsDynamic-item {
		width: 100%;
		display: flex;
		align-items: center;
	}

	.goodsDynamic-item image {
		width: 32rpx;
		height: 32rpx;
		border-radius: 50%;
		flex-shrink: 0;
	}

	.goodsDynamic-item text {
		margin-left: 8rpx;
		text-overflow: ellipsis;
		white-space: nowrap;
		overflow: hidden;
	}

	.notice-box {
		display: flex;
		box-sizing: border-box;
		width: 100vw;
		height: 88rpx;
		line-height: 88rpx;
		justify-content: space-between;
		align-items: center;
		padding-right: 32rpx;
		border-bottom: 1rpx solid #efeff4;
	}

	.notice {
		display: flex;
		justify-content: space-between;
		align-items: center;
		width: 600rpx;
	}

	.notice_icon {
		margin-left: 20rpx;
		width: 30rpx;
		height: 30rpx;
	}

	.notice_swiper {
		height: 88rpx;
		width: 600rpx;
	}

	.notice_itemr {
		padding-left: 16rpx;
		font-size: 24rpx;
		overflow: hidden;
		color: #e64340;
	}

	.notice-box .more {
		color: #666;
		font-size: 24rpx;
	}

	.container {
		background-color: #fff;
		min-height: 100%;
		display: block;
	}

	.category-box {
		background-color: #fff;
		display: flex;
		flex-wrap: wrap;
	}

	.category-list {
		flex: 1;
		/* width:150rpx; */
		margin-top: 20rpx;
		text-align: center;
		display: inline-block;
		overflow: hidden;
	}

	.category-column {
		width: 100%;
		margin-top: 20rpx;
		overflow: hidden;
	}

	.category-imgbox {
		width: 90rpx;
		height: 90rpx;
	}

	.category-title {
		font-size: 24rpx;
		text-align: center;
		margin-top: 10rpx;
	}


	.swiper-container {
		width: 750rpx;
		height: 375rpx;
		position: relative;
	}

	.swiper-container .swiper1 {
		width: 750rpx;
		height: 375rpx;
	}

	.swiper-container .swiper1 image {
		width: 750rpx;
		height: 375rpx;
	}

	::-webkit-scrollbar {
		width: 0;
		height: 0;
		color: transparent;
	}

	.type-item-on {
		color: #e64340;
		border-bottom: 1rpx solid #e64340;
	}

	.goods-container {
		display: flex;
		justify-content: space-between;
		flex-wrap: wrap;
		box-sizing: content-box;
		padding: 24rpx;
		background-color: #F2f2f2;
	}

	.goods-box {
		width: 339rpx;
		height: 472rpx;
		background-color: #fff;
		overflow: hidden;
		margin-bottom: 24rpx;
		border-radius: 5px;
	}

	.goods-box .img-box {
		width: 339rpx;
		height: 339rpx;
		overflow: hidden;
	}

	.goods-box .img-box image {
		width: 339rpx;
		height: 339rpx;
	}

	.goods-box .goods-title {
		width: 280rpx;
		text-overflow: ellipsis;
		white-space: nowrap;
		overflow: hidden;
		font-size: 24rpx;
		padding: 24rpx 0 0rpx 0;
		color: #000;
		margin-left: 24rpx;
	}

	.goods-box .goods-price {
		width: 280rpx;
		overflow: hidden;
		font-size: 24rpx;
		padding: 24rpx 0;
		color: #e64340;
		margin-left: 24rpx;
	}

	.pos-fiexd {
		position: fixed;
		top: 0;
		left: 0;
	}

	.coupons {
		margin-top: 10rpx;
		width: 100%;
		height: 180rpx;
		overflow: hidden;
	}

	.coupons-scroll {
		white-space: nowrap;
		height: 180rpx;
		width: 100%;
		background-color: #fff;
	}

	.coupons-item {
		width: 300rpx;
		height: 180rpx;
		margin: 10rpx;
		padding-top: 20rpx;
		padding-left: 15rpx;
		background-color: #f1a83b;
		box-sizing: content-box;
		font-size: 20rpx;
		line-height: 35rpx;
		overflow: hidden;
		color: #fff;
		display: inline-block;
	}

	.coupons-float {
		position: fixed;
		right: 15rpx;
		bottom: 80rpx;
		/* width:80rpx;
  height:80rpx; */
		/* background-color: #fff; */
		text-align: center;
		/* border-radius:50%;
  border: 1rpx solid #ddd; */
	}

	.coupons-float image {
		width: 110rpx;
		height: 110rpx;
		margin-top: 10rpx;
	}

	.tuan {
		width: 750rpx;
		background-color: #F2f2f2;
		padding-top: 10rpx;
	}

	.tuan-item {
		width: 720rpx;
		margin: auto;
		margin-top: 20rpx;
		background-color: #FFF;
		position: relative;
	}

	.tuan-goods-pic {
		width: 720rpx;
		height: 250rpx;
		overflow: hidden;
	}

	.tuan-goods-pic image {
		width: 720rpx;
	}

	.tuan-title {
		margin-top: 20rpx;
		margin-left: 20rpx;
		font-size: 30rpx;
		font-weight: 400;
	}

	.tuan-profile {
		margin-left: 20rpx;
		font-size: 28rpx;
		color: #999;
	}

	.tuan-price {
		margin-left: 20rpx;
	}

	.tuan-price .now {
		font-size: 36rpx;
		color: #e64340;
	}

	.tuan-price .original {
		margin-left: 20rpx;
		font-size: 26rpx;
		color: #999;
		text-decoration: line-through;
	}

	.tuan-btn {
		position: absolute;
		right: 30rpx;
		bottom: 10rpx;
	}

	.category-goods-title {
		width: 100vw;
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 8rpx;
		border-bottom: 1rpx solid #efeff4;
	}

	.category-goods-title .more {
		color: #666;
		font-size: 24rpx;
		margin-right: 32rpx;
	}

	.progress {
		width: 686rpx;
		padding: 0 32rpx;
		margin-bottom: 16rpx;
	}

	/*秒杀*/
	.miaosha-container {
		padding: 0;
	}

	.miaosha-goods-list {
		margin: 20rpx;
		background: #f6f6f6;
		border-radius: 16rpx;
		display: flex;
		padding: 20rpx;
	}

	.miaosha-goods-list .image {
		width: 260rpx;
		height: 260rpx;
		flex-shrink: 0;
		border-radius: 16rpx;
	}

	.miaosha-goods-list .r {
		margin-left: 32rpx;
	}

	.miaosha-goods-list .r .goods-title {
		color: #333;
		font-size: 28rpx;
	}

	.miaosha-goods-list .r .label {
		color: #e64340;
		font-size: 24rpx;
		display: flex;
		align-items: flex-start;
		margin-top: 8rpx;
	}

	.miaosha-goods-list .r .label text {
		margin-left: 8rpx;
	}

	.miaosha-goods-list .count-down {
		background: #e64340;
		padding: 8rpx 16rpx;
		border-radius: 16rpx;
		margin-top: 12rpx;
	}

	.van-count-down {
		color: #fff !important;
		font-size: 26rpx !important;
		text-align: center;
	}

	.miaosha-price-btn {
		padding-top: 32rpx;
	}

	.miaosha-price-btn .price {
		color: #e64340;
		font-size: 40rpx;
		margin-top: 12rpx;
	}

	.miaosha-price-btn .price text {
		color: #666666;
		font-size: 26rpx;
		text-decoration: line-through;
	}
</style>

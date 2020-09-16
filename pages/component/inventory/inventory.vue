<template>
	<view>
	<cu-custom bgColor="bg-gradual-blue" class="customHead" :isBack="true"><block slot="backText">返回</block><block slot="content">库存查询</block></cu-custom>
	<loading :loadModal="loadModal"></loading>
	<uni-fab
	:pattern="pattern"
	:horizontal="horizontal"
	:vertical="vertical"
	:popMenu="popMenu"
	distable
	:direction="direction"
	 @fabClick="fabClick"
	 ></uni-fab>
	 <view class="box getheight">
	 	<view class="cu-bar search bg-white" style="height: 30px;">
	 		<view class="search-form round">
	 			<text class="cuIcon-search"></text>
	 		<input :adjust-position="false" type="text" :value="keyword" @input="inputChange" placeholder="搜索" confirm-type="search"></input>
	 	</view>
	 	<view class="action">
	 		<button class="cu-btn bg-green shadow-blur round" @tap="$manyCk(search)">搜索</button>						</view>
	 	</view>
	 </view>
	<scroll-view scroll-y class="page" :style="{ 'height': pageHeight + 'px' }">
		<view v-for="(item,index) in cuIconList" :key="index">
				<view class="cu-list menu-avatar">
					<view class="cu-item" style="width: 100%;margin-top: 2px;height: 260upx;" >
						<view style="clear: both;width: 100%;" class="grid text-left col-2" @tap="$manyCk(showList(index, item))" data-target="Modal" data-number="item.number">
							<view class="text-grey">序号:{{item.index=(index + 1)}}</view>
							<view class="text-grey">编码:{{item.FNumber}}</view>
							<view class="text-grey">名称:{{item.FName}}</view>
							<view class="text-grey">规格:{{item.FModel}}</view>
							<view class="text-grey">仓库:{{item.FStockName}}</view>
							<view class="text-grey">库位:{{item.FStockPlacename}}</view>
							<view class="text-grey">批号:{{item.FBatchNo}}</view>
							<!-- <view class="text-grey">数量:{{item.FQty}}</view>
							<view class="text-grey">单位:{{item.FUnitName}}</view> -->
							<view class="text-grey">标签编码:{{item.FLabelNumber}}</view>
							<view class="text-grey">基本单位:{{item.FBUUnitName}}</view>
							<view class="text-grey">基本数量:{{item.FBUQty}}</view>
							<view class="text-grey">常用单位:{{item.FCUUnitName}}</view>
							<view class="text-grey">常用数量:{{item.FCUUQty}}</view>
						</view>
					</view>
				</view>
		</view>
	</scroll-view>
	</view>
</template>

<script>
	import basic from '@/api/basic';
	export default {
		data() {
			return {
			keyword: '',
			pageHeight: 0,
			cuIconList: [],
			loadModal: false,
			onoff: true,
			horizontal: 'right',
			vertical: 'bottom',
			popMenu: false,
			direction: 'horizontal',
			pattern: {
				color: '#7A7E83',
				backgroundColor: '#fff',
				selectedColor: '#007AFF',
				buttonColor: '#007AFF'
			},
			};
		},
		onReady: function() {
				 var me = this
				 uni.getSystemInfo({
				 　　success: function(res) { // res - 各种参数
				 　　   let info = uni.createSelectorQuery().select(".getheight");
				 　　   let customHead = uni.createSelectorQuery().select(".customHead");
						 var infoHeight = 0;
						 var headHeight = 0;
				 　　　  　info.boundingClientRect(function(data) { //data - 各种参数
							infoHeight = data.height
				 　　    }).exec();
						customHead.boundingClientRect(function(data) { //data - 各种参数
							headHeight = data.height
				 　　    }).exec();
				 setTimeout(function () {
				 				me.pageHeight= res.windowHeight - infoHeight - headHeight
				 		}, 1000);
				       }
				 });
		},
		methods: {
			inputChange(e){
			    this.keyword = e.detail.value
			 },
		fabClick() {
			var that = this
			let resultA = []
			uni.scanCode({
				success:function(res){
					if(resultA.indexOf(res.result)==-1) {
						basic.inventoryByBarcode({'uuid':res.result}).then(reso => {
						//if(reso.success){
							console.log(reso)
							for(let i in reso.data) {
								that.cuIconList.push(reso.data[i])				
							}	 
						//}
						}).catch(err => {
							uni.showToast({
								icon: 'none',
								title: err.msg,
							});
						})
						resultA.push(res.result)
					}
				}
			});
		},
		// 查询条件过滤
		qFilter() {
	       let obj = {}
	       this.keyword != null && this.keyword != '' ? obj.name = this.keyword : null
			return obj
		},
		search(){
			const me = this
			if (this.keyword != null && typeof this.keyword != 'undefined') {
				basic.inventoryList(this.qFilter()).then(res => {
					if(res.success){
						me.cuIconList=res.data

					}
				}).catch(err => {
					uni.showToast({
						icon: 'none',
						title: err.msg,
					});
				})
			}else{
				uni.showToast({
					icon: 'none',
					title: '格式错误'
				});
				return;
				}
			
		},
		}
	}
</script>

<style>
	.nav-li {
		width: 60%;
		text-align: center;
		margin-left: 20%;
	}
	.page {
		height: 100vh;
	}
	.nav-list{
		margin-top: 5%;
	}
	.nav-title::first-letter {
	    font-size: 16px;
	    margin-right: 2px;
	}
</style>

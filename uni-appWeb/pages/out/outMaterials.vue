<template>
	<view class="page">
		
		<!-- <cover-view class="select">
			<sl-filter :independence="true" :color="titleColor" :themeColor="themeColor" :menuList.sync="menuList" @result="result"></sl-filter>
		</cover-view> -->
		
		<uni-nav-bar :fixed="true" background-color="#20a0ff" :border="false">
			<view class="input-view">
				<uni-icons type="search" size="22" color="#666666" />
				<input v-model="materialFilterText" confirm-type="search" class="input" type="text" placeholder="输入物料信息">
				<uni-icons :color="'#999999'" v-if="materialFilterText!==''" class="icon-clear" type="clear" size="22" @click="clear" />
			</view>
		</uni-nav-bar>
		
		<view v-for="(item,index) in materialAdd" :key="index" class="card-set">
			<uni-card class="card_style"
			    :title="item.material_name"
			    mode="basic" 
			    :is-shadow="true" 
				:extra="item.material_iden"
			>
				<view @click="test(item)">物料编码：{{ item.material_iden }}</view>
				<view>物料名称：{{ item.material_name }}</view>
				<view>规格：{{ item.material_specification }}</view>
				<view>型号：{{ item.material_model }}</view>
				<view>计量单位：{{ item.material_meterage }}</view>
				<view>存货量：{{ item.material_attr }}</view>
				<view>选择数量:  
					<view @click="_calcDecValue(item)" class="uni-numbox__minus">
						<text class="uni-numbox--text" :class="{ 'uni-numbox--disabled': item.material_num <= min || disabled }">-</text>
					</view>
						<input @blur="_onBlur(item)" :max="item.material_attr" class="uni-numbox__value" type="number" v-model = "item.material_num" @click="test(item)" />
					<view @click="_calcAddValue(item)" class="uni-numbox__plus">
						<text class="uni-numbox--text" :class="{ 'uni-numbox--disabled': item.material_num >= max || disabled }">+</text>
					</view>
				</view>
				<view>选择：<switch class="select" color='#d81e06'  @change="switchChange(item)" v-model="item.material_check" type="checkbox" /></view>
				<view>备注：<textarea class="remarks_input" maxlength="200" v-model="item.material_remarks" placeholder="请输入,限制200字" auto-height="true"></textarea></view>
			</uni-card>
		</view>
		<!-- <view>
			<drag-button
				:isDock="true"
				:existTabBar="true"
				@btnClick="newOut">
			</drag-button>
		</view> -->
		<view class="shopcart">
			<!-- @click="toggleList" -->
			<view class="cartBottom" >
				<view class="carIcon">
					<!-- <view class="iconBox" :class="getAllCount ? 'active' : '' ">
						<text class="allcount" v-if="getAllCount">{{getAllCount}}</text>
						<image src="/static/cart.png" mode="" class="img"></image>
					</view> -->
				</view>
				<view class="middle">
					<!-- <text class="price" :class="getAllCount ?　'active': ''">已选{{getAllPrice}}</text> -->
				</view>
				<view class="BtnRight">
					<button type="primary" class="button_style" @click="newOut">确定</button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import uniSegmentedControl from '../../components/uni-segmented-control/uni-segmented-control.vue'
	import uniCard from '../../components/uni-card/uni-card.vue'
	import uniSection from '../../components/uni-section/uni-section.vue'
	// import slFilter from '@/components/sl-filter/sl-filter.vue'
	import materialData from '../../data/purchaseMaterials.js'
	import dragButton from '../../components/drag-button/anotherButton.vue'
	// import uniNumberBox from '@/components/uni-number-box/uni-number-box.vue'
	import uniIcons from '@/components/uni-icons/uni-icons.vue'
	import uniNavBar from '@/components/uni-nav-bar/uni-nav-bar.vue'
	
	export default {
		components: {
			uniSegmentedControl,
			uniCard,
			uniSection,
			uniSegmentedControl,
			// slFilter,
			dragButton,
			// uniNumberBox,
			uniNavBar,
			uniIcons
		},
		data() {
			return {
				
				themeColor: '#000000',
				titleColor: '#666666',
				materialList: materialData.data,
				materialFilterText: '',
				disabled: {
					type: Boolean,
					default: false
				},
				max: {
					type: Number,
					default: 100
				},
				min: {
					type: Number,
					default: 0
				}
			}
						
		},
		onLoad() {
			
		},
		mounted(){
			
		},
		computed:{
			materialAdd(){
				var arr = []
		
				this.materialList.forEach((item) => {
					var num = 0
					var remarks = ''
					var check = 0
					var maxnum = parseInt(item.material_attr)
					item.material_attr = maxnum
					item.material_check = check 
					item.material_remarks = remarks
					item.material_num = num
					
					arr.push(item)
				})
				
				if (this.materialFilterText) {
					arr = this.materialList.filter(item => item.material_iden.includes(this.materialFilterText)||
						item.material_name.includes(this.materialFilterText)||
						item.material_specification.includes(this.materialFilterText)||
						item.material_model.includes(this.materialFilterText)||
						item.material_meterage.includes(this.materialFilterText)
					)
				}	
				
				
				return arr
			},
			
		},
		methods:{
			materialSelect(){
				var arr = []
				
				this.materialList.forEach((item) => arr.push(item))
				
				console.log(arr)
				
				arr = this.materialList.filter(item => (item.material_check == 1))
				
				console.log(arr)
				
				return arr
			},
			result(val) {
				this.filterResult = JSON.stringify(val, null, 2)
			},
			change(item,num) {
				this.item.material_num = num
			},
			switchChange(item){
				console.log(item.material_attr)
				
				console.log(item.material_check)
				item.material_check = !(item.material_check)
				console.log(item.material_check)
			},
			test(item) {
				console.log(item.material_remarks)
				console.log(item.material_num)
				console.log(typeof(item.material_num))
				console.log(item.material_check)
				console.log(this.materialSelect())
			},
			newOut(){
				let arr = []
				arr = this.materialSelect()
				
				uni.setStorageSync('out_select',arr)
				
				uni.navigateTo({
					url: './out',
				});
			},
			clear(){
				this.materialFilterText = ''
			},
			
			_onBlur(item) {
				let value = parseInt(item.material_num);
				let max = parseInt(item.material_attr);
				let min = 0;
				if (!value) {
					return;
				}
				value = +value;
				if (value > max) {
					value = max;
				} else if (value < min) {
					value = min;
				}
				
				console.log(max)
				console.log(min)
				console.log(value)
				item.material_num =	value
				this.$forceUpdate()
				console.log(item.material_num)
				
			},
			_calcDecValue(item){
				let value = parseInt(item.material_num);
				let max = parseInt(item.material_attr);
				let min = 0;
				value -= 1;
				if (value < min) {
					return;
				}
				if(value > max){
					value = max
				}
				
				item.material_num = value;
				this.$forceUpdate()
			},
			_calcAddValue(item){
				let value = parseInt(item.material_num);
				let max = parseInt(item.material_attr);
				let min = 0;
				value += 1;
				if (value > max) {
					return;
				}
				if(value < min){
					value = min
				}
				
				item.material_num = value;
				this.$forceUpdate()
			},
			inputs(num,item){
				item.material_num = this.num
				console.log(item.material_num)
			}
		}
	}
</script>

<style lang="scss" scoped>
	.page {
		width: 100%;
		height: 100%;
		background: #F1F0F3;
	}
	
	.card_style{
		font-size: 16px;
		
		.num_select{
			right: 0;
		}
		
		.select{
			float: right;
		}
	}
	
	.list-text {
		display: flex;
		flex-direction: row;
	}
	
	.shopcart .cartBottom {
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		height: 54px;
		z-index: 99;
		display: flex;
		background-color: #141d27;
	}
	
	.input_style{
		position: fixed;
		top: 44px;
		left: 0;
		right: 0;
		height: 44px;
		z-index: 99;
		display: flex;
		background-color: #20a0ff;
	}
	
	.carIcon {
		flex: 1;
	}
	
	.iconBox {
		position: absolute;
		bottom: 22px;
		left: 18px;
		z-index: 101;
		background-color: rgb(70, 73, 75);
		border-radius: 50%;
		height: 48px;
		width: 48px;
		line-height: 55px;
		/* border: 6px solid #141d27; */
	}
	
	.iconBox .allcount {
		position: absolute;
		right: -6px;
		top: 0;
		display: inline-block;
		padding: 0 6px;
		font-size: 9px;
		line-height: 16px;
		font-weight: 400;
		border-radius: 10px;
		background-color: #f01414;
		color: #ffffff;
	}
	
	.img {
		font-size: 24px;
		line-height: 24px;
		width: 30px;
		height: 30px;
		padding-left: 6px;
		padding-top: 6px;
		color: #cccccc;
		border-radius: 50%;
	}
	
	.carIcon .active {
		background-color: #00a0dc;
	}
	
	.middle {
		display: flex;
		flex-direction: column;
		flex: 2;
		color: #ffffff;
	}
	
	.BtnRight {
		flex: 1;
		
		.button_style{
			padding: 0;
			margin: 0;
			height: 54px;
			border-radius: 0;
		}
		
		}
	
	.input-view {
		align-items: center;
		justify-content: center;
		width: 100%;
		display: flex;
		background-color: #e7e7e7;
		height: 30px;
		border-radius: 15px;
		padding: 0 4%;
		flex-wrap: nowrap;
		margin: 7px 10rpx;
		line-height: 30px;
		background: #f5f5f5;
		
		.input {
			height: 30px;
			line-height: 30px;
			width: 94%;
			padding: 0 3%;
		}
	}
</style>
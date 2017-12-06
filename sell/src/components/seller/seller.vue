<template>
	<div class="seller">
      <div class="topwrap">
      	<div class="name">{{this.seller.name}}</div>
      	<div class="starwrap">
      		<star :size="36" :score="this.seller.score"></star>
      	</div>
      	<span class="ratingNum">({{ratings.length}})</span>
      	<span class="sellNum">月售{{sell}}单</span>
      </div>
      <div class="basicNumWrap">
      	<div class="wrap">
      		<div class="label">起送价</div>
      		<div class="num">
      		{{seller.minPrice}}
				<span>元</span>
      		</div>
      	</div>
      	<div class="wrap">
      		<div class="label">商家配送</div>
      		<div class="num">
      		{{seller.deliveryPrice}}
				<span>元</span>
      		</div>
      	</div>
      	<div class="wrap">
      		<div class="label">平均配送时间</div>
      		<div class="num">
      			{{seller.deliveryTime}}
				<span>分钟</span>
      		</div>
      	</div>
      </div>
      <div class="inbetween"></div>
      <div class="bulletinWrap">
      	<div class="title">公告与活动</div>
      	<p class="contents">{{seller.bulletin}}</p>
      	<ul>
      		<li v-for="items in seller.supports" class="singleWrap">
      			<span :class="classMap[items.type]+'_4'" class="icon"></span>
      			<span class="description">{{items.description}}</span>
      		</li>
      	</ul>
      </div>
      <div class="inbetween"></div>
      <ul class="picWrap">
      	<div class="title">商家实景</div>
      	<div class="pics-wrap">
	      	<li v-for="items in seller.pics" class="singlePics">
	      		<img :src="items">
	      	</li>
      	</div>
      </ul>
      <div class="inbetween"></div>
      <div class="infoWrap">
      	<div class="title">商家信息</div>
      	<ul>
      		<li v-for="items in seller.infos" class="singleInfo">{{items}}</li>
      	</ul>
      </div>
      	<shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    </div>

</template>

<script type="text/ecmascript-6">
	import bus from '../common/public.js';
	import star from '../star/star.vue';
	import shopcart from '../shopcart/shopcart.vue';
	const ERR_OK = 0;
	export default{
		props: {
			seller: {
				type: Object
			}
		},
		data() {
			return {
				goods: [],
				passfoods: [],
				ratings: []
			};
		},
		computed: {
			sell: function() {
				let result = 0;
				for (let i = 0; i < this.goods.length; i++) {
					let foods = this.goods[i].foods;
					for (let j = 0; j < foods.length; j++) {
						result = result + foods[j].sellCount;
					}
				}
				return result;
			},
			selectFoods() {
				let foods = [];
					this.passfoods.forEach((food) => {
						if (food.count) {
							foods.push(food);
						}
					});
				return foods;
			}
		},
		components: {
			'star': star,
			'shopcart': shopcart
		},
		created() {
			this.$http.get('/api/goods').then(function(res) {
				res = res.body;
				if (res.errno === ERR_OK) {
					this.goods = res.data;
				}
			});
			this.$http.get('/api/ratings').then(function(res) {
				res = res.body;
				if (res.errno === ERR_OK) {
					this.ratings = res.data;
				}
			});
			this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
			bus.$on('passfoods', (res) => {
				this.passfoods = res;
			});
		},
		beforeDestroy() {
			// bus.$emit('passfoods', this.selectFoods);
			bus.$emit('passfoods', this.passfoods);
		}
	};
</script>

<style lang="stylus" rel="stylesheet/stylus">

	.seller
		position: absolute
		top: 174px
		bottom: 0px
		width: 100%
		overflow: auto
		.topwrap
			margin: 18px 18px 0 18px
			padding-bottom: 18px
			border-bottom: 1px solid rgba(7,17,27,0.1)
			.name
				font-size: 14px
				font-weight: 600
				color: rgb(7,17,27)
				line-height: 14px
				margin-bottom: 8px
			.starwrap
				display: inline-block
				margin-right: 8px
				vertical-align: top
			.ratingNum
				margin-right: 12px
				vertical-align: top
				font-size: 10px
				color: rgb(77,85,93)
				line-height: 18px
			.sellNum
				vertical-align: top
				font-size: 10px
				color: rgb(77,85,93)
				line-height: 18px
		.basicNumWrap
			display: flex
			padding: 18px 
			border-bottom: 1px solid rgba(7,17,27,0.1)
			.wrap
				flex: 1
				text-align: center
				font-size: 0
				border-right: 1px solid rgba(7,17,27,0.1)
				&:last-child
					border: none
				.label
					font-size: 10px
					color: rgb(147,153,159)
					line-height: 10px
					margin-bottom: 4px
				.num
					font-size: 24px
					color: rgb(7,17,27)
					font-weight: 200
					line-height: 24px
					span
						font-size:10px
		.inbetween
			background-color: #f3f5f7
		.bulletinWrap
			border-top: 1px solid rgba(7,17,27,0.1)
			border-bottom: 1px solid rgba(7,17,27,0.1)
			padding: 18px 18px 0 18px
			.title
				font-weight: 600
				font-size: 14px
				color: rgb(7,17,27)
				line-height: 14px
			.contents
				margin-top: 8px
				padding-left:12px
				padding-right: 12px
				font-size: 12px
				font-weight: 200 
				color: rgb(240,20,20)
				line-height: 24px
				padding-bottom: 16px
				border-bottom: 1px solid rgba(7,17,27,0.1)
			ul
				.singleWrap
					padding: 16px 12px 
					border-bottom: 1px solid rgba(7,17,27,0.1)
					&:last-child
						border: none
					.icon
						width: 16px
						height: 16px
						margin:0 12px
						background-size: 16px 16px
						background-repeat: no-repeat
						display: inline-block
						vertical-align: top
					.decrease_4
						background-image: url(decrease_4@2x.png)
					.discount_4
						background-image: url(discount_4@2x.png)
					.guarantee_4
						background-image: url(guarantee_4@2x.png)
					.invoice_4
						background-image: url(invoice_4@2x.png)
					.special_4
						background-image: url(special_4@2x.png)
					@media (-webkit-min-device-pixel-ratio: 3)
						.decrease_4
							background-image:url(decrease_4@3x.png)
						.discount_4
							background-image:url(discount_4@3x.png)
						.guarantee_4
							background-image:url(guarantee_4@3x.png)
						.invoice_4
							background-image:url(invoice_4@3x.png)	
						.special_4
							background-image:url(special_4@3x.png)
					.description
						font-size: 12px
						font-weight: 200
						color: rgb(7,17,27)
						line-height: 16px
		.picWrap
			padding: 18px
			overflow-x: hidden
			border-top: 1px solid rgba(7,17,27,0.1)
			border-bottom: 1px solid rgba(7,17,27,0.1)
			.title
				font-size: 14px
				font-weight: 600
				color: rgb(7,17,27)
				line-height: 14px
			.pics-wrap
				height: 120px
				width: 100%
				overflow-x: auto
				white-space: nowrap
				.singlePics
					display: inline-block
					margin-top: 12px
					margin-right: 6px
					&:last-child
						margin-right: 0
					img
						height: 90px
						width: 120px
						background-size: 120px 90px
						background-repeat: no-repeat
		.infoWrap
			padding: 18px 
			border-top: 1px solid rgba(7,17,27,0.1)
			border-bottom: 1px solid rgba(7,17,27,0.1)
			.title
				font-size: 14px
				font-weight: 600
				color: rgb(7,17,27)
				line-height: 14px
				margin-bottom: 12px
			.singleInfo
				padding: 16px 12px
				border-top: 1px solid rgba(7,17,27,0.1)
				font-size: 12px
				font-weight: 200
				color: rgb(7,17,27)
				line-height: 16px





</style>
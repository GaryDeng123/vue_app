<template>
	<transition name="move">
		<div v-show="showFlag" class="food" @click="show">
			<div class="pic"><img :src="food.image"></div>
			<div class="head">
				<div class="name">{{food.name}}</div>
				<div>
					<span class="sell-count">月售{{food.sellCount}}份</span>
					<span class="rating">好评率{{food.rating}}%</span>
				</div>
				<div class="price">
					<span class="now-price">
						<span class="symbol">￥</span>
						{{food.price}}
					</span>
					<span class="old-price" v-show="food.oldPrice">{{food.oldPrice}}</span>
				</div>
				<div class="addtoCart" @click.stop="addCart" v-show="!food.count">加入购物车</div>
				<div class="cartcontrol-wrapper" v-show="food.count">
					<cartcontrol :food="food"></cartcontrol>
				</div>
			</div>
			<div class="divide"></div>
			<div class="desc-wrapper" v-show="food.info">
				<div class="title">商品介绍</div>
				<div class="content">{{food.info}}</div>
			</div>
			<div class="divide" v-show="food.info"></div>
			<div class="ratings-wrap">
				<div class="title">商品评价</div>
				<div class="label-wrap">
					<span class="all label">
						全部
						<span class="num">{{foodCount}}</span>
					</span>
					<span class="recommend label">
						推荐
						<span class="num">{{foodRecommend}}</span>
					</span>
					<span class="diss label">
						吐槽
						<span class="num">{{foodDiss}}</span>
					</span>
				</div>
			</div>
				<div class="content-only">
					<span class="icon-check_circle icon"></span>
					<span class="text">只看有内容的评价</span>
				</div>
				<ul>
					<li v-for="item in food.ratings" class="rating-single">
						{{item.text}}
					</li>
				</ul>
			
		</div>
	</transition>
</template>

<script type="text/ecmascript-6">
	import Vue from 'vue';
	import cartcontrol from '../cartcontrol/cartcontrol.vue';
	export default {
		props: {
			food: {
				type: Object
			}
		},
		data() {
			return {
				showFlag: false
			};
		},
		methods: {
			show() {
				this.showFlag = !this.showFlag;
			},
			addCart() {
				Vue.set(this.food, 'count', 1);
			}
		},
		components: {
			'cartcontrol': cartcontrol
		},
		computed: {
			foodCount() {
				if (!this.food.ratings) {
					return;
				}
				return this.food.ratings.length;
			},
			foodRecommend() {
				if (!this.food.ratings) {
					return;
				}
				let count = 0;
				this.food.ratings.forEach((type) => {
					if (type.rateType === 0) {
						count++;
					}
				});
				return count;
			},
			foodDiss() {
				if (!this.food.ratings) {
					return;
				}
				let count = 0;
				this.food.ratings.forEach((type) => {
					if (type.rateType === 1) {
						count++;
					}
				});
				return count;
			}
		}
	};
</script>

<style lang="stylus" rel="stylesheet/stylus">
	.food
		position: fixed
		left:0
		top:0
		bottom: 48px
		z-index: 30
		width:100%
		overflow: auto
		background-color: #FFF
		transition:all 0.4s linear
		&.move-enter,&.move-leave-to
			left: 100%
		.pic
			height: 375px
			width: 100%
			img
				height: 375px
				width: 100%
		.head
			position:relative
			padding:18px
			border-bottom: 1px solid rgba(7,17,27,0.1)
			.name
				font-size:14px
				font-weight:700
				color:rgb(7,17,27)
				line-height:14px
				margin-bottom:8px
			.sell-count,.rating
				font-size:10px
				color:rgb(147,153,159)
				line-height:10px
				margin-right: 12px
			.price
				margin-top: 18px
				.now-price
					font-size: 14px
					font-weight: 700
					color:rgb(240,20,20)
					line-height: 24px
					.symbol
						font-size:10px
						font-weight:normal
				.old-price
					font-size: 10px
					font-weight: 700
					color:rgb(147,153,159)
					line-height: 24px
					.symbol
						font-size:10px
						font-weight:normal
			.addtoCart
				position: absolute
				width: 74px
				height:24px
				padding:6px 12px
				box-sizing:border-box
				right:18px
				bottom:18px
				font-size:10px
				color:#FFF
				line-height:12px
				background-color:rgb(0,160,220)
				border-radius:12px
			.cartcontrol-wrapper
				position: absolute
				right:18px
				bottom:18px
		.divide
			height:16px
			background-color:#f3f5f7
			border-bottom: 1px solid rgba(7,17,27,0.1)
		.desc-wrapper
			padding:18px
			border-bottom: 1px solid rgba(7,17,27,0.1)
			.title
				font-size:14px
				color:rgb(7,17,27)
				line-height:24px
			.content
				font-size:12px
				font-weight:200
				color:rgb(77,85,93)
				line-height:24px
				margin: 6px 8px
		.ratings-wrap
			padding:18px 18px 0 18px
			.title
				margin-bottom:6px
				font-size:14px
				color:rgb(7,17,27)
				line-height:14px
			.label-wrap
				margin-top:12px
				padding-bottom:18px
				border-bottom:1px solid rgba(7,17,27,0.1)
				.label
					padding:8px 12px
					margin-right:8px
					font-size:12px
					line-height:16px
					.num
						font-size:8px
				.all
					background-color:rgb(0,160,220)
					border-radius:1px
					color:#FFF
				.recommend
					background-color:rgba(0,160,220,0.2)
					border-radius:1px
					color:rgb(77,85,93)
				.diss
					background-color:rgba(77,85,93,0.2)
					border-radius:1px
					color:rgb(77,85,93)
		.content-only
			padding:12px 18px
			border-bottom:1px solid rgba(7,17,27,0.1)
			.icon-check_circle
				font-size:24px
				color:rgb(147,153,159)
				line-height:24px
			.text
				margin-left:8px
				font-size:12px
				color:rgb(147,153,159)
				line-height:24px







</style>
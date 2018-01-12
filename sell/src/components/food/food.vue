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
					<span class="all label" @click.stop="showAll">
						全部
						<span class="num">{{foodCount}}</span>
					</span>
					<span class="recommend label" @click.stop="recommendShow">
						推荐
						<span class="num">{{foodRecommend}}</span>
					</span>
					<span class="diss label" @click.stop="dissShow">
						吐槽
						<span class="num">{{foodDiss}}</span>
					</span>
				</div>
			</div>
			<div class="content-only">
				<span class="icon-check_circle icon" @click.stop="textShow" :class="checkicon"></span>
				<span class="text">只看有内容的评价</span>
			</div>
			<div class="ratings-detail-wrap">
				<ul>
					<li v-for="item in food.ratings" class="rating-single" v-show="(textShowFlag||item.text)&&(recommendFlag||!item.rateType)&&(dissFlag||item.rateType)">

							<div class="time">{{item.rateTime | time}}</div>
							<span :class="classMap[item.rateType]" class="icon"></span>
							<span class="text">{{item.text}}</span>
							<div class="name-pic-wrap">
								<div class="name">{{item.username}}</div>
								<div class="pic"><img :src="item.avatar"></div>
							</div>

					</li>
				</ul>
			</div>
			
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
				showFlag: false,
				classMap: ['icon-thumb_up', 'icon-thumb_down'],
				textShowFlag: true,
				recommendFlag: true,
				dissFlag: true
			};
		},
		methods: {
			show() {
				this.showFlag = !this.showFlag;
				this.textShowFlag = true;
				this.recommendFlag = true;
				this.dissFlag = true;
			},
			addCart() {
				Vue.set(this.food, 'count', 1);
			},
			textShow() {
				this.textShowFlag = !this.textShowFlag;
			},
			recommendShow() {
				this.recommendFlag = false;
			},
			dissShow() {
				this.dissFlag = false;
			},
			showAll() {
				this.recommendFlag = true;
				this.dissFlag = true;
			}
		},
		components: {
			'cartcontrol': cartcontrol
		},
		filters: {
			time: function (value) {
				let d = new Date(parseInt(value));
				return (d.getFullYear()) + '-' + (d.getMonth() + 1 > 9 ? d.getMonth() + 1 : '0' + (d.getMonth() + 1)) + '-' + (d.getDate() > 9 ? d.getDate() : '0' + d.getDate()) + ' ' + (d.getHours() > 9 ? d.getHours() : '0' + d.getHours()) + ':' + (d.getMinutes() > 9 ? d.getMinutes() : '0' + d.getMinutes()) + ':' + (d.getSeconds() > 9 ? d.getSeconds() : '0' + d.getSeconds());
			}
		},
		computed: {
			foodCount() {
				if (!this.food.ratings) {
					return;
				}
				return this.food.ratings.length;
			},
			checkicon() {
				if (this.textShowFlag === true) {
					let con = 'icon-on';
					return con;
				} else {
					let con = 'icon-off';
					return con;
				}
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
				width: 84px
				height:24px
				text-align:center
				// padding:6px 12px
				box-sizing:border-box
				right:18px
				bottom:18px
				font-size:10px
				color:#FFF
				line-height:24px
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
			.icon
				font-size:24px
				line-height:24px
			.icon-on
				color:rgb(147,153,159)
			.icon-off
				color:rgb(0,160,220)
			.text
				margin-left:8px
				font-size:12px
				color:rgb(147,153,159)
				line-height:24px
		.ratings-detail-wrap
			margin:0 18px
			.rating-single
				position:relative
				padding:16px 0
				font-size:0
				border-bottom:1px solid rgba(7,17,27,0.1)
				.time
					font-size:10px
					color:rgb(147,153,159)
					line-height:12px
				.text
					display:inline-block
					font-size:12px
					color:rgb(7,17,27)
					line-height:16px
					margin-left:4px
					margin-top:6px
				.icon
					display:inline-block
					font-size:12px
					line-height:16px
					margin-top:6px
				.icon-thumb_up
					color: rgb(0,160,220)
				.icon-thumb_down
					color: rgb(183,187,191)
				.name-pic-wrap
					position:absolute
					top:16px
					right:0px
					.name
						display:inline-block
						font-size:10px
						color:rgb(147,153,159)
						line-height:12px
					.pic
						display:inline-block
						margin-left:6px
						width:12px
						height:12px
						left: 6px
						vertical-align:top
						img
							width:12px
							height:12px
							background-size: 12px 12px
							border-radius:50%








</style>
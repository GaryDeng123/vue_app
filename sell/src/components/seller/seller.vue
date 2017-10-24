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
				<span>元</span>
      		</div>
      	</div>
      </div>
    </div>

</template>

<script type="text/ecmascript-6">
	import star from '../star/star.vue';
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
			}
		},
		components: {
			'star': star
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
		}
	};
</script>

<style lang="stylus" rel="stylesheet/stylus">

	.seller
		.topwrap
			margin: 18px 18px 0 18px
			padding-bottom: 18px
			border-bottom: 1px solid rgba(7,17,27,0.1)
			.name
				font-size: 14px
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
			padding: 18px 0
			margin: 0 18px 
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


</style>
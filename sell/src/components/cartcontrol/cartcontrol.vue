<template>
	<div class="cartcontrol">
		<transition name="move">
			<div class="cart-decrease" v-show="food.count>0" @click.stop="decreaseCart">
				<span class="inner icon-remove_circle_outline"></span>
			</div>
		</transition>
		<div class="cart-count" v-show="food.count>0">{{food.count}}</div>
		<div class="cart-add" @click.stop="addCart">
			<span class="icon-add_circle"></span>
		</div>
	</div>
</template>
	
<script type="text/ecmascript-6">
	import Vue from 'vue';
	import bus from '../common/public.js';
	export default{
		props: {
			food: {
				type: Object
			}
		},
		created() {
			// console.log(this.food);
		},
		methods: {
			addCart() {
				// console.log('click');
				if (!this.food.count) {
					Vue.set(this.food, 'count', 1);
				} else {
					this.food.count ++;
				}
				// console.log('++++++' + event.target);
				// this.$emit('cartAdd', event.target);
				bus.$emit('caradd', event.target);
			},
			decreaseCart() {
				this.food.count --;
			}
		}
	};
</script>

<style lang="stylus" rel="stylesheet/stylus">
	.cartcontrol
		font-size: 0
		.cart-decrease
			display: inline-block
			padding: 6px
			transition: all 0.4s linear
			.inner
				display: inline-block
				font-size: 24px
				line-height: 24px
				color: rgb(0,160,220)
				transition: all 0.4s linear
			&.move-enter-to,move-leave
				opacity: 1
				transform: translate3D(0, 0, 0)
				.inner
					transform: rotate(0)
			&.move-enter, &.move-leave-to
				opacity: 0
				transform: translate3D(24px, 0, 0)
				.inner	
					transform: rotate(180deg)
		.cart-count
			display: inline-block
			vertical-align: top
			width: 12px
			padding-top: 6px
			line-height 24px
			text-align: center
			font-size: 10px
			color: rgb(147,153,159)
		.cart-add
			display: inline-block
			padding: 6px
			.icon-add_circle
				font-size: 24px
				line-height: 24px
				color: rgb(0,160,220)
</style>
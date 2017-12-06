<template>
	<div class="goods">
		<div class="menu-wrapper">
			<ul>
				<li v-for="(value,index) in goods" class="left-single" @click="goAnchor('#' + value.name)" 
				:class="{'current':currentIndex === index}">
					<span class="text">
						<span v-show="value.type>0" class="icon" :class="classMap[value.type]"></span>
						{{value.name}}
					</span>
				</li>
			</ul>
		</div>
		<div class="foods-wrapper" @scroll="getNowHeight()">
			<ul>
				<li v-for="value in goods" class="right-single right-single-hook" :id="value.name">
					<h1>{{value.name}}</h1>
					<ul class="foods-wrapper">
						<li v-for="foods in value.foods" class="foods-single" @click="selectFood(foods,$event)">
							<div class="foods-icon"><img :src="foods.icon"></div>
							<div class="foods-text-wrapper">
								<h1 class="name">{{foods.name}}</h1>
								<h2 class="description" v-show="foods.description">{{foods.description}}</h2>
								<span class="sellCount">月售{{foods.sellCount}}份</span> 
								<span class="rating">好评率{{foods.rating}}%</span>
								<div class="price">
									<span class="newPrice"><span>¥</span>{{foods.price}}</span>
									<span class="oldPrice" v-show="foods.oldPrice!='' ">¥{{foods.oldPrice}}</span>
								</div>
							</div>
							<div class="cartcontrol-wrapper">
								<cartcontrol :food="foods"></cartcontrol>
							</div>
						</li>
					</ul>
				</li>
			</ul>
		</div>
		<shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
		<food :food="selectedFood" ref="food"></food>
	</div>

</template>

<script type="text/ecmascript-6">
	import bus from '../common/public.js';
	import shopcart from '../shopcart/shopcart.vue';
	import cartcontrol from '../cartcontrol/cartcontrol.vue';
	import food from '../food/food.vue';
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
				eachTop: [],
				height: 0,
				selectedFood: {}
			};
		},
		computed: {
			currentIndex() {
				for (let i = 0; i < this.eachTop.length; i++) {
        let height1 = this.eachTop[i];
        let height2 = this.eachTop[i + 1];
        // console.log('height=' + this.height);
        // console.log('height1=' + height1);
        // console.log('height2=' + height2);
        if (!height2 || (this.height >= height1 && this.height < height2)) {
          // console.log('%%%%%%%%%%%%%%%%' + i);
          return i;
        }
       }
			},
			selectFoods() {
				let foods = [];
				this.goods.forEach((good) => {
					good.foods.forEach((food) => {
						if (food.count) {
							foods.push(food);
						}
					});
				});
				return foods;
			}
			// selectFoods() {
			// 	let foods = [];
			// 		this.passfoods.forEach((food) => {
			// 			if (food.count) {
			// 				foods.push(food);
			// 			}
			// 		});
			// 	return foods;
			// }
		},
		methods: {
			dopassfoods() {
				this.goods.forEach((good) => {
					good.foods.forEach((food) => {
						if (food.count) {
							this.passfoods.push(food);
						}
					});
				});
		},
		goAnchor(selector) {
        var anchor = this.$el.querySelector(selector);
        var newTop = anchor.offsetTop;
        var wrapper = this.$el.querySelector('.goods>.foods-wrapper');
        var oldTop = wrapper.scrollTop;
        var distance = newTop - oldTop;
        var step = distance / 50;
        if (newTop > oldTop) {
					Down();
        } else {
					Up();
        }
				function Down() {
				if (oldTop < newTop) {
				wrapper.scrollTop = oldTop;
				oldTop = oldTop + step;
				setTimeout(Down, 10);
				} else {
				wrapper.scrollTop = newTop;
				}
				}
				function Up() {
				if (oldTop > newTop) {
				wrapper.scrollTop = oldTop;
				oldTop = oldTop + step;
				setTimeout(Up, 10);
				} else {
				wrapper.scrollTop = newTop;
				}
			}
				this.height = newTop;
    },
    getEachHeight() {
      let foodList = this.$el.getElementsByClassName('right-single-hook');
      for (let i = 0; i < foodList.length; i++) {
        this.eachTop.push(foodList[i].offsetTop);
      }
     },
     getNowHeight() {
       var wrapper = this.$el.querySelector('.goods>.foods-wrapper');
       var height = wrapper.scrollTop;
       this.height = height;
       // console.log(height);
       // for (let i = 0; i < this.eachTop.length; i++) {
       //  let height1 = this.eachTop[i];
       //  let height2 = this.eachTop[i + 1];
       //  console.log('height=' + height);
       //  console.log('height1=' + height1);
       //  console.log('height2=' + height2);
       //  if (!height2 || (height > height1 && height < height2)) {
       //    console.log('%%%%%%%%%%%%%%%%' + i);
       //    return i;
       //  }
       // }
     },
		selectFood(foods, event) {
			// if (!event._constructed) {
			// 	return;
			// }
			this.selectedFood = foods;
			this.$refs.food.show();
		}
	},

		created() {
			this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
			bus.$on('passgoods', (res) => {
				this.goods = res;
			});
			this.$http.get('/api/goods').then(function(res) {
				res = res.body;
				if (res.errno === ERR_OK) {
					this.goods = res.data;
					this.$nextTick(() => {
						this.getEachHeight();
					});
				}
			});
			bus.$on('passfoods', (res) => {
				this.passfoods = res;
				// console.log(res);
				// this.dopassfoods();
			});
		},
		beforeDestroy() {
			// bus.$emit('passfoods', this.selectFoods);
			this.dopassfoods();
			bus.$emit('passfoods', this.passfoods);
		},
		components: {
		'shopcart': shopcart,
		'cartcontrol': cartcontrol,
		'food': food
    }
	};
</script>

<style type="text/css">
	.goods{
		display: flex;
		position: absolute;
		top: 174px;
		bottom: 46px;
		width: 100%;
		overflow: hidden;
	}
	.menu-wrapper{
		flex: 0 0 80px;
		width: 80px;
		background-color: #f3f5f7;
		overflow: auto;
	}
	.current{
		font-weight: 700;
		background-color: #FFF;
	}
	.foods-wrapper{
		flex: 1;
		background-color: #f3f5f7;
		overflow: auto;
	}
	.left-single{
		display: flex;
		align-items: center;
		justify-content: center;
		height: 54px;
		/*margin: 0 12px;*/
		border-bottom: 1px solid;
		border-color: rgba(7,17,27,0.1);		
	}
	.left-single:last-child{
		border:none;
	}
	.icon{
	display: inline-block;
	vertical-align: top;
	height: 12px;
	width: 12px;
	margin-right: 2px;
	background-size: 12px 12px;
	background-repeat: no-repeat;
}
.decrease{
	background-image: url(decrease_1@2x.png);
}
.discount{
	background-image: url(discount_1@2x.png);
}
.guarantee{
	background-image: url(guarantee_1@2x.png);
}
.invoice{
	background-image: url(invoice_1@2x.png);
}
.special{
	background-image: url(special_1@2x.png);
}

@media (-webkit-min-device-pixel-ratio: 3){
	.decrease{
		background-image:url(decrease_1@3x.png);
	}
	.discount{
		background-image:url(discount_1@3x.png);
	}
	.guarantee{
		background-image:url(guarantee_1@3x.png);
	}
	.invoice{
		background-image:url(invoice_1@3x.png);
	}
	.special{
		background-image:url(special_1@3x.png);
	}
}
.left-single>.text{
	width: 56px;
	text-align: left;
	font-size: 12px;
	line-height: 14px;
	/*color: rgb(147,153,159);*/
}
/*.goods>.foods-wrapper{
		transition: top linear 0.1s;
}*/
.right-single>h1{
	font-size: 12px;
	color: rgb(147,153,159);
	line-height: 26px;
	height: 26px;
	padding-left: 14px;
	border-left: 2px solid #d9dde1;
}
.right-single>.foods-wrapper{
	background-color: #FFF;
	height: 100%;
}
.foods-wrapper>.foods-single{
	display: flex;
	height: 100%;
	margin: 0 18px;
	padding: 18px 0;
	/*position: relative;*/
	border-bottom: 1px solid;
	border-color: rgba(7,17,27,0.1);
	/*display: inline-block;*/
	position: relative;
}
.foods-single:last-child{
	border: none;
}
.foods-single>.cartcontrol-wrapper{
	position: absolute;
	right: 0;
	bottom: 14px;
}
.foods-icon{
	width: 60px;
	height: 60px;
	/*float: left;*/
}
.foods-icon>img{
	width: 60px;
	height: 60px;
}

.foods-text-wrapper{
	margin-left: 10px;
	margin-top: 2px;
}
.foods-text-wrapper>.name{
	font-size: 14px;
	color: rgb(7,17,27);
	line-height: 14px;
}
.foods-text-wrapper>.description{
	font-size: 10px;
	color: rgb(147,153,159);
	line-height: 15px;
	margin-top: 8px;
}
.foods-text-wrapper>.sellCount,.foods-text-wrapper>.rating{
	font-size: 10px;
	line-height: 10px;
	color: rgb(147,153,159);
	margin-top: 8px;
	margin-bottom: 8px;
}
.foods-text-wrapper>.sellCount{
	margin-right: 12px;
}
.price>.newPrice{
	font-size: 14px;
	color: rgb(240,20,20);
	font-weight: 700;
	line-height: 24px
}
.newPrice>span{
	font-size: 10px;
	font-weight: normal;
}
.price>.oldPrice{
	vertical-align: top;
	margin-left: 8px;
	font-size: 10px;
	color: rgb(147,153,159);
	font-weight: 700;
	line-height: 24px;
	text-decoration: line-through;
}
</style>
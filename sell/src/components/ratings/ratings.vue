<template>
	<div class="ratings">
		<div class="comments-wrap">
			<div class="seller-wrap">
				<div class="left">
					<h1>{{seller.score}}</h1>
					<div>综合评分</div>
					<p>高于周边商家{{seller.rankRate}}%</p>
				</div>
				<div class="right">
					<div class="star-wrap">
						<span class="rate-label">服务态度</span>
						<span>
							<star :size="36" :score="seller.serviceScore"></star>
						</span>
						<span class="score">{{seller.serviceScore}}</span>
					</div>
					<div class="star-wrap">
						<span class="rate-label">食品分数</span>
						<span>
							<star :size="36" :score="seller.foodScore"></star>
						</span>
						<span class="score">{{seller.foodScore}}</span>
					</div>
					<div class="star-wrap">
						<span class="rate-label">送达时间</span>
						<span class="deliveryTime">{{seller.deliveryTime}}分钟</span>
					</div>
				</div>
			</div>
			<div class="inbetween"></div>
			<ul class="part2-wrap">
				<li v-for="value in ratings" class="single-wrap">
					<img :src="value.avatar" class="user-avatar">
					<div class="right">
						<h1>{{value.username}}</h1>
						<div class="score-wrap">
							<span><star :size="24" :score="value.score"></star></span>
							<span class="howsoon" v-if="value.deliveryTime">{{value.deliveryTime}}分钟送达</span>
						</div>
						<p>{{value.text}}</p>
						<ul class="recommend" v-if="value.recommend">
							<span :class="classMap[value.rateType]" class="recommend-icon"></span>
							<div class="detail-wrap">
								<li v-for="content in value.recommend" class="recommend-detail">
									{{content}}
								</li>
							</div>
						</ul>
						<div class="time">{{value.rateTime | time}}</div>
					</div>
				</li>
			</ul>
		</div>
    </div>

</template>

<script type="text/ecmascript-6">
	import star from '../star/star.vue';
	const ERR_OK = 0;
	// const THUMB_UP = 'on';
	// const THUMB_DOWN = 'off';
	export default{
		props: {
			seller: {
				type: Object
			}
		},
		data() {
			return {
				ratings: []
			};
		},
		computed: {
			itemClasses() {
				// let result = [];
			}
		},
		components: {
			'star': star
		},
		filters: {
			time: function (value) {
				let d = new Date(parseInt(value));
								return (d.getFullYear()) + '-' + (d.getMonth() + 1 > 9 ? d.getMonth() + 1 : '0' + (d.getMonth() + 1)) + '-' + (d.getDate() > 9 ? d.getDate() : '0' + d.getDate()) + ' ' + (d.getHours() > 9 ? d.getHours() : '0' + d.getHours()) + ':' + (d.getMinutes() > 9 ? d.getMinutes() : '0' + d.getMinutes()) + ':' + (d.getSeconds() > 9 ? d.getSeconds() : '0' + d.getSeconds());
			}
		},
		created() {
			this.classMap = ['icon-thumb_down', 'icon-thumb_up'];
			this.$http.get('/api/ratings').then(function(res) {
				res = res.body;
				if (res.errno === ERR_OK) {
					this.ratings = res.data;
					// console.log(this.ratings);
				}
			});
		}
	};
</script>

<style type="text/css">
	.ratings{
		width: 100%;
		position: absolute;
		top: 174px;
		bottom: 0px;
		width: 100%;
		overflow: auto;
	}
	.inbetween{
		height: 18px;
		width: 100%;
		background-color: #f3f5f7;
	}
	.seller-wrap{
		border-top: 1px solid rgba(7,17,27,0.1);
		border-bottom: 1px solid rgba(7,17,27,0.1);
		width: 100%;
		display: flex;
	}
	.seller-wrap>.left{
		margin: 18px 0;
		flex: 0 0 137px;
		width: 137px;
		text-align: center;
		border-right: 1px solid rgba(7,17,27,0.1);
	}
	@media only screen and (max-width: 320px){
		.seller-wrap>.left{
			flex: 0 0 120px;
			width: 120px;
		}
	}
	.seller-wrap>.left>h1{
		font-size: 24px;
		color: rgb(255,153,0);
		line-height: 28px;
		margin-bottom: 6px;
	}
	.seller-wrap>.left>div{
		font-size: 12px;
		color: rgb(7,17,27);
		line-height: 18px;
		margin-bottom: 8px;
	}
	.seller-wrap>.left>p{
		font-size: 10px;
		color: rgba(7,17,27,0.5);
		line-height: 10px;
		margin-bottom: 6px;
	}
	.seller-wrap>.right{
		margin: 18px 0 18px 24px;
		text-align: left;
	}
	@media only screen and (max-width: 320px){
		.seller-wrap>.right{
			margin-left: 8px;
		}
	}
	.seller-wrap>.right>.star-wrap{
		display: inline-block;
		margin-bottom: 8px;
		vertical-align: top;
	}
	.seller-wrap>.right>.star-wrap>span{
		display: inline-block;
	}
	.seller-wrap>.right>.star-wrap>.rate-label{
		display: inline-block;
		font-size: 12px;
		color: rgb(7,17,27);
		line-height: 18px;
		margin-right: 12px;
		vertical-align: top;
	}
	@media only screen and (max-width: 320px){
		.seller-wrap>.right>.star-wrap>.rate-label{
			margin-right: 4px;
		}
	}
	.seller-wrap>.right>.star-wrap>.score{
		display: inline-block;
		font-size: 12px;
		color: rgb(255,153,0);
		line-height: 18px;
		vertical-align: top;
		margin-left: 12px;
		vertical-align: top;
	}
	@media only screen and (max-width: 320px){
		.seller-wrap>.right>.star-wrap>.score{
			margin-left: 4px;
		}
	}
	.seller-wrap>.right>.star-wrap>.deliveryTime{
		font-size: 12px;
		color: rgb(147,153,159);
		line-height: 18px;
	}
	.part2-wrap{
		border-top: 1px solid rgba(7,17,27,0.1)
	}
	.single-wrap{
		margin: 18px 12px 18px 18px;
		display: flex;
		border-bottom: 1px solid rgba(7,17,27,0.1);
	}
	.user-avatar{
		flex: 0 0 28px;
		width: 28px;
		height: 28px;
		margin-right: 12px;
		border-radius: 28px;
		background-size: 28px 28px;
		background-repeat: no-repeat;
	}
	.single-wrap>.right{
		margin-right: 18px;
		flex: 1;
		width: 100%;
		position: relative;
	}
	.single-wrap>.right>h1{
		font-size: 20px;
		color: rgb(7,17,27);
		line-height: 12px; 
		margin-bottom: 4px;
	}
	.single-wrap>.right>p{
		font-size: 12px;
		color: rgb(7,17,27);
		line-height: 12px;
		margin-top: 6px;
		margin-bottom: 8px;
	}
	.score-wrap>span{
		display: inline-block;
		margin-right: 6px;
	}
	.howsoon{        
		font-size: 10px;
		font-weight: 200;
		color: rgb(147,153,159);
		line-height: 12px;
	}
	.recommend{
		/*white-space: nowrap;*/
		overflow: hidden;
		text-overflow: ellipsis;
		width: 100%;
		display: flex;
		align-items:top;
	}
	.recommend>.detail-wrap{
		overflow: hidden;
		text-overflow: ellipsis;
		/*white-space: nowrap;*/
		margin-bottom: 18px;
	}
	.recommend-detail{
		padding: 0 6px;
		/*width: 40px;*/
		border:1px solid rgba(7,17,27,0.1);
		border-radius: 2px;
		background-color: #FFF;
		font-size: 9px;
		color: rgb(147,153,159);
		line-height: 16px;
		display: inline-block;
		margin-right: 8px;
		/*vertical-align: top;*/
	}
	.recommend>span{
		float: left;
	}
	.recommend-icon{
		margin-right: 8px;
		font-size: 12px;
		vertical-align: top;
		position: relative;
		bottom: -2px;
	}
	.icon-thumb_up{
		color: rgb(0,160,220);
	}
	.icon-thumb_down{
		color: rgb(183,187,191);
	}
	.single-wrap>.right>p{
		font-size: 12px;
		color: rgb(7,17,27);
		line-height: 18px;
	}
	.time{
		position: absolute;
		right: 18px;
		top: 0px;
		font-size: 10px;
		font-weight: 200;
		color: rgb(147,153,159);
		line-height: 12px;
	}
</style>
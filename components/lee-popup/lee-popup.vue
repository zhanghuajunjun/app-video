<template>
	<view class="lee-popup-mask" :class="maskClass" @click="maskClickHandler" data-role="mask">
		<view class="lee-popup" :style="popupStyle" @transitionend="onAnimationEnd">
			<slot></slot>
		</view>
	</view>
</template>

<script>
	// 弹出层状态
	const Status = {
		OPEN: 'open',
		CLOSE: 'close',
		OPENED: 'opened',
		CLOSED: 'closed'
	}
	
	/**
	 * LeePopup弹出层
	 * @property {String}  type = [top|left|right|bottom] 弹出方向
	 * 		@value top 从顶部弹出
	 * 		@value left 从左侧弹出
	 * 		@value right 从右侧弹出
	 * 		@value bottom 从底部弹出
	 * @property {String} width 横向弹出时，指定弹框宽度
	 * @property {String} height 纵向弹出时，指定弹出高度
	 * @property {Object} customStyle 自定义弹出层样式
	 * @property {Boolean} animation 是否开启动画 
	 * @property {Boolean} round 是否使用圆角
	 * @property {String} padding 定义边距
	 * @event {Function(status: String)} change 状态更新事件
	 * @event {Function} open 弹层打开事件，动画未结束
	 * @event {Function} opened 弹层打开事件，动画结束时
	 * @event {Function} close 弹层关闭事件，动画未结束
	 * @event {Function} closed 弹层关闭事件，动画结束时
	 */
	export default {
		props: {
			
			// 弹窗方向
			type: {
				type: String,
				default: 'top'
			},
			
			// 横向弹出时，指定弹框宽度
			width: {
				type: String,
				default: '440rpx'
			},
			
			// 纵向弹出时，指定弹出高度
			height: {
				type: String,
				default: 'auto'
			},
			
			// 自定义弹出层样式
			customStyle: {
				type: Object,
				default: () => {}
			},
			
			// 是否开启动画
			animation: {
				type: Boolean,
				default: true
			},
			
			// 圆角模式
			round: {
				type: Boolean,
				default: false
			},
			
			// 边距
			padding: {
				type: String,
				default: '30rpx'
			}
		},
		data() {
			return {
				status: Status.CLOSED
			}
		},
		computed: {
			
			// 是否为纵向弹出层
			isVertical() {
				return ['top', 'bottom'].includes(this.type)
			},
			
			// 弹出层样式计算
			popupStyle() {
				const style = Object.assign({
					width: this.isVertical ? '100%' : this.width,
					height: this.isVertical ? this.height : '100%',
					padding: this.padding
				}, this.customStyle)
				if (this.animation) {
					style.transition = 'all .5s'
				}
				return Object.entries(style).map(item => `${item[0]}: ${item[1]}`).join(';')
			},
			
			// 遮罩层类计算
			maskClass() {
				const list = [
					`lee-popup-type_${this.type}`,
					`lee-popup-status_${this.status}`,
				]
				if (this.round) {
					list.push('lee-popup-round')
				}
				return list
			}
		},
		watch: {
			// 派发状态更新事件
			status(status) {
				this.$emit(status)
				this.$emit('change', status)
			}
		},
		methods: {
			
			// 打开弹出层
			open() {
				this.status = this.animation ? Status.OPEN : Status.OPENED
			},
			// 关闭弹出层
			close() {
				this.status = this.animation ? Status.CLOSE : Status.CLOSED
			},
			
			// 点击遮罩层
			maskClickHandler(e) {
				if (e.target.dataset.role === 'mask') {
					this.close()
				}
			},
			
			// 动画结束
			onAnimationEnd() {
				if (this.status === Status.OPEN) {
					this.status = Status.OPENED
				} else if (this.status === Status.CLOSE) {
					this.status = Status.CLOSED
				}
			}
		}
	}
</script>

<style lang="scss">
	$z-index: 3000;
	
	.lee-popup-mask {
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		position: fixed;
		z-index: $z-index;
		background-color: $uni-bg-color-mask;
		
		&.lee-popup-status_closed {
			visibility: hidden;
		}
	}
	
	.lee-popup {
		box-sizing: border-box;
		background-color: $uni-bg-color;
		position: absolute;
		
		&-round > & {
			border-radius: $uni-border-radius-lg;
		}
		
		&-type_top > & {
			top: 0;
			left: 0;
			transform: translate(0, -100%);
			border-top-left-radius: 0;
			border-top-right-radius: 0;
		}
		&-type_left > & {
			top: 0;
			left: 0;
			transform: translate(-100%, 0);
			border-top-left-radius: 0;
			border-bottom-left-radius: 0;
		}
		&-type_right > & {
			top: 0;
			right: 0;
			transform: translate(100%, 0);
			border-top-right-radius: 0;
			border-bottom-right-radius: 0;
		}
		&-type_bottom > & {
			left: 0;
			bottom: 0;
			transform: translate(0, 100%);
			border-bottom-left-radius: 0;
			border-bottom-right-radius: 0;
		}
		
		&-status_open > &,
		&-status_opened > & {
			transform: translate(0, 0);
		}
	}
</style>

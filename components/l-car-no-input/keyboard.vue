<template>
	<u-popup :overlay="overlay" :closeOnClickOverlay="closeOnClickOverlay" mode="bottom" :popup="false" :show="show"
		:safeAreaInsetBottom="safeAreaInsetBottom" @close="popupClose" :zIndex="zIndex" :customStyle="{
			backgroundColor: 'rgb(214, 218, 220)'
		}">
		<view class="u-keyboard">
			<slot />
			<view class="u-keyboard__tooltip" v-if="tooltip">
				<view hover-class="u-hover-class" :hover-stay-time="100">
					<text class="u-keyboard__tooltip__item u-keyboard__tooltip__cancel" v-if="showCancel"
						@tap="onCancel">{{showCancel && cancelText}}</text>
				</view>
				<view>
					<text v-if="showTips"
						class="u-keyboard__tooltip__item u-keyboard__tooltip__tips">{{tips ? tips : '车牌号键盘'}}</text>
				</view>
				<view hover-class="u-hover-class" :hover-stay-time="100">
					<text v-if="showConfirm" @tap="onConfirm"
						class="u-keyboard__tooltip__item u-keyboard__tooltip__submit"
						hover-class="u-hover-class">{{showConfirm && confirmText}}</text>
				</view>
			</view>
			<view class="u-keyboard" @touchmove.stop.prevent="noop">
				<view v-for="(group, i) in keyBoardList" :key="i" class="u-keyboard__button" :index="i"
					:class="[i + 1 === 4 && 'u-keyboard__button--center']">
					<view class="u-keyboard__button__inner-wrapper" v-for="(item, j) in group" :key="j"
						:class="{disabled: checkDisabled(i, j)}">
						<view class="u-keyboard__button__inner-wrapper__inner" :hover-stay-time="200"
							@tap="carInputClick(i, j)" hover-class="u-hover-class">
							<text class="u-keyboard__button__inner-wrapper__inner__text">{{ item }}</text>
						</view>
					</view>
					<view v-if="i === keyBoardList.length - 1" @touchstart="backspaceClick" @touchend="clearTimer"
						class="u-keyboard__button__inner-wrapper">
						<view class="u-keyboard__button__inner-wrapper__right" hover-class="u-hover-class"
							:hover-stay-time="200">
							<u-icon size="28" name="backspace" color="#303133"></u-icon>
						</view>
					</view>
				</view>
			</view>
		</view>
	</u-popup>
</template>

<script>
	export default {
		name: "l-car-keyboard",
		data() {
			return {}
		},
		mixins: [uni.$u.mpMixin, uni.$u.mixin],
		props: {
			keyReg: {
				type: RegExp,
				default: ''
			},
			keyBoardList: {
				type: Array,
				default: () => []
			},
			// 是否显示顶部工具条
			tooltip: {
				type: Boolean,
				default: uni.$u.props.keyboard.tooltip
			},
			// 是否显示工具条中间的提示
			showTips: {
				type: Boolean,
				default: uni.$u.props.keyboard.showTips
			},
			// 工具条中间的提示文字
			tips: {
				type: String,
				default: uni.$u.props.keyboard.tips
			},
			// 是否显示工具条左边的"取消"按钮
			showCancel: {
				type: Boolean,
				default: uni.$u.props.keyboard.showCancel
			},
			// 是否显示工具条右边的"完成"按钮
			showConfirm: {
				type: Boolean,
				default: uni.$u.props.keyboard.showConfirm
			},
			// 是否开启底部安全区适配，开启的话，会在iPhoneX机型底部添加一定的内边距
			safeAreaInsetBottom: {
				type: Boolean,
				default: uni.$u.props.keyboard.safeAreaInsetBottom
			},
			// 是否允许通过点击遮罩关闭键盘
			closeOnClickOverlay: {
				type: Boolean,
				default: uni.$u.props.keyboard.closeOnClickOverlay
			},
			// 控制键盘的弹出与收起
			show: {
				type: Boolean,
				default: uni.$u.props.keyboard.show
			},
			overlay: {
				type: Boolean,
				default: uni.$u.props.keyboard.overlay
			},
			// z-index值
			zIndex: {
				type: [String, Number],
				default: uni.$u.props.keyboard.zIndex
			},
			// 取消按钮的文字
			cancelText: {
				type: String,
				default: uni.$u.props.keyboard.cancelText
			},
			// 确认按钮的文字
			confirmText: {
				type: String,
				default: uni.$u.props.keyboard.confirmText
			},
		},
		methods: {
			checkDisabled(i, j) {
				let value = this.keyBoardList[i][j];
				return !this.keyReg.test(value)
			},
			// 点击键盘按钮
			carInputClick(i, j) {
				let value = this.keyBoardList[i][j];
				if (this.keyReg.test(value)) {
					this.$emit('change', value);
				}
			},
			// 修改汽车牌键盘的输入模式，中文|英文
			changeCarInputMode() {
				this.abc = !this.abc;
			},
			// 点击退格键
			backspaceClick() {
				this.$emit('backspace');
				clearInterval(this.timer); //再次清空定时器，防止重复注册定时器
				this.timer = null;
				this.timer = setInterval(() => {
					this.$emit('backspace');
				}, 250);
			},
			clearTimer() {
				clearInterval(this.timer);
				this.timer = null;
			},
			// 键盘关闭
			popupClose() {
				this.$emit('close');
			},
			// 输入完成
			onConfirm() {
				this.$emit('confirm');
			},
			// 取消输入
			onCancel() {
				this.$emit('cancel');
			},
		}
	}
</script>

<style lang="scss" scoped>
	@import "uview-ui/libs/css/components.scss";

	.u-keyboard {

		&__tooltip {
			@include flex;
			justify-content: space-between;
			background-color: #FFFFFF;
			padding: 14px 12px;

			&__item {
				color: #333333;
				flex: 1;
				text-align: center;
				font-size: 15px;
			}

			&__submit {
				text-align: right;
				color: $u-primary;
			}

			&__cancel {
				text-align: left;
				color: #888888;
			}

			&__tips {
				color: $u-tips-color;
			}
		}
	}

	$u-car-keyboard-background-color: rgb(224, 228, 230) !default;
	$u-car-keyboard-padding:6px 0 6px !default;
	$u-car-keyboard-button-inner-width:64rpx !default;
	$u-car-keyboard-button-inner-background-color:#FFFFFF !default;
	$u-car-keyboard-button-height:80rpx !default;
	$u-car-keyboard-button-inner-box-shadow:0 1px 0px #999992 !default;
	$u-car-keyboard-button-border-radius:4px !default;
	$u-car-keyboard-button-inner-margin:8rpx 5rpx !default;
	$u-car-keyboard-button-text-font-size:16px !default;
	$u-car-keyboard-button-text-color:$u-main-color !default;
	$u-car-keyboard-center-inner-margin: 0 4rpx !default;
	$u-car-keyboard-special-button-width:134rpx !default;
	$u-car-keyboard-lang-font-size:16px !default;
	$u-car-keyboard-lang-color:$u-main-color !default;
	$u-car-keyboard-active-color:$u-primary !default;
	$u-car-keyboard-line-font-size:15px !default;
	$u-car-keyboard-line-color:$u-main-color !default;
	$u-car-keyboard-line-margin:0 1px !default;
	$u-car-keyboard-u-hover-class-background-color:#BBBCC6 !default;

	.u-keyboard {
		@include flex(column);
		justify-content: space-around;
		background-color: $u-car-keyboard-background-color;
		align-items: stretch;
		padding: $u-car-keyboard-padding;

		&__button {
			@include flex;
			justify-content: center;
			flex: 1;
			/* #ifndef APP-NVUE */
			/* #endif */

			&__inner-wrapper {
				box-shadow: $u-car-keyboard-button-inner-box-shadow;
				margin: $u-car-keyboard-button-inner-margin;
				border-radius: $u-car-keyboard-button-border-radius;


				&__inner {
					@include flex;
					justify-content: center;
					align-items: center;
					width: $u-car-keyboard-button-inner-width;
					background-color: $u-car-keyboard-button-inner-background-color;
					height: $u-car-keyboard-button-height;
					border-radius: $u-car-keyboard-button-border-radius;

					&__text {
						font-size: $u-car-keyboard-button-text-font-size;
						color: $u-car-keyboard-button-text-color;
					}
				}

				&.disabled {
					.u-keyboard__button__inner-wrapper__inner {
						background-color: #eeeeee;

						.u-keyboard__button__inner-wrapper__inner__text {
							color: #999999 !important;
						}
					}
				}

				&__left,
				&__right {
					border-radius: $u-car-keyboard-button-border-radius;
					width: $u-car-keyboard-special-button-width;
					height: $u-car-keyboard-button-height;
					background-color: $u-car-keyboard-u-hover-class-background-color;
					@include flex;
					justify-content: center;
					align-items: center;
					box-shadow: $u-car-keyboard-button-inner-box-shadow;
				}

				&__left {
					&__line {
						font-size: $u-car-keyboard-line-font-size;
						color: $u-car-keyboard-line-color;
						margin: $u-car-keyboard-line-margin;
					}

					&__lang {
						font-size: $u-car-keyboard-lang-font-size;
						color: $u-car-keyboard-lang-color;

						&--active {
							color: $u-car-keyboard-active-color;
						}
					}
				}
			}
		}
	}

	.u-hover-class {
		background-color: $u-car-keyboard-u-hover-class-background-color;
	}
</style>

<template>
	<view>
		<l-car-keyboard ref="uKeyboard" :keyBoardList="keyBoardList" :keyReg="keyReg" :show="show" :overlay="overlay"
			:closeOnClickOverlay="closeOnClickOverlay" :tooltip="tooltip" :showTips="showTips" :tips="tips"
			:showCancel="showCancel" :showConfirm="showConfirm" :zIndex="zIndex" :cancelText="cancelText"
			:confirmText="confirmText" @change="change" @backspace="backspace" @close="popupClose" @confirm="onConfirm"
			@cancel="onCancel">
			<slot />
		</l-car-keyboard>
	</view>
</template>

<script>
	import lCarKeyboard from './keyboard.vue'
	const reg0 = /^[京津沪渝冀豫云辽黑湘皖鲁新苏浙赣鄂桂甘晋蒙陕吉闽贵粤青藏川宁琼使领]$/
	const reg1 = /^[A-HJ-NP-Z]$/
	const reg2 = /^[A-HJ-NP-Z0-9]{0,5}$/
	const reg3 = /^[A-HJ-NP-Z0-9挂学警港澳]$/
	export default {
		components: {
			lCarKeyboard
		},
		data() {
			return {}
		},
		props: {
			show: {
				type: Boolean,
				default: false
			},
			value: {
				type: String,
				default: ''
			},
			random: {
				type: Boolean,
				default: false
			},
			// 是否开启底部安全区适配，开启的话，会在iPhoneX机型底部添加一定的内边距
			safeAreaInsetBottom: {
				type: Boolean,
				default: uni.$u.props.keyboard.safeAreaInsetBottom
			},
			// 是否显示遮罩
			overlay: {
				type: Boolean,
				default: uni.$u.props.keyboard.overlay
			},
			// 是否允许通过点击遮罩关闭键盘
			closeOnClickOverlay: {
				type: Boolean,
				default: uni.$u.props.keyboard.closeOnClickOverlay
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
		computed: {
			modelValue: {
				get() {
					return this.value
				},
				set(val) {
					this.$emit('change', val)
					this.$emit('input', val)
				}
			},
			keyBoardList() {
				const {
					modelValue = ''
				} = this
				const len = modelValue.length || 0
				switch (len) {
					case 0:
						return ((random) => {
							let data = [
								'京',
								'沪',
								'粤',
								'津',
								'冀',
								'豫',
								'云',
								'辽',
								'黑',
								'湘',
								'皖',
								'鲁',
								'苏',
								'浙',
								'赣',
								'鄂',
								'桂',
								'甘',
								'晋',
								'陕',
								'蒙',
								'吉',
								'闽',
								'贵',
								'渝',
								'川',
								'青',
								'琼',
								'宁',
								'藏',
								'新',
								'使',
								'领',
							]
							let tmp = [];
							if (random) data = uni.$u.randomArray(data);
							tmp[0] = data.slice(0, 10);
							tmp[1] = data.slice(10, 20);
							tmp[2] = data.slice(20, 28);
							tmp[3] = data.slice(28, 33);
							return tmp;
						})(this.random)
					default:
						return ((random) => {
							let data = [
								1,
								2,
								3,
								4,
								5,
								6,
								7,
								8,
								9,
								0,
								'Q',
								'W',
								'E',
								'R',
								'T',
								'Y',
								'U',
								'I',
								'O',
								'P',
								'A',
								'S',
								'D',
								'F',
								'G',
								'H',
								'J',
								'K',
								'L',
								'Z',
								'X',
								'C',
								'V',
								'B',
								'N',
								'M',
								'挂',
								'学',
								'警',
								'港',
								'澳'
							]
							let tmp = [];
							if (random) data = uni.$u.randomArray(data);
							tmp[0] = data.slice(0, 10);
							tmp[1] = data.slice(10, 20);
							tmp[2] = data.slice(20, 29);
							tmp[3] = data.slice(29, 36);
							tmp[4] = data.slice(36, 41);
							return tmp;
						})(this.random)
				}
			},
			keyReg() {
				const {
					modelValue = ''
				} = this
				const len = modelValue.length || 0
				switch (len) {
					case 0:
						return reg0
					case 1:
						return reg1
					case 2:
					case 3:
					case 4:
					case 5:
					case 6:
						return reg2
					case 7:
						return reg3
					default:
						return /^[]$/
				}
			}
		},
		methods: {
			change(keyValue) {
				this.modelValue = `${this.modelValue}${keyValue}`
			},
			backspace() {
				this.modelValue = this.modelValue.substr(0, this.modelValue.length - 1)
				this.$emit('backspace');
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

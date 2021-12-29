<template>
	<view class="wrap">
		<u--form labelPosition="left" :model="formParams" ref="form1" labelWidth="auto">
			<u-form-item label="车牌1:" prop="carNo1" borderBottom ref="item1" @click.native="handleShow('carNo1')"
				placeholder="请输入车牌号码">
				<u--input v-model="formParams.carNo1" readonly border="none"></u--input>
			</u-form-item>
			<u-form-item label="车牌2:" prop="carNo2" borderBottom ref="item1" @click.native="handleShow('carNo2')"
				placeholder="请输入车牌号码">
				<u--input v-model="formParams.carNo2" readonly border="none"></u--input>
			</u-form-item>
		</u--form>
		<l-car-no-input :show="show" v-model="carNo" @cancel="handleCancel" @close="handleCancel"
			@confirm="handleCancel" :closeOnClickOverlay="true" @change="handleCarNoInputChange" />
	</view>
</template>

<script>
	import lCarNoInput from '@/components/l-car-no-input/index.vue'

	export default {
		components: {
			lCarNoInput
		},
		data() {
			return {
				show: false,
				carNo: '',
				formParams: {
					carNo1: '',
					carNo2: ''
				},
				handleCarNoInputChange: () => {}
			}
		},
		methods: {
			handleShow(fieldName) {
				this.show = true
				this.carNo = this.formParams[fieldName]
				this.handleCarNoInputChange = (val) => {
					this.formParams[fieldName] = val
				}
			},
			handleCancel() {
				this.show = false
			},
		}

	}
</script>

<style>
	page {}

	.wrap {
		margin: 30rpx;
		padding: 30rpx;
		background-color: #FFFFFF;
		box-shadow: 0 0 20rpx 2rpx rgba(0, 0, 0, .08);
	}
</style>

<template>
	<view class="content">
		<button @click="isShow=true">sb</button>
		<button @click="hp">hp</button>
		<mwTreePicker ref="tree" :show="isShow" :data="xx" title="Hello" label="name" children="banjilist" @submit="submit" @cancel="isShow=false" />
	</view>
</template>

<script>
	import mwTreePicker from "./mw-tree-picker.vue"
	export default {
		data() {
			return {
				isShow: false,
				title: 'Hello',
				xx: [{
						banjilist: [{
								id: 10073,
								name: "一年级1班",
								banjilist: [{
										id: 10073,
										name: "一年级1班",
									},
									{
										id: 10074,
										name: "一年级2班"
									},
								]
							},
							{
								id: 10074,
								name: "一年级2班"
							},
						],
						id: 2019,
						name: "一年级",
					},
					{
						banjilist: [{
							id: 10075,
							name: "二年级1班"
						}, {
							id: 10076,
							name: "二年级2班"
						}],
						id: 2020,
						name: "二年级"
					},
					{
						banjilist: [{
							id: 1007522,
							name: "二年级1班"
						}, {
							id: 10076,
							name: "二年级2班"
						}],
						id: 2020,
						name: "二年级"
					}

				],
				listData: [{
					id: 1,
					name: '公司1',
					children: [{
						id: 11,
						name: '研发部',
						children: [{
							id: 111,
							name: '张三',
						}, {
							id: 112,
							name: '李四',

						}]
					}, {
						id: 12,
						name: '综合部',
					}]
				}, ]
			}
		},
		onLoad() {

		},
		methods: {
			hp() {
				this.isShow = true
				const arr = [{
							id: 1007522,
							name: "二年级1班"
						}]
				this.$refs.tree.checkData(arr, 'id')
				
			},
			submit(data) {
				console.log(data, "Hello")
			},
			showPicker() {

			}
		},
		components: {
			mwTreePicker
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
<template>
	<scroll-view :style="{'background-color': currentThemeColor.backgroundColor}" scroll-y class="ss-slider">
		<view v-if="chapterList.length>0">
			<block v-for="(item, index) in chapterList" :key="index">
				<view @click="chapterItemAction(index)" class="chapter-item" :style="{'color': currentChapter ==  index ? '#449bda' : currentThemeColor.color}" :class="{'chapter-current-item': currentChapter ==  index}">
					<text>{{item.title}}</text>
				</view>
				<view class="line" :style="{'background-color': currentThemeColor.borderColor}"></view>
			</block>
		</view>
		<view v-else style="text-align: center;margin-top: 100%;">暂无更多章节~</view>
	</scroll-view>
</template>

<script>
	export default {
		name: 'ss-slider',
		props: {
			chapterList: {
				type: Array,
				default () {
					return [];
				}
			},
			index: {
				type: Number,
				default () {
					return 0;
				}
			},
			currentChapter: {
				type: Number,
				default () {
					return 0;
				}
			},
			isNightTheme: {
				type: Boolean,
				default () {
					return false;
				}
			}

		},
		computed: {
			currentThemeColor: function () {
				return this.isNightTheme ? this.nightThemeColor : this.dayThemeColor;
			}
		},
		data () {
			return {
				dayThemeColor: {
					backgroundColor: '#fff',
					color: '#333',
					borderColor: '#f0f0f0'
				},
				nightThemeColor: {
					backgroundColor: '#333',
					color: '#808080',
					borderColor: '#808080'
				}
			}
		},
		methods: {
			chapterItemAction (index) {
				this.$emit('chapterItemTap', index);
			}
		}
	}
</script>

<style lang="scss">
	.ss-slider {
		height: 100%;
		display: flex;
		flex-direction: column;

		.line {
			margin: 0 10upx 0upx 30upx;
			height: 1px;
		}

		.chapter-item {
			padding: 20upx 30upx;
			font-size: $uni-font-size-base;
		}

		.chapter-current-item {
			color: #449bda;
		}
	}
</style>

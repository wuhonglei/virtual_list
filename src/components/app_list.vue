<template>
	<div
		class="list-view"
		@scroll="handleScroll"
	>
		<div
			class="list-view-phantom"
			:style="{
         height: `${contentHeight()}px`
      }"
		>
		</div>
		<div
			ref="content"
			class="list-view-content"
		>
			<div
				class="list-view-item"
				:key="item.value"
				:style="{height: getRandomStyle(item).height + 'px', backgroundColor: getRandomStyle(item).backgroundColor}"
				v-for="item in visibleData"
			>
				{{ item.value }}
			</div>
		</div>
	</div>
</template>

<script>
export default {
	name: 'ListView',
	props: {
		data: {
			type: Array,
			required: true
		},

		itemHeight: {
			type: Number,
			default: 30
		}
	},
	mounted() {
		this.updateVisibleData();
	},

	data() {
		return {
			visibleData: []
		};
	},

	methods: {
		updateVisibleData(scrollTop) {
      scrollTop = scrollTop || 0;
			const start = this.findNearestItemIndex(scrollTop);
			const end = this.findNearestItemIndex(scrollTop + this.$el.clientHeight) + 10;
			this.visibleData = this.data.slice(start, Math.min(end + 1, this.data.length)); // 获取可见区域的元素列表
			/**
			 * 滚动条向下滑动过程中，整个容器的区域向上滑动，此时 content 内容区为了抵抗这种运动，需要整体向下移动
			 */
			this.$refs.content.style.transform = `translateY(${this.getItemSizeAndOffset(start).offset}px)`;
		},

		getRandomStyle({value}) {
			let colors = ['red', 'blue', 'white', 'orange'];
			let heights = [25, 30, 40, 50];
      let index = (value % 4);

			return {
				backgroundColor: colors[index],
				height: heights[index]
			};
		},

		handleScroll() {
			const scrollTop = this.$el.scrollTop;
			this.updateVisibleData(scrollTop);
		},

		// 计算内容总高度
		contentHeight() {
			const { data, getRandomStyle } = this;
			let total = 0;
			for (let i = 0, j = data.length; i < j; i++) {
				total += getRandomStyle(data[i]).height;
      }
			return total;
		},

		// 计算指定滚动距离对应的起始索引值
		findNearestItemIndex(scrollTop) {
			const { data, getRandomStyle } = this;
			let total = 0;
			for (let i = 0, j = data.length; i < j; i++) {
				const size = getRandomStyle(data[i]).height;
				total += size;
				if (total >= scrollTop || i === j - 1) {
					return i;
				}
			}

			return 0;
		},
		getItemSizeAndOffset(index) {
			const { data, getRandomStyle } = this;
			let total = 0;
			for (let i = 0, j = Math.min(index, data.length - 1); i <= j; i++) {
				const size = getRandomStyle(data[i]).height;

				if (i === j) {
					return {
						offset: total,
						size
					};
				}
				total += size;
			}

			return {
				offset: 0,
				size: 0
			};
		}
	}
}
</script>

<style lang="postcss" scoped>
.list-view {
	height: 400px;
	overflow: auto;
	position: relative;
	border: 1px solid #aaa;
}

.list-view-phantom {
	position: absolute;
	left: 0;
	top: 0;
	right: 0;
	/* 不显示 */
	z-index: -1;
}

.list-view-content {
	position: absolute;
	left: 0;
	right: 0;
	top: 0;
}

.list-view-item {
	padding: 5px;
	color: #666;
	box-sizing: border-box;
}
</style>

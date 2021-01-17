<template>
	<div
		class="list-view"
		@scroll="handleScroll"
	>
		<div
			class="list-view-phantom"
			:style="{
         height: contentHeight
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
				:style="getRandomStyle(item.value)"
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

	computed: {
		contentHeight() {
			return this.data.length * this.itemHeight + 'px';
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
			/**
			 * clientheight=padding+height-横向滚动轴高度
			 *
			 * Math.ceil 表示取顶，例如 Math.ceil(2.1) -> 3
			 */
			const visibleCount = Math.ceil(this.$el.clientHeight / this.itemHeight);

			/**
			 * scrollTop 表示元素在有滚动条时，滚动条向下滚动的距离也就是元素顶部被遮住部分的高度,不带单位
			 * 没有滚动条时，元素的 scrollTop 恒为 0
			 *
			 * itemHeight = 10
			 * 场景1：
			 * scrollTop = 10
			 * 则 start 为 1
			 *
			 * 场景2:
			 * scrollTop = 5
			 * 则 start 为 0
			 */
			const start = Math.floor(scrollTop / this.itemHeight);
      const end = start + visibleCount; // visibleCount = 14
			this.visibleData = this.data.slice(start, end); // 获取可见区域的元素列表
			/**
			 * 滚动条向下滑动过程中，整个容器的区域向上滑动，此时 content 内容区为了抵抗这种运动，需要整体向下移动
			 */
			this.$refs.content.style.transform = `translateY(${start * this.itemHeight}px)`;
		},

		getRandomStyle(value) {
			let colors = ['red', 'blue', 'white', 'orange'];
			let heights = ['25px', '30px', '40px', '50px'];
			let index = (value % 4);
			return {
				backgroundColor: colors[index],
				height: '30px'
			};
		},

		handleScroll() {
			const scrollTop = this.$el.scrollTop;
			this.updateVisibleData(scrollTop);
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

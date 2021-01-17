<template>
	<div id="app">
     <app-list :data="list"></app-list>
	</div>
</template>

<script>

import AppList from './components/app_list.vue';

export default {
	name: 'App',
	components: {
		AppList
  },
  data() {
    return {
      list: this.getDataList()
    }
  },
  methods: {
    getDataList() {
      return [...new Array(150)].map((item,index) => {
        return {
          value: index + 1
        }
      })
    }
  },
}
</script>

<style>
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}
</style>

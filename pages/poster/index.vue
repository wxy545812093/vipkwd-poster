<template>
	<poster ref="poster" :hideLoading="false" :preload="false" :config="config" @success="onPosterSuccess" @fail="onPosterFail"></poster>
</template>

<script>
	import poster from '@/components/vipkwd-poster/index'
	import Data from './demoData'
	export default {
		components: {
			poster
		},
		mixins: [],
		data() {
			return {
				config: Data.demo,
			}
		},
		onLoad(options){
			//this.getData(options)
		},
		methods: {
			getData(options){
				//远程获取数据
				
				
				//本地构造数据结构
				
				//刷新数据
				this.config = data;
			},
			/**
			 * 异步生成海报
			 */
			onCreatePoster() {
				this.$set(this, 'config', Data.demo);
				// 入参：true为抹掉重新生成 
				this.$refs.poster.onCreate(true);
			},
			/**
			 * 海报绘制成功，返回海报本地 filePath
			 */
			onPosterSuccess(e) {
			    console.log(e);
			},
			/**
			 * 海报绘制失败
			 */
			onPosterFail(err) {
			    console.error(err);
			},
		}
	}
</script>
<style lang="scss" scoped>
</style>

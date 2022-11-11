<template>
	<view>
		<view>
			<view class="view-container">
				<image v-if="tempFilePath" style="width: 100vw" :lazyLoad="true" mode="widthFix" :show-menu-by-longpress="true" :src="tempFilePath"></image>
			</view>
			<view class="action-bar">
				<button class="maker"  @click='onCreate'>绘制</button>
				<button v-if="tempFilePath" class="downloader" @click="onSaveToAlbum">保存</button>
				<button v-if="tempFilePath" class="viewer" @click="onViewer">预览</button>
			</view>
		</view>
		<we-canvas ref="weCanvas" id="poster" @success="onCreateSuccess" @fail="onCreateFail" />
	</view>
</template>

<script>
	import weCanvas from './wecanvas';
	export default {
		components: {
			weCanvas
		},
		props: {
			config: {
				type: Object,
				value: {},
			},
			preload: { // 是否预下载图片资源
				type: Boolean,
				value: false,
			},
			hideLoading: { // 是否隐藏loading
				type: Boolean,
				value: false,
			}
		},
		data(){
			return {
				tempFilePath: null
			}
		},
		onReady() {
		    if (this.preload) {
		        this.downloadStatus = 'doing';
		        this.$refs.weCanvas.downloadResource(this.config).then(() => {
		            this.downloadStatus = 'success';
		            this.trigger('downloadSuccess');
		        }).catch((e) => {
		            this.downloadStatus = 'fail';
		            this.trigger('downloadFail', e);
		        });
		    }
		},
		methods: {
		    onCreate(reset = false) {
		        !this.hideLoading && uni.showLoading({ mask: true, title: '生成中' });
		        return this.downloadResource(typeof reset === 'boolean' && reset).then(() => {
		            !this.hideLoading && uni.hideLoading();
		            this.$refs.weCanvas.create(this.config);
		        })
		        .catch((err) => {
		            !this.hideLoading && uni.hideLoading();
		            this.toast(err.errMsg || '生成失败');
		            this.$emit('fail', err);
		        })
		    },
			onViewer(){
				uni.previewImage({
				    current: this.tempFilePath,
				    urls: [this.tempFilePath]
				})
			},
			onSaveToAlbum(){
				const that = this
				const tryToSave = () => {
					uni.saveImageToPhotosAlbum({
						filePath: this.tempFilePath,
						success: function () {
							that.toast('保存成功');
						},
						fail(){
							that.toast('保存失败');
						}
					});
				}

				uni.getSetting({
					success(res){
						if(!res.authSetting['scope.writePhotosAlbum']){
							return uni.showModal({
								title: '提示',
								content: '需要您授权“添加到相册”权限',
								success: function (res) {
									if (res.confirm) {
										return uni.openSetting({
											success(res) {
												if(!res.authSetting['scope.writePhotosAlbum']){
													return that.toast('相册权限不足，保存失败');
												}
												tryToSave()
											},
											fail(e){
												console.error(121, e)
											}
										});
									} else if (res.cancel) {
										return that.toast('您已拒绝权限拉取，保存失败');
									}
								}
							});
						}
						tryToSave()
					}
				})
			},
		    onCreateSuccess(e) {
		        const { detail } = e;
				this.tempFilePath = detail
		        this.$emit('success', {detail});
		    },
		    onCreateFail(err) {
		        this.$emit('fail', err);
		    },
			toast(title){
				return uni.showToast({ icon: 'none', title });
			},
		    trigger(event, data) {
		        if (this.listener && typeof this.listener[event] === 'function') {
		            this.listener[event](data);
		        }
		    },
		    once(event, fun) {
		        if (typeof this.listener === 'undefined') {
		            this.listener = {};
		        }
		        this.listener[event] = fun;
		    },
		    downloadResource(reset) {
		        return new Promise((resolve, reject) => {
		            if (reset) {
		                this.downloadStatus = null;
		            }
		            if (this.downloadStatus && this.downloadStatus !== 'fail') {
						if(this.downloadStatus === 'success') {
		                    resolve();
		                } else {
		                    this.once('downloadSuccess', () => resolve());
		                    this.once('downloadFail', (e) => reject(e));
		                }
		            } else {
		                this.$refs.weCanvas.downloadResource(this.config)
		                    .then(() => {
		                        this.downloadStatus = 'success';
		                        resolve();
		                    })
		                    .catch((e) => reject(e));
		            }  
		        })
		    },
		}
	}
</script>

<style lang="scss" scoped>
	.view-container{
		background-image:url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAB4AAAAeCAYAAAA7MK6iAAAAAXNSR0IArs4c6QAAATVJREFUSEvtlV2SgyAQhBuh3DWS7N7F+x/Bs0QSdy1EcQu3tPIjZkhZxUPC80DTPQwfG4ZhQITFoguXZUn2XRTFVW3d/JD3yiwfa2fHry0shABjDAxAby36vr+Kci3qrutQn2u4M+Re3rXAG7UTTNMUWutx00eaojUGl49/TdgYg1Ol8JllyOV/Py+XV5hzDsE5dNuO9e4SzoW1dt7/6HGdlALnIkx4ctxqDTfgoY7d7VSlwJME8rCnO3aVrj8Jcx3G6LQL6HHz26A6HuFeyOH7C7t8R4uaMpCPol474z3HlIQ3rYkPiU3tEA6L7/gZtE3GniHb7Pi1haloW4qaitTFqKlouxUOQaq3xxS03QqHINUrTEGbzzEFqYvCVLT5ekxB6nucCL8sMMF8k5+LpLhhUTRI/AG573u2kUnP6wAAAABJRU5ErkJggg==);
		width: 100vw;
		min-height: 100vh;
	}
	.action-bar{
		position:fixed;
		bottom:60rpx;
		left:0;
		right:0;

		display: flex;
		justify-content: space-between;
		align-items: center;
		
		button{
			padding: 0 40rpx;
			height: 60rpx;
			display: flex;
			align-items: center;
			border-radius: 100rpx;
			color:#fff;
			font-size: 1rem;
		}
		.maker{
			background: blue;
		}
		.downloader{
			background: green;
		}
		.viewer{
			background: #b1b10c
		}
	}
</style>

<template>
  <view class="container">
    <view class="center">
	<view style="border: 1px solid black; margin: 10px;" v-if="!imagePath" >
      <uni-icons type="camera-filled" size="250" @click="getImg" ></uni-icons><br/>
	</view>
		<button v-if="!imagePath" type="default" @click="getImg" style="border: 1px solid black;width: 250px; margin-bottom: 5px;">拍照识别</button>
		<image v-if="imagePath" mode="aspectFit" :src="imagePath" class="uploaded-image" @click="preview"></image>
	   <button v-if="imagePath" type="default" @click="getImg" style="width: 250px; margin-bottom: 5px;">重新拍摄</button>
	   <button v-if="imagePath" type="default" @click="uploadImg" style="width: 250px;margin-bottom: 5px;">开始识别</button>
	   <button v-if="imagePath" type="default" @click="back" style="width: 250px;margin-bottom: 5px;">返回主页</button>
    </view>
  </view>
</template>
<script>
	export default {
		data() {
			return {
				imagePath: '', // 存储图片路径
			}
		},
		methods: {
			getImg() {
				console.log("enter upload");
				uni.chooseImage({
				count: 1,
				// sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
				sourceType: ['album', 'camera'], //从相册选择
				success: (chooseImageRes) => {
					uni.showToast({
					  title: '图片获取成功',
					  icon: 'none', // success|loading|none
					  duration: 2000 // 持续时间，单位为毫秒，默认为1500
					});
					const tempFilePaths = chooseImageRes.tempFilePaths;
					this.imagePath = tempFilePaths[0]; // 将图片路径赋值给imagePat
				}
				});
			},
			uploadImg(){
				uni.showToast({
				  title: '开始识别',
				  icon: 'none', // success|loading|none
				  duration: 2000 // 持续时间，单位为毫秒，默认为1500
				});
				uni.navigateTo({
				  url: '/pages/result/result?imagePath=' + encodeURIComponent(this.imagePath)
				});
			},
			back(){
				this.imagePath=''
			},
			preview(){
				// console.log("s");
				uni.previewImage({
							urls: [this.imagePath],
							success() {
								console.log("look!");
							},
							fail(e){
								console.log(e);
							}
						});
			}
			
		}
	}
</script>

<style>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.center {
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.label {
  margin-top: 10px;
  line-height: 20px;
}
.uploaded-image {
    margin-button: 20px;
    width: 400px;
    height: 450px;
	padding-bottom: 20px;
}

</style>

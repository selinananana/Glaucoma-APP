<template>
	<view class="container">
			<uni-section title="原始图像" subTitle="" type="line">
						<view class="example-body">
							<uni-row class="demo-uni-row" :width="nvueWidth">
								<uni-col >
									<image  :src="imagePath[0]" class="uploaded-image" mode="aspectFit" @click="preview(0)"></image>
								</uni-col>
							</uni-row>
						</view>		
			</uni-section>
			<uni-row class="demo-uni-row" :width="nvueWidth" style="text-align: center;" v-if="!ans">
				···处理中···
			</uni-row>
			<uni-section title="角度矫正" subTitle="" type="line" v-if="ans">
						<view class="example-body">
							<uni-row class="demo-uni-row" :width="nvueWidth">
								<uni-col>
									<image  :src="imagePath[1]" class="uploaded-image" mode="aspectFit" @click="preview(1)"></image>
								</uni-col>
							</uni-row>
						</view>
			</uni-section>
			<uni-section title="去噪处理" subTitle="" type="line" v-if="ans">
						<view class="example-body">
							<uni-row class="demo-uni-row" :width="nvueWidth">
								<uni-col>
									<image  :src="imagePath[2]" class="uploaded-image" mode="aspectFit" @click="preview(2)"></image>
								</uni-col>
							</uni-row>
						</view>	
			</uni-section>
			<uni-section title="识别结果" subTitle="" type="line" v-if="ans">
						<view class="example-body">
							<uni-row class="demo-uni-row" :width="nvueWidth">
								<uni-col>
									<!-- <image  :src="imagePath[3]" class="uploaded-image" mode="aspectFit" @click="preview(3)"></image> -->
									<uni-row class="demo-uni-row" :width="nvueWidth" style="text-align: center;" v-if="isIll">
										检测怀疑患有青光眼
									</uni-row>
									<uni-row class="demo-uni-row" :width="nvueWidth" style="text-align: center;" v-if="!isIll">
										检测未患有青光眼
									</uni-row>
								</uni-col>
							</uni-row>		
						</view>
			</uni-section>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				imagePath:[],
				answer:'',
				isIll:false,
				nvueWidth: uni.getSystemInfoSync().windowWidth,
				ans:false,
				isRequest:false
			}
		},
		methods: {
			onLoad(query) {
				console.log("onLoad==");
			   if (query.imagePath) {
			     const image = decodeURIComponent(query.imagePath);
			     // 在这里可以使用获取到的图片地址进行相关操作
			     console.log('传递过来的图片地址：', image);
				 this.imagePath[0]=image;
				 this.isRequest=false;
			   }
			 },
			 onShow(){	
				 console.log("onShow==");
				 if(!this.isRequest){
					 console.log("send a request");
					 this.isRequest=true;
					uni.uploadFile({		 
					  url: 'http://192.168.154.175:8080/judge',
					  filePath: this.imagePath[0],
					  name: 'file',
					  formData: {
					    'user': 'test'
					  },
					  header: {
					    'content-type' : 'multipart/form-data',
					  },
					  // timeout: 60000, 
					  success: (res) => {
						this.ans=true;
										uni.showToast({
										  title: '识别成功',
										  icon: 'none', // success|loading|none
										  duration: 2000 // 持续时间，单位为毫秒，默认为1500
										});
										console.log(res);
										const responseData = JSON.parse(res.data);
										this.imagePath[1] = responseData[0];
										// console.log(this.imagePath1);
										this.imagePath[2] = responseData[1];
										this.answer=responseData[2];
										if (this.answer === "1") {
										    // 执行当 answer 等于 "1" 时的后续工作
										   this.isIll=true;
										    // 进行其他操作...
										  } else {
										    // 执行当 answer 不等于 "1" 时的后续工作
										    this.isIll=false;
										    // 进行其他操作...
										  }
					  },
					  fail(e) {
					  	console.log(e)
						console.log('request fail')
					  }
					}); 
				 }
				 
			 },
			 preview(index){
			 	console.log(this.imagePath[index]);
			 	uni.previewImage({
							
			 				urls: this.imagePath,
							current:index,
			 				success() {
			 					console.log("look!"+index);
			 				},
			 				fail(e){
			 					console.log(e);
			 				}
			 			});
			 }
		},		 
	}
</script>

<style>
<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.example-body {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
  padding: 20px;
  background-color: #f5f5f5;
}

.demo-uni-row {
  margin-bottom: 10px;
}

.demo-uni-col {
  padding: 10px;
  text-align: center;
}

.uploaded-image {
  max-width: 100%;
  max-height: 100%;
}
</style>
</style>

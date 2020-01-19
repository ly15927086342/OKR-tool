<template>
	<view class="content">
		<view class="flex-row center-center player playing" :class="playing?'none':'keepgo'" @click="playing = !playing">
			<image v-if="playing" src="../../static/音乐开.png" style="width: 50rpx;height: 50rpx;"></image>
			<image v-else src="../../static/音乐关.png" style="width: 50rpx;height: 50rpx;"></image>
		</view>
		<view v-if="page == 0" class="content animated">
			<view class="title-container animated tdFadeInUp">
				<view class="sm-title-container">
					<view class="title">O</view>
					<view class="title">K</view>
					<view class="title">R</view>
				</view>
				<view class="sm-title-container">
					<view class="sm-title">Objectives&nbsp;</view>
					<view class="sm-title">and&nbsp;</view>
					<view class="sm-title">Key&nbsp;</view>
					<view class="sm-title">Results</view>
				</view>
				<view class="sm-title" style="margin-top: 0;">一套适用于个人的目标管理方法</view>
			</view>
			<view class="introduction">
				<view class="intro-item">
					<view class="character animated tdExpandInBounce">O</view>
					<view class="chr-intro chr-title animated tdExpandIn">目标：</view>
				</view>
				<view class="chr-intro-zh animated tdFadeIn">目标是要有野心的，有一些挑战的，有些让你不舒服的。</view>
				<view class="intro-item" style="margin-top: 50rpx;">
					<view class="character animated tdExpandInBounce">KR</view>
					<view class="chr-intro chr-title animated tdExpandIn">关键成果：</view>
				</view>
				<view class="chr-intro-zh animated tdFadeIn">基于数值的期望描述能够产生可量化、可评分的结果。</view>
			</view>
			<image class="btn-img animated tdPlopInUp" src="../../static/开始.png" @click="next"></image>
		</view>
		<view v-else-if="page == 1" class="content animated">
			<view style="width: 100%;margin-top: 20rpx;">
				<view class="step-item">
					<view class="step-title animated tdExpandIn" style="margin: 0;">
						第一步 你的目标<view v-if="noObj">*</view>
					</view>
					<view style="width: 100%;">
						<input type="text" placeholder="不超过20字" v-model="objective" style="margin-top: 20rpx;" maxlength="20" />
					</view>
				</view>
			</view>
			<view style="width: 100%;">
				<view class="step-item">
					<view class="step-title animated tdExpandIn">
						第二步 关键结果<view v-if="noKr">*</view>
					</view>
					<view style="width: 100%;">
						<view class="kr-item" v-for="(kr,index) in key_res" :key="index">
							<view style="margin-right: 20rpx;color: #c9d6de;">{{index+1}}</view>
							<input type="text" placeholder="不超过20字" v-model="kr.value" style="flex: 1;" maxlength="20" @blur="checkArray" />
							<image v-if="key_res.length > 1" src="../../static/移除.png" style="height: 40rpx;width: 40rpx;margin-left: 20rpx;"
							 @click="remove(index)"></image>
						</view>
						<view v-if="key_res.length == 5" style="color: #c9d6de;font-size: 30rpx;">最多设置五条关键结果</view>
						<image v-else-if="hasEmpty" src="../../static/添加.png" style="height: 40rpx;width: 40rpx;"
						 @click="add"></image>
					</view>
				</view>
			</view>
			<view style="width: 100%;">
				<view class="step-item">
					<view class="step-title animated tdExpandIn">
						第三步 设定期限
					</view>
					<picker mode="date" style="font-size: 35rpx;color: #52616A;" :start="startDate" :end="endDate" :value="date"
					 @change="bindChange">{{date}}</picker>
				</view>
			</view>
			<view class='btn'>
				<image class="btn-img animated tdFadeInRight" src="../../static/上一步.png" @click="back"></image>
				<image class="btn-img animated tdFadeInLeft" src="../../static/下一步.png" @click="next"></image>
			</view>
		</view>
		<view v-else-if="page == 2" class="content animated">
			<view style="width: 100%;margin-top: 20rpx;">
				<view class="step-item">
					<view class="step-title animated tdExpandIn" style="margin: 0;">
						第四步 你的昵称<view v-if="noName">*</view>
					</view>
					<view style="width: 100%;">
						<input type="text" placeholder="张三李四" v-model="name" style="margin-top: 20rpx;" maxlength="10" />
					</view>
				</view>
			</view>
			<view style="width: 100%;">
				<view class="step-item">
					<view class="step-title animated tdExpandIn">
						第五步 选择背景图
					</view>
					<view style="width: 100%;flex-wrap: wrap;margin-top: 40rpx;" class="flex-row center-start">
						<view class="flex-column center-center child" v-for="(item,index) in background" :key="index" @click="chooseIt(index)">
							<view class="animated" :class="chooseId == index ? 'tdSwingIn' : 'none'" :style="{backgroundColor:item.color}"
							 style="border-radius: 30rpx;height: 18vw;width: 18vw;position: relative;">
								<image v-if="chooseId == index" :src="icon.check" style="position: absolute;right: 15rpx;bottom: 15rpx;width: 30rpx;height: 30rpx;"></image>
							</view>
							<view style="font-size: 25rpx;color: #707070;line-height: 2.5;">{{item.name}}</view>
						</view>
					</view>
				</view>
			</view>
			<view class='btn'>
				<image class="btn-img animated tdFadeInRight" src="../../static/上一步.png" @click="back"></image>
				<image class="btn-img animated tdFadeInLeft" src="../../static/生成.png" @click="makePic"></image>
			</view>
		</view>
		<view v-else class="content animated" style="width: 100%;overflow: hidden;height: 100%;">
			<view class="cardBody">
				<view class="flex-column center-center" style="width: 100%;height: 100%;">
					<view v-if="load" class="flex-column center-center" style="position: absolute;z-index: 999;top: 0;left: 50%;transform: translateX(-50%);"
					 :style="{height: canvasHeight+'px'}">
						<!-- <image :src="require('../../static/loading.gif')" style="width: 100px;height: 100px;"></image> -->
						<div class="spinner">
							<div class="double-bounce1"></div>
							<div class="double-bounce2"></div>
						</div>
					</view>
					<canvas v-if="load" id="shareCanvas" canvas-id="shareCanvas" :style="{height: canvasHeight+'px',width: canvasWidth+'px'}"></canvas>
					<image v-else style="z-index: 1000;" :src="canvasBase" :style="{height: canvasHeight+'px',width: canvasWidth+'px'}"
					></image>
				</view>
			</view>
			<view class="flex-row center-center animated tdShrinkInBounce" style="height: 10%;">
				<image src="../../static/back.png" class="btn-last" @click="back" style="margin-right:60rpx;"></image>
				<!-- <image src="../../static/download.png" class="btn-last" @click="download"></image> -->
				<view style="color: #c9d6de;font-size: 30rpx;">长按图片，保存或分享</view>
			</view>
			<view class="flex-column center-center" style="height: 10%;font-size: 25rpx;color: #c9d6de;">
				<view>&copy; 2019-2020 Li Yu</view>
				<text selectable="true">源码见https://github.com/ly15927086342/OKR-tool.git</text>
			</view>
		</view>
	</view>
</template>

<script>
	import check from '../../static/check.js'
	var innerAudioContext = null;

	//是否微信打开
	// function isWeiXin() {
	// 	var ua = window.navigator.userAgent.toLowerCase();
	// 	console.log(ua); //mozilla/5.0 (iphone; cpu iphone os 9_1 like mac os x) applewebkit/601.1.46 (khtml, like gecko)version/9.0 mobile/13b143 safari/601.1
	// 	if (ua.match(/MicroMessenger/i) == 'micromessenger') {
	// 		return true;
	// 	} else {
	// 		return false;
	// 	}
	// }

	export default {
		data() {
			const currentDate = this.getDate({
				format: true
			});

			return {
				page: 0,
				objective: '',
				key_res: [],
				date: currentDate,
				name: '',
				background: [{
					name: '紫',
					color: '#d9e1e8',
					deepColor: '#9cafc9',
					src: '../../static/背景/OKR模板-01.png'
				}, {
					name: '绿',
					color: '#c5e99d',
					deepColor: '#8fbc95',
					src: '../../static/背景/OKR模板-02.png'
				}, {
					name: '粉',
					color: '#f1bbba',
					deepColor: '#eb9e9e',
					src: '../../static/背景/OKR模板-03.png'
				}, {
					name: '白',
					color: '#fffff5',
					deepColor: '#ffda8f',
					src: '../../static/背景/OKR模板-04.png'
				}, {
					name: '蓝',
					color: '#a3daff',
					deepColor: '#0080ff',
					src: '../../static/背景/OKR模板-05.png'
				}, {
					name: '黄',
					color: '#fff1b9',
					deepColor: '#f9a11b',
					src: '../../static/背景/OKR模板-06.png'
				}],
				chooseId: 0,
				icon: {
					check: check
				},
				canvasMaxHeight: 1167,
				canvasMaxWidth: 600,
				canvasWidth: 600,
				canvasHeight: 1167,
				// isWeiXin: false,
				load: true,
				canvasBase: '',
				playing: false,
			}
		},
		onLoad() {
			innerAudioContext = uni.createInnerAudioContext();
			let res = uni.getSystemInfoSync();
			console.log(res)
			this.canvasHeight = res.windowHeight * 0.75 / this.canvasMaxHeight * this.canvasMaxWidth > res.windowWidth ? Math.round(
				res.windowWidth /
				this.canvasMaxWidth * this.canvasMaxHeight) : Math.round(res.windowHeight * 0.75);
			this.canvasWidth = Math.round(this.canvasHeight / this.canvasMaxHeight * this.canvasMaxWidth);
			// this.isWeiXin = isWeiXin();
			innerAudioContext.autoplay = true;
			innerAudioContext.loop = true; //循环播放
			innerAudioContext.src = '../../static/飘向北方.mp3';
			innerAudioContext.onPlay(() => {
				console.log('开始播放');
				this.playing = !innerAudioContext.paused;
			});
			innerAudioContext.onError((res) => {
				console.log(res.errMsg);
				console.log(res.errCode);
			});
			console.log(innerAudioContext)
		},
		watch: {
			'playing': function(newd, old) {
				if (this.playing == true) { //继续播放
					innerAudioContext.play();
				} else { //暂停播放
					innerAudioContext.pause();
				}
			},
		},
		computed: {
			hasEmpty() {
				return this.key_res.filter(item => {
					return item.value === ''
				}).length == 0
			},
			noObj() {
				return this.objective == ''
			},
			noKr() {
				return this.key_res.length == 0
			},
			noName() {
				return this.name == ''
			},
			startDate() {
				return this.getDate('start');
			},
			endDate() {
				return this.getDate('end');
			}
		},
		methods: {
			getDate(type) {
				const date = new Date();
				let year = date.getFullYear();
				let month = date.getMonth() + 1;
				let day = date.getDate();

				if (type === 'start') {
					year = year;
				} else if (type === 'end') {
					year = year + 30;
				}
				month = month > 9 ? month : '0' + month;;
				day = day > 9 ? day : '0' + day;
				return `${year}-${month}-${day}`;
			},
			next() {
				this.page++;
				this.checkArray();
			},
			back() {
				this.page--;
			},
			add() {
				this.key_res.push({
					value: ''
				})
			},
			remove(index) {
				this.key_res.splice(index, 1);
			},
			bindChange(e) {
				this.date = e.target.value;
			},
			checkArray() {
				for (let i = 0; i < this.key_res.length; i++) {
					if (this.key_res[i].value === '') {
						this.key_res.splice(i, 1);
					}
				}
			},
			makePic() {
				if (this.objective == '' || this.key_res.length == 0 || this.name == '') {
					uni.showToast({
						title: '请填写完整',
						icon: 'none'
					})
					return
				}
				this.load = true;
				this.page++;
				console.log(this.canvasWidth, this.canvasHeight)

				//生成海报				
				const ctx = uni.createCanvasContext('shareCanvas')
				uni.getImageInfo({
					src: this.background[this.chooseId].src,
					success: (res) => {
						this.draw(res, ctx);
						ctx.draw(false, (ee) => {
							uni.canvasToTempFilePath({
								canvasId: 'shareCanvas',
								success: (ress) => {
									this.load = false;
									this.canvasBase = ress.tempFilePath;
								}
							})
						})
					},
					fail: (res) => {
						uni.showToast({
							title: '加载失败',
							icon: 'none',
							success: () => {
								setTimeout(() => {
									this.page--;
								}, 1000)
							}
						})
					}
				})
			},
			draw(res, ctx) {
				//背景图
				ctx.drawImage(res.path, 0, 0, this.canvasWidth, this.canvasHeight)

				//设置字体
				// ctx.font = ''

				//title
				this.writeText(ctx, 'center', this.background[this.chooseId].deepColor, 60, '我的OKR海报', 300, 124);

				//执行人
				this.writeText(ctx, 'left', this.background[this.chooseId].deepColor, 35, '对象', 42, 225);

				this.writeText(ctx, 'left', '#52616a', 25, this.name, 42, 281);

				//目标
				this.writeText(ctx, 'left', this.background[this.chooseId].deepColor, 35, '目标', 42, 369);

				this.writeText(ctx, 'left', '#52616a', 25, this.objective, 42, 425);

				//关键成果
				this.writeText(ctx, 'left', this.background[this.chooseId].deepColor, 35, '关键成果', 42, 513);

				let top = 569;

				for (let i = 0; i < this.key_res.length; i++) {
					this.writeText(ctx, 'left', '#52616a', 25, (i + 1) + '. ' + this.key_res[i].value, 42, top + 53 * i);
				}

				top = 569 + 53 * this.key_res.length + 48;

				//截止日期
				this.writeText(ctx, 'left', this.background[this.chooseId].deepColor, 35, '截止日期', 42, top);

				this.writeText(ctx, 'left', '#52616a', 25, this.date, 42, top + 56);

			},
			chooseIt(index) {
				this.chooseId = index;
			},
			writeText(ctx, align, color, size, content, left, top) {
				ctx.setTextAlign(align)
				ctx.setFillStyle(color)
				ctx.setFontSize(Math.round(size / this.canvasMaxWidth * this.canvasWidth))
				ctx.fillText(content, Math.round(left / this.canvasMaxWidth * this.canvasWidth), Math.round(top / this.canvasMaxWidth *
					this.canvasWidth))
			}
		}
	}
</script>

<style>
	.playing {
		animation: run 10s linear 0s infinite;
	}
	.keepgo {
		animation-play-state: paused;
	}
	
</style>
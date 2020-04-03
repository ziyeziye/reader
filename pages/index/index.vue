<template>
	<view class="Box" style="">
		<!-- 网页背景开始 -->
		<view class="pagbg anmt" :style="{backgroundColor:pageBg}"></view>
		<view class="zhongMenu" @click="dianjile()"></view>
		<!-- 网页背景结束 -->
		<!-- 带返回键的导航栏开始 -->
		<view class="topBox anmt" :style="{color:fontColor,backgroundColor:menuBg,top:show?'0':'-150px'}">
			<view :style="{height:statusBarHeight}"></view>
			<view style="height: 40upx;"></view>
			<view style="height: 100upx;line-height: 100upx;text-overflow: ellipsis;" class="tMain">
				<image src="../../static/yueduBack.png" class="back" @click="back()"></image>
				<text style="font-size: 24upx;line-height: normal;overflow: hidden;
				display: -webkit-box;-webkit-box-orient: vertical;-webkit-line-clamp: 2;">({{section_title}})</text>
			</view>
		</view>
		<!-- 带返回键的导航栏结束 -->
		<!-- 菜单开始 -->
		<view class="bottomBox anmt" :style="{color:fontColor,backgroundColor:menuBg,bottom:show?'0':'-250px'}">
			<view style="overflow: hidden;">
				<view style="float: left;width: 25%;overflow: hidden;" @click="up_chapter()">
					<view style="float: left;width: 60%;line-height: 70upx;text-align: center;font-size: 24upx;">上一章</view>
				</view>

				<view style="float: left;width: 50%;overflow: hidden;">
					<view style="line-height: 70upx;text-align: center;font-size: 24upx;
					display: -webkit-box;-webkit-box-orient: vertical;-webkit-line-clamp: 1;">{{section_title}}</view>
				</view>

				<view style="float: left;width: 25%;overflow: hidden;" @click="next_chapter()">
					<view style="float: right;width: 60%;line-height: 70upx;text-align: center;font-size: 24upx;">下一章</view>
				</view>
			</view>
			<view style="overflow: hidden;">
				<view style="float: left;width: 50%;overflow: hidden;">
					<view style="float: left;width: 30%;line-height: 70upx;text-align: center;font-size: 24upx;">字体</view>
					<view style="float: left;width: 70%;height: 70upx;display: flex;align-content: center;justify-content: center;">
						<slider style="width: 100%;" :value="size" min="20" max="100" @changing="changeFontSize" @change="changeFontSize" :activeColor="fontColor" :backgroundColor="pageBg" :block-color="fontColor" block-size="20" />
					</view>
				</view>
				<view style="float: left;width: 50%;overflow: hidden;">
					<view style="float: left;width: 30%;line-height: 70upx;text-align: center;font-size: 24upx;">间距</view>
					<view style="float: left;width: 70%;height: 70upx;display: flex;align-content: center;justify-content: center;">
						<slider style="width: 100%;" :value="lineHeight" min="50" max="150" @changing="changeLineHeight" @change="changeLineHeight" :activeColor="fontColor" :backgroundColor="pageBg" :block-color="fontColor" block-size="20" />
					</view>
				</view>
			</view>
			<view style="overflow: hidden;">
				<view style="float: left;width: 15%;line-height: 100upx;text-align: center;font-size: 24upx;">背景</view>
				<view style="float: left;width: 85%;height: 100upx;display: flex;">
					<view class="sekuai" v-for="(item,index) in zhutiList" @tap="qiehuan(index)" :key="item.name" :style="{backgroundColor:item.pageBg,borderColor:dqzhuti==index?item.fontColor:'rgba(0,0,0,0)'}" v-if="index!=1&&index!=2"></view>
				</view>
			</view>
			<view style="width: 100%;display: flex;" class="ddd">
				<view @click="mulu()">
					<view><text class="tficon">&#xe671;</text></view>
					<view>目录</view>
				</view>
				<view @click="qiehuan(dqzhuti==1?0:1)">
					<view><text class="tficon">{{dqzhuti==1?'&#xe699;':'&#xe612;'}}</text></view>
					<view>{{dqzhuti==1?'白天':'夜间'}}</view>
				</view>
				<view @click="qiehuan(dqzhuti==2?0:2)" :style="dqzhuti==2?'color:green':''">
					<view><text class="tficon">&#xe639;</text></view>
					<view>护眼</view>
				</view>
			</view>
		</view>
		<!-- 菜单结束 -->
		<!-- 顶部开始 -->
		<view class="anmt" :style="{color:fontColor,lineHeight:statusBarHeight,backgroundColor:show?menuBg:pageBg,position:'fixed',top:'0',left:'0',zIndex:'6',width:'100%',fontSize:'3vw',zIndex:'20'}">
			<!-- 时间电量开始 -->
			<view :style="{height:statusBarHeight,padding: '0 5vw'}">
				<view style="float: left;letter-spacing:0">
					<text v-text="systemTime"></text>
				</view>
				<view style="float: right;letter-spacing:0;">
					<text class="iconfont icon-80dianliang" style="font-size: 5vw;position: relative;">
						<text class="dianxin" :style="{backgroundColor:show?menuBg:pageBg}">
							<text class="dLiang" :style="{backgroundColor:fontColor,width:battery+'%'}"></text>
						</text>
					</text>
				</view>
			</view>
			<!-- 时间电量结束 -->
			<!-- 书名章节开始 -->
			<view style="height: 40upx;line-height: 40upx;padding: 0 5vw;">
				<view style="float: left;width: 300upx;overflow: hidden;white-space: nowrap;text-overflow: ellipsis;" v-text="bookName"></view>
				<view v-text="section_title" style="float: right;width: 300upx;overflow: hidden;white-space: nowrap;text-overflow: ellipsis;text-align: right;"></view>
			</view>
			<!-- 书名章节结束 -->
		</view>
		<!-- 顶部结束 -->
		<!-- 小说正文开始 -->
		<view class="sview" :style="{paddingTop:'calc('+statusBarHeight+' + 80px)',color:textColor,fontSize:forUpx(size)+'px',lineHeight:forUpx(lineHeight)+'px'}">
			<rich-text :nodes="content_text"></rich-text>
		</view>
		<!-- 小说正文结束 -->
		<!-- 目录 -->
		<view class="slider-mask" @click="sliderShow=false" :class="{'slider-show-mask' :sliderShow}">
			<view class="slider-section" :class="{'slider-show-section' :sliderShow}">
				<ss-sliderbar :isNightTheme="isNight" @chapterItemTap="changeChapter" :currentChapter="currentChapter" :chapterList="chapterList"></ss-sliderbar>
			</view>
		</view>

	</view>
</template>
<script>
	var interval,timeInter,dianliangInter;
	import zhuti from '../../zhuti'
	import Json from '../../Json'
	import ssSliderbar from '../../components/ss-sliderbar/ss-sliderbar.vue'

	export default{
		data(){
			return{
				show:false,//菜单display
				dqzhuti:0,//当前主题
				zhutiList:zhuti.data,//主题列表
				fontColor:'rgb(100,103,120)',//菜单字体颜色
				pageBg:'rgb(247,247,247)',//页面背景色
				menuBg:'#fff',//菜单背景色
				textColor:'#000',//富文本文字颜色
				statusBarHeight: '',
				battery:'',//电量
				systemTime:'',//系统时间
				size:uni.getStorageSync('fontsize')?uni.getStorageSync('fontsize'):40,//正文字体大小
				lineHeight:uni.getStorageSync('lineHeight')?uni.getStorageSync('lineHeight'):70,//正文行间距

				bookName:'',//书名
				id:'',//本书ID
				section_title:'暂无内容',//章节标题
				//正文
				content_text: '',
				sliderShow:false,
				chapterList:[],
				currentChapter: 0, // 当前的章节数 默认从0开始
				isNight: false,
			}
		},
		components: {
			ssSliderbar,
		},
		onUnload() {
			// 页面卸载的时候清除定时器
			clearInterval(timeInter)
			clearInterval(dianliangInter)
			uni.hideLoading();
			//页面卸载的时候将通知栏显示出来
			plus.navigator.setFullscreen(false);
		},
		created() {
			var this_ = this;
			//获取状态栏高度给顶部占位节点
			plus.navigator.setFullscreen(true);
			var zt = uni.getStorageSync('zhuti');//主题索引
			if(zt){
				this.dqzhuti = zt;
				this.fontColor=zhuti.data[zt].fontColor;//菜单字体颜色
				this.pageBg=zhuti.data[zt].pageBg;//页面背景色
				this.menuBg=zhuti.data[zt].menuBg;//菜单背景色
				this.textColor=zhuti.data[zt].textColor;//富文本文字颜色
			}else{
				this.dqzhuti = 0;
				this.fontColor=zhuti.data[0].fontColor;//菜单字体颜色
				this.pageBg=zhuti.data[0].pageBg;//页面背景色
				this.menuBg=zhuti.data[0].menuBg;//菜单背景色
				this.textColor=zhuti.data[0].textColor;//富文本文字颜色
			}
			uni.getSystemInfo({
				success: res=>{
					this.statusBarHeight = res.statusBarHeight + 'px';
				}
			})
		},
		onShow() {
			//页面显示的时候将通知栏隐藏掉
			plus.navigator.setFullscreen(true);
		},
		onHide() {
			//页面隐藏的时候将通知栏显示出来
			plus.navigator.setFullscreen(false);
		},
		onLoad(e) {
			this.id = e.book_id
			this.loadBook(this.id)
			plus.navigator.setStatusBarStyle('dark');
			plus.navigator.setStatusBarBackground('#FF0000');
			this.dianliang();
			this.getTimes();
			//每分钟获取一次电量
			dianliangInter = setInterval(()=>{
				this.dianliang();
			},60000)
			//每秒获取一次时间
			timeInter = setInterval(()=>{
				this.getTimes();
			},1000)
		},
		watch: {
			dqzhuti: function () {
				this.isNight = this.dqzhuti == 0 ? false : true;
			},
		},
		methods:{
			//修改正文字体大小
			changeFontSize(e){
				this.size = e.detail.value;
				uni.setStorageSync('fontsize',e.detail.value);
			},
			//修改正文行间距
			changeLineHeight(e){
				this.lineHeight = e.detail.value;
				uni.setStorageSync('lineHeight',e.detail.value);
			},

			loadBook(id) {
				this.bookName = '斗破苍穹'
				this.chapterList = Json.chapterList;

				let chapterIndex = uni.getStorageSync('book_index_'+this.id);//主题索引
				if (chapterIndex) {
					this.currentChapter = chapterIndex
				}
				this.changeChapter(this.currentChapter)
			},

			mulu(){
				this.show =false
				setTimeout(()=>{
					this.sliderShow = true;
				}, 0.7);
			},
			up_chapter(){
				let length = this.chapterList.length
				if(length>0 && this.currentChapter>0) {
					this.changeChapter(this.currentChapter-1)
				}
			},
			next_chapter(){
				let length = this.chapterList.length
				if(length>0 && this.currentChapter<length-1) {
					this.changeChapter(this.currentChapter+1)
				}
			},
			changeChapter(index) {
				if (this.chapterList.length>0 && this.chapterList[index]) {
					uni.setStorageSync('book_index_'+this.id, index);
					this.currentChapter = index
					this.section_title = this.chapterList[index].title
					this.content_text = '第一章&nbsp;&nbsp;陨落的天才<br>&nbsp;&nbsp;&nbsp;&nbsp;“斗之力，三段！”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;望着测验魔石碑上面闪亮得甚至有些刺眼的五个大字，少年面无表情，唇角有着一抹自嘲，紧握的手掌，因为大力，而导致略微尖锐的指甲深深的刺进了掌心之中，带来一阵阵钻心的疼痛…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“萧炎，斗之力，三段！级别：低级！”测验魔石碑之旁，一位中年男子，看了一眼碑上所显示出来的信息，语气漠然的将之公布了出来…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;中年男子话刚刚脱口，便是不出意外的在人头汹涌的广场上带起了一阵嘲讽的骚动。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“三段？嘿嘿，果然不出我所料，这个“天才”这一年又是在原地踏步！”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“哎，这废物真是把家族的脸都给丢光了。”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“要不是族长是他的父亲，这种废物，早就被驱赶出家族，任其自生自灭了，哪还有机会待在家族中白吃白喝。”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“唉，昔年那名闻乌坦城的天才少年，如今怎么落魄成这般模样了啊？”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“谁知道呢，或许做了什么亏心事，惹得神灵降怒了吧…”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;周围传来的不屑嘲笑以及惋惜轻叹，落在那如木桩待在原地的少年耳中，恍如一根根利刺狠狠的扎在心脏一般，让得少年呼吸微微急促。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;少年缓缓抬起头来，露出一张有些清秀的稚嫩脸庞，漆黑的眸子木然的在周围那些嘲讽的同龄人身上扫过，少年嘴角的自嘲，似乎变得更加苦涩了。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“这些人，都如此刻薄势力吗？或许是因为三年前他们曾经在自己面前露出过最谦卑的笑容，所以，如今想要讨还回去吧…”苦涩的一笑，萧炎落寞的转身，安静的回到了队伍的最后一排，孤单的身影，与周围的世界，有些格格不入。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“下一个，萧媚！”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;听着测验人的喊声，一名少女快的人群中跑出，少女刚刚出场，附近的议论声便是小了许多，一双双略微火热的目光，牢牢的锁定着少女的脸颊…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;少女年龄不过十四左右，虽然并算不上绝色，不过那张稚气未脱的小脸，却是蕴含着淡淡的妩媚，清纯与妩媚，矛盾的集合，让得她成功的成为了全场瞩目的焦点…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;少女快步上前，小手轻车熟路的触摸着漆黑的魔石碑，然后缓缓闭上眼睛…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;在少女闭眼片刻之后，漆黑的魔石碑之上再次亮起了光芒…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“斗之气：七段！”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“萧媚，斗之气：七段！级别:高级！”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“耶！”听着测验员所喊出的成绩，少女脸颊扬起了得意的笑容…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“啧啧，七段斗之气，真了不起，按这进度，恐怕顶多只需要三年时间，她就能称为一名真正的斗者了吧…”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“不愧是家族中种子级别的人物啊…”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;听着人群中传来的一阵阵羡慕声，少女脸颊上的笑容更是多了几分，虚荣心，这是很多女孩都无法抗拒的诱惑…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;与平日里的几个姐妹互相笑谈着，萧媚的视线，忽然的透过周围的人群，停在了人群外的那一道孤单身影上…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;皱眉思虑了瞬间，萧媚还是打消了过去的念头，现在的两人，已经不在同一个阶层之上，以萧炎最近几年的表现，成年后，顶多只能作为家族中的下层人员，而天赋优秀的她，则将会成为家族重点培养的强者，前途可以说是不可限量。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“唉…”莫名的轻叹了一口气，萧媚脑中忽然浮现出三年前那意气风的少年，四岁练气，十岁拥有九段斗之气，十一岁突破十段斗之气，成功凝聚斗之气旋，一跃成为家族百年之内最年轻的斗者！<br><br>&nbsp;&nbsp;&nbsp;&nbsp;当初的少年，自信而且潜力无可估量，不知让得多少少女对其春心荡漾，当然，这也包括以前的萧媚。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;然而天才的道路，貌似总是曲折的，三年之前，这名声望达到巅峰的天才少年，却是突兀的接受到了有生以来最残酷的打击，不仅辛辛苦苦修炼十数载方才凝聚的斗之气旋，一夜之间，化为乌有，而且体内的斗之气，也是随着时间的流逝，变得诡异的越来越少。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;斗之气消失的直接结果，便是导致其实力不断的后退。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;从天才的神坛，一夜跌落到了连普通人都不如的地步，这种打击，让得少年从此失魂落魄，天才之名，也是逐渐的被不屑与嘲讽所替代。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;站的越高，摔得越狠，这次的跌落，或许就再也没有爬起的机会。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“下一个，萧薰儿！”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;喧闹的人群中，测试员的声音，再次响了起来。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;随着这有些清雅的名字响起，人群忽然的安静了下来，所有的视线，豁然转移。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;在众人视线汇聚之处，一位身着紫色衣裙的少女，正淡雅的站立，平静的稚嫩俏脸，并未因为众人的注目而改变分毫。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;少女清冷淡然的气质，犹如清莲初绽，小小年纪，却已初具脱俗气质，难以想象，日后若是长大，少女将会如何的倾国倾城…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;这名紫裙少女，论起美貌与气质来，比先前的萧媚，无疑还要更胜上几分，也难怪在场的众人都是这般动作。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;莲步微移，名为萧薰儿的少女行到魔石碑之前，小手伸出，镶着黑金丝的紫袖滑落而下，露出一截雪白娇嫩的皓腕，然后轻触着石碑…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;微微沉静，石碑之上，刺眼的光芒再次绽放。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“斗之气：九段！级别：高级！”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;望着石碑之上的字体，场中陷入了一阵寂静。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“…竟然到九段了，真是恐怖！家族中年轻一辈的第一人，恐怕非薰儿小姐莫属了。”寂静过后，周围的少年，都是不由自主的咽了一口唾沫，眼神充满敬畏…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;斗之气，每位斗者的必经之路，初阶斗之气分一至十段，当体内斗之气到达十段之时，便能凝聚斗之气旋，成为一名受人尊重的斗者！<br><br>&nbsp;&nbsp;&nbsp;&nbsp;人群中，萧媚皱着浅眉盯着石碑前的紫裙少女，脸颊上闪过一抹嫉妒…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;望着石碑上的信息，一旁的中年测验员漠然的脸庞上竟然也是罕见的露出了一丝笑意，对着少女略微恭声道：“薰儿小姐，半年之后，你应该便能凝聚斗气之旋，如果你成功的话，那么以十四岁年龄成为一名真正的斗者，你是萧家百年内的第二人！”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;是的，第二人，那位第一人，便是褪去了天才光环的萧炎。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“谢谢。”少女微微点了点头，平淡的小脸并未因为他的夸奖而出现喜悦，安静的回转过身，然后在众人炽热的注目中，缓缓的行到了人群最后面的那颓废少年面前…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“萧炎哥哥。”在经过少年身旁时，少女顿下了脚步，对着萧炎恭敬的弯了弯腰，美丽的俏脸上，居然露出了让周围少女为之嫉妒的清雅笑容。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“我现在还有资格让你怎么叫么?”望着面前这颗已经成长为家族中最璀璨的明珠，萧炎苦涩的道，她是在自己落魄后，极为少数还对自己依旧保持着尊敬的人。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“萧炎哥哥，以前你曾经与薰儿说过，要能放下，才能拿起，提放自如，是自在人！”萧薰儿微笑着柔声道，略微稚嫩的嗓音，却是暖人心肺。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“呵呵，自在人？我也只会说而已，你看我现在的模样，象自在人吗？而且…这世界，本来就不属于我。”萧炎自嘲的一笑，意兴阑珊的道。<br><br>&nbsp;&nbsp;&nbsp;&nbsp;面对着萧炎的颓废，萧薰儿纤细的眉毛微微皱了皱，认真的道：“萧炎哥哥，虽然并不知道你究竟是怎么回事，不过，薰儿相信，你会重新站起来，取回属于你的荣耀与尊严…”话到此处，微顿了顿，少女白皙的俏脸，头一次露出淡淡的绯红：“当年的萧炎哥哥，的确很吸引人…”<br><br>&nbsp;&nbsp;&nbsp;&nbsp;“呵呵…”面对着少女毫不掩饰的坦率话语，少年尴尬的笑了一声，可却未再说什么，人不风流枉少年，可现在的他，实在没这资格与心情，落寞的回转过身，对着广场之外缓缓行去…<br><br>&nbsp;&nbsp;&nbsp;&nbsp;站在原地望着少年那恍如与世隔绝的孤独背影，萧薰儿踌躇了一会，然后在身后一干嫉妒的狼嚎声中，快步追了上去，与少年并肩而行…';
				}
			},

			back(){
				uni.navigateBack({});
			},
			dianjile(){
				this.show=!this.show;
			},
			//切换主题
			qiehuan(e){
				this.fontColor=zhuti.data[e].fontColor;//菜单字体颜色
				this.pageBg=zhuti.data[e].pageBg;//页面背景色
				this.menuBg=zhuti.data[e].menuBg;//菜单背景色
				this.textColor=zhuti.data[e].textColor;//富文本文字颜色
				uni.setStorageSync('zhuti',e);
				this.dqzhuti=e;
			},
			//获取系统电量
			dianliang(){
				var this_ = this;
				if (uni.getSystemInfoSync().platform != 'ios'){
					var main = plus.android.runtimeMainActivity();
					var Intent = plus.android.importClass('android.content.Intent');
					var recevier = plus.android.implements('io.dcloud.feature.internal.reflect.BroadcastReceiver', {
						onReceive: function(context, intent) {
							var action = intent.getAction();
							if (action == Intent.ACTION_BATTERY_CHANGED) {
								var level   = intent.getIntExtra("level", 0); //电量
								var voltage = intent.getIntExtra("voltage", 0); //电池电压
								var temperature = intent.getIntExtra("temperature", 0); //电池温度
								//如需获取别的，在这里继续写，此代码只提供获取电量
								this_.battery = level;
							}
						}
					});
					var IntentFilter = plus.android.importClass('android.content.IntentFilter');
					var filter = new IntentFilter(Intent.ACTION_BATTERY_CHANGED);
					main.registerReceiver(recevier, filter);
				}else{
					var UIDevice = plus.ios.import("UIDevice");
					var dev = UIDevice.currentDevice();
					if (!dev.isBatteryMonitoringEnabled()) {
						dev.setBatteryMonitoringEnabled(true);
					}
					var level =dev.batteryLevel();
					this_.battery = level*100;
				}
			},
			getTimes(){
				var times = new Date();
				this.systemTime = (times.getHours()<10?'0'+times.getHours():times.getHours()) + ':' + (times.getMinutes()<10?'0'+times.getMinutes():times.getMinutes());
			},
			forUpx(e){
				return uni.upx2px(e)
			}
		}
	}
</script>
<style>
	@font-face {font-family: "iconfont";
		src: url('../../font/dianliang.ttf') format('truetype');
	}
	.iconfont {
		font-family: "iconfont" !important;
		font-style: normal;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
	}
	.icon-80dianliang:before {
		content: "\e617";
	}
	.pagbg{
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
	}
	.zuizhong.active{
		opacity: 1;
		position: fixed;
		width: 100upx;
		text-align: center;
		font-size: 30upx;
		font-weight: bold;
		color: #ec706b;
		transform: translateX(50%) scale(0.7);
		right: 80upx;
		-webkit-transition: all 0.5s;
		z-index: 9
	}
	.zuizhong{
		opacity: 0;
		position: fixed;
		width: 100upx;
		text-align: center;
		font-size: 20upx;
		font-weight: bold;
		color: #ec706b;
		right: 80upx;
		transform: translateX(50%)  scale(0.1);
		-webkit-transition: all 0.5s;
		transition: all 0.5s;
		z-index: 9
	}
	.quanquan{
		-webkit-box-shadow: rgba(0,0,0,0.12) 0px 3px 13px 1px;
		box-shadow: rgba(0,0,0,0.12) 0px 3px 13px 1px;
		position: fixed;
		right: 70upx;
		z-index: 9;
		border-radius: 50%;
		overflow: hidden;
		transform: translateX(50%) scale(0.5);
	}
	.back{
		width: 40upx;
		height: 40upx;
		margin: 0 40upx;
	}
	.sview{
		width: calc(100% - 40upx);
		font-size: 45upx;
		line-height: 90upx;
		position: relative;
		text-indent:calc(2em + 12upx);
		margin: 0 auto;
		z-index: 5;
		white-space:normal;
		word-break:break-all;
		word-wrap:break-word;
		overflow: hidden;
		padding: 0 20upx 300upx;
	}
	.titlee{
		width: 100%;
		font-size: 45upx;
		line-height: 65upx;
		position: relative;
		z-index: 5;
		padding: 0 20upx 50upx;
		text-indent:-2.3em;
	}
	.tMain{
		display: flex;
		align-items:center;
	}
	page{
		letter-spacing:6upx;
	}
	.topBox{
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		opacity: 1;
		z-index: 9;
	}
	.bottomBox{
		padding: 0 0 20upx 0;
		position: fixed;
		bottom: 0;
		left: 0;
		width: 100%;
		opacity: 1;
		z-index: 9;
	}
	.bottomBox .ddd>view{
		width: 100%;
		text-align: center;
		font-size: 24upx;
		line-height: 40upx;
	}
	.bottomBox .ddd image{
		width: 40upx;
		height: 40upx;
	}
	@font-face {font-family: "ydiconfont";
		src: url('https://at.alicdn.com/t/font_1282539_9h0uwv1sxps.ttf') format('truetype'); /* chrome, firefox, opera, Safari, Android, iOS 4.2+ */
	}
	.tficon{
		font-family: ydiconfont;
		font-size: 34upx;
	}
	.guanggao{
		position: fixed;
		bottom: 0;
		left: 0;
		z-index: 6;
		width: 100%;
	}
	.dianxin{
		position: absolute;
		top: 32.5%;
		left: 11%;
		width: 72%;
		height: 35%;
	}
	.dLiang{
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		display: block;
	}
	.anmt{
		transition: all 0.5s;
	}
	.sekuai{
		width: 150upx;
		height: 100upx;
		background-color: #EC706B;
		border-radius: 12upx;
		border: 5upx solid #000;
		transform: scale(0.4);
		margin: -10upx -35upx 0;
	}
	.zhongMenu{
		top: 50%;
		position: fixed;
		left: 0;
		width: 100%;
		height: 50%;
		transform: translateY(-50%);
		z-index: 9;
	}
</style>


<style lang="scss">
	.slider-mask {
		position: fixed;
		top: 0upx;
		left: 0upx;
		bottom: 0upx;
		right: 0upx;
		display: flex;
		transition: .7s;
		background-color: rgba(0, 0, 0, 0);
		visibility: hidden;
		animation-delay: .2s;
		z-index: 99;

		.slider-section {
			height: 100%;
			width: 70%;
			transform: translateX(-100%);
			transition: .7s;
		}

		.slider-show-section {
			transform: translateX(0);
		}
	}

	.slider-show-mask {
		visibility: visible;
		background-color: rgba(0, 0, 0, 0.5);
	}
</style>
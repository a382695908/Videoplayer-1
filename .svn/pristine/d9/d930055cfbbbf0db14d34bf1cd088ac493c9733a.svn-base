package
{
	import com.hurlant.crypto.symmetric.NullPad;
	
	import flash.display.SimpleButton;
	import flash.display.Sprite;
	import flash.display.Stage;
	import flash.display.StageAlign;
	import flash.display.StageDisplayState;
	import flash.display.StageScaleMode;
	import flash.events.Event;
	import flash.events.MouseEvent;
	import flash.external.ExternalInterface;
	import flash.geom.Point;
	import flash.system.Security;
	import flash.system.System;
	import flash.utils.clearTimeout;
	import flash.utils.setTimeout;
	
	import org.denivip.osmf.plugins.HLSPluginInfo;
	import org.osmf.containers.MediaContainer;
	import org.osmf.elements.LoadFromDocumentElement;
	import org.osmf.events.AlternativeAudioEvent;
	import org.osmf.events.BufferEvent;
	import org.osmf.events.DisplayObjectEvent;
	import org.osmf.events.LoadEvent;
	import org.osmf.events.MediaFactoryEvent;
	import org.osmf.events.TimeEvent;
	import org.osmf.layout.HorizontalAlign;
	import org.osmf.layout.LayoutMetadata;
	import org.osmf.layout.LayoutMode;
	import org.osmf.layout.ScaleMode;
	import org.osmf.layout.VerticalAlign;
	import org.osmf.media.MediaElement;
	import org.osmf.media.MediaFactory;
	import org.osmf.media.MediaPlayer;
	import org.osmf.media.MediaPlayerSprite;
	import org.osmf.media.PluginInfoResource;
	import org.osmf.media.URLResource;
	import org.osmf.media.videoClasses.VideoSurface;
	import org.osmf.traits.AudioTrait;
	import org.osmf.traits.MediaTraitType;

	public class VideoPlayer extends Sprite
	{
		private var factory:MediaFactory;
		private var player:MediaPlayer;
		private var container:MediaContainer;
		private var resource:URLResource;

		public function VideoPlayer()
		{
			this.addEventListener(Event.ADDED_TO_STAGE, onAddtoStageHandler);
 
			flash.system.Security.allowDomain("*");
			ExternalInterface.addCallback("setVideoSource",setVideoSource);

		}
		/**
		 *设置外部调用接口 
		 * @param sourelist
		 * 
		 */		
		public function setVideoSource(sourelist:Array):void{
			tempList = sourelist;
			bigPlay.visible =false;
			currentVideoSource = tempList[0];
			 
			player.stop();
			doSwitchSource(currentVideoSource);
		}
		private var tempList:Array;
		 
		private function onAddtoStageHandler(event:Event):void
		{
			this.removeEventListener(Event.ADDED_TO_STAGE, onAddtoStageHandler);
			stage.scaleMode=StageScaleMode.NO_SCALE;
			stage.align=StageAlign.TOP_LEFT;
			this.stage.addEventListener(Event.RESIZE, onResizeHandler);
			tempList=ExternalInterface.call("setvideoSource");
			initMedia();
			if (tempList)
			{ 
				currentVideoSource=tempList[0];
			}
			
			initLayout();
			addSplash();

		}

		/**
		 * 舞台发生尺寸变化的时候
		 * @param event
		 *
		 */
		private function onResizeHandler(event:Event):void
		{
			initLayout();
		//	onFullScreenClickHandler(null);
		}

		/**
		 * 初始化界面组件
		 *
		 */
		private function initMedia():void
		{
			factory=new MediaFactory();
			player=new MediaPlayer();
			container=new MediaContainer();
			addChild(container);

			stage.addEventListener(MouseEvent.CLICK, onContainerClickHandler);
			addChild(progressbar);
			progressbar.addEventListener(MouseEvent.CLICK, onProgressHandler);
			addChild(playButton);
			playButton.addEventListener(MouseEvent.CLICK, onPlayClickHandler);
			addChild(playProgress);

			addChild(volButton);
			volButton.volmask.mouseEnabled=false;
			volButton.volSlider.mouseEnabled=false;

			volButton.addEventListener(MouseEvent.CLICK, onVolValueClickHandler);
		 
			addChild(fullScreenButton);
			fullScreenButton.addEventListener(MouseEvent.CLICK, onFullScreenClickHandler);
			 

			addChild(switchSourceButton);
			switchSourceButton.addEventListener(MouseEvent.CLICK, onSwitchVideoSourceHandler);
			addChild(bigPlay); 
			addChild(busyMV);
			bigPlay.visible = false;
			bigPlay.addEventListener(MouseEvent.CLICK, onBigplayClickHandler);
			player.addEventListener(DisplayObjectEvent.DISPLAY_OBJECT_CHANGE, onChangeVideoHandler);

			player.addEventListener(TimeEvent.CURRENT_TIME_CHANGE, onPlayHandler);

			factory.loadPlugin(new PluginInfoResource(new HLSPluginInfo()));
		}

		/**
		 *当在舞台上点击的时候触发，
		 *  从中判断出只有在视频上点击的时候才让暂停和播放生效
		 * @param event
		 *
		 */
		private function onContainerClickHandler(event:MouseEvent):void
		{
			if (event.target is VideoSurface || event.target == stage)
			{
				if(event.currentTarget!=bigPlay){
					onPlayClickHandler(null);
				}
				
				if(event.target != switchSourceButton ){
					switchSourceButton.gotoAndStop(1);
				}
			}

		}
		private var layoutMetadata:LayoutMetadata=new LayoutMetadata();
		private const controlGap:int=1;

		/**
		 * 布局 控制栏按钮
		 *
		 */
		private function initLayout():void
		{
			playButton.width=50;
			playButton.height=25;
			playButton.x=0;
			playButton.y=stage.stageHeight - playButton.height;

			progressbar.width=stage.stageWidth - 310;
			progressbar.height=25;
			progressbar.x=playButton.x + playButton.width + controlGap;
			progressbar.y=stage.stageHeight - progressbar.height;
			if (progressSlideLength == 0)
			{
				progressSlideLength=progressbar.slide.width;
			}
			playProgress.width=120;
			playProgress.height=25;
			playProgress.x=progressbar.x + progressbar.width + controlGap;
			playProgress.y=stage.stageHeight - playProgress.height;



			volButton.width=40;
			volButton.height=25;
			volButton.x=playProgress.x + playProgress.width + controlGap;
			volButton.y=stage.stageHeight - volButton.height;


		/*	screenSizeButton.width=50;
			screenSizeButton.height=25;
			screenSizeButton.x=volButton.x + volButton.width + controlGap;
			screenSizeButton.y=stage.stageHeight - screenSizeButton.height;
*/
			
			fullScreenButton.width=50;
			fullScreenButton.height=25;
			fullScreenButton.x=volButton.x + volButton.width + controlGap;
			fullScreenButton.y=stage.stageHeight - fullScreenButton.height;

			switchSourceButton.labelField.mouseEnabled=false;
			switchSourceButton.labelField.mouseWheelEnabled=false;
			switchSourceButton.width=45;
			switchSourceButton.height=25;
			switchSourceButton.x=fullScreenButton.x + fullScreenButton.width + controlGap;
			switchSourceButton.y=stage.stageHeight - switchSourceButton.height;


			bigPlay.x=(stage.stageWidth - bigPlay.width) * 0.5;
			bigPlay.y=(stage.stageHeight - bigPlay.height) * 0.5;
			
			busyMV.x=bigPlay.x;
			busyMV.y=bigPlay.y;


			container.width=stage.stageWidth;
			container.height=stage.stageHeight;

		}

		/**
		 * 让屏幕中间的繁忙图标消失
		 *
		 */
		private function clearBusyIcon():void
		{
			if (busyMV.visible)
			{
				busyMV.visible=false;


				busyMV.stop();
			}

		}

		/**
		 * 让屏幕中间的  繁忙图标显示出来
		 *
		 */
		private function setBusyIcon():void
		{

			busyMV.visible=true;
			busyMV.play();
		}

		/**
		 *添加屏幕等待界面
		 *
		 */
		private function addSplash():void
		{
			if (currentVideoSource.length > 0)
			{
				playVideo(currentVideoSource);

			}
			clearTimeout(pauseTimeUint);
			pauseTimeUint=setTimeout(splashLayout, 100);
		}
		private var pauseTimeUint:uint=0;

		/**
		 * 截取视频的前面部分用来作为  屏幕开始
		 * */
		private function splashLayout():void
		{

			playButton.gotoAndStop(1);
			if (player.canPause)
			{
				player.pause();
				clearBusyIcon();
				bigPlay.visible=true;

			}
			else
			{
				clearTimeout(pauseTimeUint);
				pauseTimeUint=setTimeout(splashLayout, 100);
			}

		}
		private var currentVideoSource:String="";

		/**
		 * 舞台中间
		 * @param event
		 *
		 */
		private function onBigplayClickHandler(event:MouseEvent):void
		{
			if (player.canPlay)
			{
				onPlayClickHandler(null);
			}
			else
			{
				bigPlay.visible=false;
				if (currentVideoSource.length > 0)
				{
					playVideo(currentVideoSource);
				}
			}
		}

		/**
		 *进度显示
		 * @param event
		 *
		 */
		private function onProgressHandler(event:MouseEvent):void
		{
			
		//	currentPlayTime=player.duration * event.localX / progressSlideLength;
			
			
			currentPlayTime = localToGlobal(new Point(progressbar.mouseX,progressbar.mouseY)).x/progressSlideLength*player.duration;
			//trace(player.duration * event.localX / progressSlideLength,currentPlayTime);
			//	trace(currentPlayTime+"--");
			//	player.seek(currentPlayTime);
			seekTo(currentPlayTime);
			clearBusyIcon();
		}

		/**
		 * 播放按钮点击
		 * @param event
		 *
		 */
		private function onPlayClickHandler(event:MouseEvent):void
		{
			if (player == null)
			{
				return;
			}
			if (player.playing)
			{
				playButton.gotoAndStop(1);
				player.pause();
				bigPlay.visible=true;
			}
			else
			{
				
				if(player.canPlay){
					clearBusyIcon();
					playButton.gotoAndStop(2);
					player.play();
					bigPlay.visible=false;
				}else{
					setBusyIcon();
				}
				
			}
		}

		/**
		 *声音大小控制的按钮被点击
		 * @param event
		 *
		 */
		private function onVolClickHandler(event:MouseEvent):void
		{ 
			if (volButton.currentFrameLabel == "expand")
			{
				if (event.target != volButton.volValueBar)
				{
					volButton.gotoAndStop("unselected");
				}

			}
			else
			{
				volButton.gotoAndStop("expand");
			}



		}
		private var currentVolnumber:uint=0;

		private function onVolValueClickHandler(event:MouseEvent):void
		{

			if (event.localX > volButton.volSlider.x)
			{

				volButton.volmask.width=event.localX - volButton.volSlider.x;
			}
			player.volume=(volButton.volmask.width / volButton.volSlider.width);
		}
		
		/**
		 * 屏幕尺寸控制 
		 * @param event
		 * 
		 */		
		private function onScreenSizeClickHandler(event:MouseEvent):void
		{
		
			//layoutMetadata = player.media.getMetadata(LayoutMetadata.LAYOUT_NAMESPACE) as LayoutMetadata;
			
			
			if(layoutMetadata.scaleMode == ScaleMode.LETTERBOX){
				player.media.removeMetadata(LayoutMetadata.LAYOUT_NAMESPACE);
				 
				setMetaLayout(ScaleMode.ZOOM);
				
			}else{
				player.media.removeMetadata(LayoutMetadata.LAYOUT_NAMESPACE);
				 
				setMetaLayout(ScaleMode.LETTERBOX);
			}
			
	//		player.media.addMetadata(LayoutMetadata.LAYOUT_NAMESPACE, layoutMetadata);
		}
		private function setMetaLayout(scalemodel:String):void{
			
			layoutMetadata.layoutMode=LayoutMode.NONE;
			layoutMetadata.scaleMode=scalemodel;//ScaleMode.LETTERBOX;
			layoutMetadata.percentHeight=100;
			layoutMetadata.percentWidth=100;
			var obj:Object = element.getMetadata(LayoutMetadata.LAYOUT_NAMESPACE);
			if(obj){
				obj.removeValue("scaleMode");
				obj.addValue("scaleMode",scalemodel);
			}
			
			layoutMetadata.scaleMode=scalemodel;
			layoutMetadata.verticalAlign=VerticalAlign.MIDDLE;
			layoutMetadata.horizontalAlign=HorizontalAlign.CENTER;
			element.addMetadata(LayoutMetadata.LAYOUT_NAMESPACE, layoutMetadata);
		}
		/**
		 * 全屏按钮点击
		 * @param event
		 *
		 */
		private function onFullScreenClickHandler(event:MouseEvent):void
		{
			if (stage.displayState == StageDisplayState.FULL_SCREEN)
			{
				stage.displayState=StageDisplayState.NORMAL;
				fullScreenButton.labelField.text = "全屏";
			}
			else
			{
				stage.displayState=StageDisplayState.FULL_SCREEN;
				fullScreenButton.labelField.text = "普屏";
			}
		}

		/**
		 *  当视频发生切换的时候
		 * @param event
		 *
		 */
		private function onChangeVideoHandler(event:DisplayObjectEvent):void
		{ 
			clearTimeout(seekTimeUint);
			seekTimeUint=setTimeout(seekTo, 100, currentPlayTime);
			//	seekTo(currentPlayTime);
		}

		private var seekTimeUint:uint=0;

		/**
		 * 通过延时 不断地寻找可以定点的时刻
		 * @param seektime
		 *
		 */
		private function seekTo(seektime:Number):void
		{

			setBusyIcon();
			clearTimeout(seekTimeUint);
			if (seektime >= player.duration)
			{
				seektime=0;
			}

			if (player.canSeek)
			{
				player.seek(seektime);
				
				if (bigPlay.visible)
				{
					clearBusyIcon();
					if(player.playing){
						bigPlay.visible = false;
					}
					playButton.gotoAndStop(1);
				}else{
					playButton.gotoAndStop(2);
				}
			}
			else
			{
				clearTimeout(seekTimeUint);
				seekTimeUint=setTimeout(seekTo, 100, seektime);
			} 
			if(audioTrait){
				audioTrait.muted=true; 
			}
			
			audioTrait=player.media.getTrait(MediaTraitType.AUDIO) as AudioTrait;
			if(audioTrait){
				audioTrait.muted=false; 
			}
			
		}
		private var audioTrait:AudioTrait;
 

		private function onSwitchVideoSourceHandler(event:MouseEvent):void
		{
			switch (event.target)
			{
				case switchSourceButton:
					if (switchSourceButton.currentFrameLabel == "unselected")
					{
						switchSourceButton.gotoAndStop("expand");
					}
					else
					{
						switchSourceButton.gotoAndStop("unselected");
					}
					;
					break;
				case switchSourceButton.lowButton:
					switchSourceButton.gotoAndStop("unselected");
					switchSourceButton.labelField.text="标清";
					doSwitchSource(tempList[0]);
					break;
				case switchSourceButton.midButton:
					switchSourceButton.gotoAndStop("unselected");
					switchSourceButton.labelField.text="高清";
					doSwitchSource(tempList[1]);
					break;
				case switchSourceButton.highButton:
					switchSourceButton.gotoAndStop("unselected");
					switchSourceButton.labelField.text="超清";
					doSwitchSource(tempList[2]);
					break;

			} 
		}

		private function doSwitchSource(currentVideoSource:String):void
		{
			currentPlayTime=player.currentTime;
			if (currentVideoSource&&currentVideoSource.length > 0)
			{

				if (player.media.hasTrait(MediaTraitType.AUDIO))
				{
					audioTrait=player.media.getTrait(MediaTraitType.AUDIO) as AudioTrait;
					audioTrait.muted=true; 
				}
				playVideo(currentVideoSource);
			}
			else
			{
				return;
			}
			seekTo(currentPlayTime);

		}

		/**
		 * 当 视频播放的时候的监控函数
		 * @param event
		 *
		 */
		private function onPlayHandler(event:TimeEvent):void
		{
			if (isNaN(player.duration) || (event.time == 0))
			{
				playButton.gotoAndStop(1);
				progressbar.slide.width=0;
				//		player.pause();
				formatTime(0, 0);
			}
			else
			{

				clearBusyIcon();
				formatTime(event.time, player.duration);
				progressbar.slide.width=progressSlideLength * (event.time / player.duration);
				if (event.time >= player.duration)
				{
					//	player.pause();
					bigPlay.visible=true;
					playButton.gotoAndStop(1);
				}
			}
		}

		private function formatTime(playedTime:Number, totalTime:Number):void
		{
			var hour:String="00";
			var min:String="00";
			var second:String="00";
			var temp:Number=0;
			temp=Math.floor(playedTime / 3600);
			if (temp < 10)
			{
				hour="0" + temp;
			}
			else
			{
				hour="" + temp;
			}

			temp=Math.floor(playedTime % 3600);
			temp=Math.floor(temp / 60);
			if (temp < 10)
			{
				min="0" + temp;
			}
			else
			{
				min="" + temp;
			}

			temp=Math.floor(playedTime % 60);

			if (temp < 10)
			{
				second="0" + temp;
			}
			else
			{
				second="" + temp;
			}


			playProgress.playedLabel.text=hour + ":" + min + ":" + second;

			temp=Math.floor(totalTime / 3600);
			if (temp < 10)
			{
				hour="0" + temp;
			}
			else
			{
				hour="" + temp;
			}

			temp=Math.floor(totalTime % 3600);
			temp=Math.floor(temp / 60);
			if (temp < 10)
			{
				min="0" + temp;
			}
			else
			{
				min="" + temp;
			}

			temp=Math.floor(totalTime % 60);

			if (temp < 10)
			{
				second="0" + temp;
			}
			else
			{
				second="" + temp;
			}

			playProgress.totalLabel.text=hour + ":" + min + ":" + second;

		}
		/**
		 * 播放按钮
		 */
		private var playButton:PlayButton=new PlayButton();
		/**
		 *进度条
		 */
		private var progressbar:ProgressBar=new ProgressBar();
		/**
		 * 播放进度显示牌
		 */
		private var playProgress:PlayProgress=new PlayProgress();
		/**
		 * 声音控制
		 */
		private var volButton:VolButton=new VolButton();
		/**
		 *视频尺寸控制按钮
		 */
		//private var screenSizeButton:ScreenSizeButton=new ScreenSizeButton();
		/**
		 *全屏按钮
		 */
		private var fullScreenButton:FullScreenButton=new FullScreenButton();

		private var switchSourceButton:SwitchSource=new SwitchSource();
		/**
		 * 屏幕中央的那个播放按钮
		 */
		private var bigPlay:BigPlay=new BigPlay();
		/**
		 *屏幕中间的那个 繁忙动画
		 */
		private var busyMV:BusyMV=new BusyMV();
		private var element:MediaElement;
		private var currentPlayTime:uint=0;
		private var progressSlideLength:uint=0;

		private function playVideo(url:String):void
		{

			resource=new URLResource(url);

			if (element == null)
			{
				element=factory.createMediaElement(resource);
				if (element === null)
				{
					throw new Error("不支持的文件格式，请确认是否是M3U8文件!");
					return;
				}
				container.addMediaElement(element);
				/*************** 视频布局 ,不是关键代码，可忽略 *****************/
				setMetaLayout(ScaleMode.STRETCH);
				/****************************************************/
				player.media=element;
				player.autoRewind=false;
				
			}
			(player.media as LoadFromDocumentElement).resource=(resource);
			volButton.volmask.width=player.volume * volButton.volSlider.width;
			playButton.gotoAndStop(2);
		}
	}
}

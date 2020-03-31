<template>
  <div class="testpage">
      <div class="btn hand-click" @click="startShare">共享屏幕</div>
      <div class="showvideo" id="showShare">

      </div>
  </div>
</template>

<script>
import AgoraRtcEngine from 'agora-electron-sdk'

const APPID = "xxx"
let rtcEngine
export default {
  components: {},
  data() {
    return {
      userInfo:{
		id:123
	  }
    };
  },
  computed: {},
  mounted() {
      this.init()
  },
  beforeDestroy() {
      rtcEngine.stopScreenCapturePreview()
	  rtcEngine.videoSourceRelease()
  },
  destroyed() {
      rtcEngine.stopScreenCapturePreview()
	  rtcEngine.videoSourceRelease()
  },
  watch: {
  
  },
  methods: {
        startShare(){
            // rtcEngine.videoSourceEnableDualStreamMode(true)
            // rtcEngine.videoSourceEnableWebSdkInteroperability(true)
            rtcEngine.videoSourceSetVideoProfile(43, false);
            let  idObj = rtcEngine.getScreenDisplaysInfo()[0]
            console.log(idObj);
            let bb = rtcEngine.videoSourceStartScreenCaptureByScreen(idObj.displayId,
            {   x:0,
                y:0,
                height:0,
                width:0
            },{
                bitrate:500,
                frameRate:15,
                height:0,
                width:0,
            })
            // let aa = rtcEngine.videoSourceUpdateScreenCaptureRegion({top:0,bottom:0,left:0,right:0})
            rtcEngine.startScreenCapturePreview()
            // console.log(aa,bb);
            console.log(bb);
        },
        init(){
            let randomNum = Math.floor(Math.random()*10+1)
            rtcEngine = new AgoraRtcEngine()
            rtcEngine.videoSourceInitialize(APPID)
            rtcEngine.videoSourceSetChannelProfile(1);
            rtcEngine.videoSourceEnableWebSdkInteroperability(true)
            rtcEngine.setVideoRenderDimension(3, 2, 1200, 680);
            //加入频道
            rtcEngine.on('videoSourceJoinedSuccess',(uid)=>{
                console.log('join:'+uid);
                this.videoMagnify2(this.userInfo.id)
            })
            rtcEngine.on('videoSourceLeaveChannel',()=>{
                console.log('leave');
            })
            rtcEngine.videoSourceJoin(null,"sreenShare"+this.userInfo.id,"sreen"+this.userInfo.id,2)
        },
        //渲染画面
        videoMagnify2(memberId) {
            this.$nextTick(() => {
                let videoContainer = document.getElementById('showShare')
                console.log(videoContainer);
                rtcEngine.setupLocalVideoSource(videoContainer)
            })
        }
  }
};
</script>

<style lang="scss" scoped>
.testpage{
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;    
    .btn{
        padding: 5px 10px;
        background: gainsboro;
        margin-bottom: 50px;
    }
    .showvideo{
        width: 500px;
        height: 500px;
        background: red;
    }
}


</style>

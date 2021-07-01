<template>
<div id="vid_info">
    <label for="vid_code">Video({{setId}})</label><br>
    <input type="text" id="vid_code" v-model="v_id"/>
    <button id="load-button" class="blu-button"
        @click="changeVid"
    >Load</button> 
    <input type="text" disabled style="display:none" id="yt-flag" @build="initYT">
    <!--<div id="player"></div>-->
    <iframe id="player"
            width="280" height="160"
            src="https://www.youtube.com/embed/M7lc1UVf-VE?enablejsapi=1"
            frameborder="0"
    ></iframe>
    <label for="startTime">Start({{setStart}})</label><br>
    <input type="number" step="0.01" id="startTime" v-model="startTime">
    <button class="blu-button"
        @click="startTime=currentTime"
    >Mark</button> 
    <label for="duration">Duration({{setDuration}})</label><br>
    <input type="number" step="0.01" id="duration" v-model="duration">
    <button class="blu-button"
        @click="duration=(currentTime-startTime).toFixed(2)"
    >Mark</button> 
    <button class="UI-button"
        @click="startTest"
    >Test Timing</button> 
</div> 
</template>

<script>
export default {
    emits:['timing-set', 'video-set'],
    data(){
        return {
            player: null,
            currentTime: 0.0,
            yt_ready: false,
            yt_update: null,
            v_id: "Uo3fi0c18YM",
            setId: "",
            startTime: 0.0,
            setStart: 0,
            duration: 0.0,
            setDuration: 0,
            inTesting: false,
        };
    }, 
    methods:{
        initYT(){
            this.player = new window.YT.Player("player", {
                width: 280,
                height: 160,
                videoId: "Uo3fi0c18YM",
                events: {
                    onReady: this.onPlayerReady,
                }
            });
        },
        onPlayerReady(event){
            console.log("Player Ready");
            this.yt_ready = true;
            this.player.cueVideoById(this.v_id, 0);
            void(event);
        },
        onTenths(){
            if(!this.yt_ready) return;
            const frame = document.getElementById('player');
            frame.width = 280;
            frame.height = 160;
            this.currentTime = this.player.getCurrentTime().toFixed(2);

            if(this.inTesting){
                if(Number(this.currentTime) > Number(this.startTime) + Number(this.duration))
                    this.player.seekTo(this.startTime);
                if(Number(this.player.getPlayerState()) === 2) this.inTesting = false;
            }
        },
        changeVid(){
            this.$emit('video-set', this.v_id);
            this.setId = this.v_id;
            this.player.loadVideoById(this.v_id, 0);
        },
        startTest(){
            this.inTesting = true;
            this.player.seekTo(this.startTime);
            this.player.playVideo();
            this.$emit('timing-set', Number(this.startTime), Number(this.duration))
            this.setStart = this.startTime;
            this.setDuration = this.duration;
        }
    },
    beforeMount(){
        this.yt_update = setInterval(this.onTenths, 100);
    },
    mounted(){
        var tag = document.createElement('script');

        tag.src = "https://www.youtube.com/iframe_api";
        var firstScriptTag = document.getElementsByTagName('script')[0];
        firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);
    },
    beforeUnmount(){
        clearInterval(this.yt_update);
    }
}

window.onYouTubeIframeAPIReady = function(){
    var flag = document.getElementById("yt-flag");
    const event = new Event('build');
    flag.dispatchEvent(event);
}
</script>

<style scoped>
iframe {
    border: 3px solid white;
    border-radius: 4px;
}   

</style>

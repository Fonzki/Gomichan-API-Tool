<template>
<div id="main" class="full-width">
    <header class="container">
        <h1>Gomichan API Entry Tool</h1> 
    </header> 
    <div class="container">
        <div class="divide">
            <video-info
                @video-set="setLineVideo"
                @timing-set="setLineTiming"
            >
            </video-info> 
            <div id="phrase-info">
                <label for="rawline" class="top-label">Enter Phrases</label>
                <input type="text" id="rawline" v-model="raw_line" class="w90">
                <label for="rtranslation" class="top-label">Translation</label>
                <input type="text" id="rtranslation" class="w90" v-model="line_translation">
                <word-lister
                  :raw_words="raw_words"
                  @wordListUpdate="wordListHandler"
                ></word-lister>
                <role-assigner
                  :raw_matches="raw_matches"
                  @phraseListUpdate="phraseListHandler"
                ></role-assigner>

                <button class="go-button"
                  @click="makeRequest"
                >Make Request</button>
            </div>
        </div>
        <div id="line-view">
          <h1>Line View</h1>
          <line-viewer
            :line="server_line"
          ></line-viewer>
        </div>
    </div>
</div>
</template>

<script>
import VideoInfoMaker from './components/VideoInfoMaker.vue';
import RoleAssigner from './components/RoleAssigner.vue';
import WordLister from './components/WordLister.vue';
import LineViewer from './components/line_view/LineViewer.vue';

export default {
    components:{
        'video-info': VideoInfoMaker,
        'role-assigner':RoleAssigner,
        'word-lister':WordLister,
        'line-viewer': LineViewer,
    },
    data(){
        return {
            API_ROOT: 'http://localhost:5000',
            SOUND_ROOT: 'https://www.fonzki.com/static/eng/',
            phonemes: null,
            words: null,
            raw_line:"",
            raw_matches: [],
            raw_words: [],
            line_translation:"",
            line_video: null,
            line_startTime: -1,
            line_duration: -1,
            phrase_list: null,
            word_list: null,
            sids_list: [],
            server_line: null,
        };
    },
    provide(){
      return{
        app: this,
        phonemes: this.phonemes,
      };
    },
    methods:{
        async getPhonemes(){
            const response = await fetch(this.API_ROOT+'/phonemes');
            const data = await response.json();

            this.phonemes = await data['phonemes'];
        },
        async getSyllables(){
            const response = await fetch(this.API_ROOT+'/syllables');
            const data = await response.json();

            this.syllables = await data['syllables'];
        },
        async getWords(){
            const response = await fetch(this.API_ROOT+'/words');
            const data = await response.json();

            this.words = await data['words'];
        },
        wordListHandler(word_list){
          this.word_list = word_list;
        },
        phraseListHandler(phrase_list){
          this.phrase_list = phrase_list;
          this.replaceOneSyllablePhraseSound();
        },
        replaceOneSyllablePhraseSound(){
          for(var i=0;i<this.raw_matches.length;i++){
            if(this.phrase_list[i].word_count === 1){
              var windex = this.wordIndexFromPhraseIndex(i);
              if(windex === -1) return;
              if(this.word_list[windex].defined) return;
              if(this.word_list[windex].is_one_syllable){
                const sound = this.word_list[windex].sound_link;
                this.phrase_list[i].sound_link = sound;
              }
            }
          }
        },
        wordIndexFromPhraseIndex(pindex){
          var index = 0;

          for(var i=0;i<pindex;i++){
            if(this.phrase_list[i].word_count) return -1;
            index += this.phrase_list[i].word_count;
          }

          return index;
        },
        setLineVideo(videoId){
            this.line_video = videoId;
        },
        setLineTiming(startTime, duration){
            this.line_startTime = startTime;
            this.line_duration = duration;
        },
        async makeRequest(){
          const trimmed_word_list = this.word_list.slice(0,this.raw_words.length);
          const trimmed_phrase_list = this.phrase_list.slice(0,this.raw_matches.length);
          const translation = this.line_translation;
          const startTime = this.line_startTime;
          const duration = this.line_duration;

          var body = {
            word_list: trimmed_word_list,
            phrase_list: trimmed_phrase_list,
            translation: translation,
            video_id: this.line_video,
            start: startTime,
            duration: duration
          };

          const endpoint = "/line/builder";
          var response = await fetch(this.API_ROOT+endpoint,{
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({json: JSON.stringify(body)}),
          });

          var data = await response.json();
          this.server_line = await data;
          console.log(data);
          await this.getWords();
          this.line_translation = "";
        },
        slugify(str) {
            str = str.replace(/^\s+|\s+$/g, ''); // trim
            str = str.toLowerCase();
          
            // remove accents, swap ñ for n, etc
            var from = "àáäâèéëêìíïîòóöôùúüûñç·/_:;";
            var to   = "aaaaeeeeiiiioooouuuunc-----";
            for (var i=0, l=from.length ; i<l ; i++) {
                str = str.replace(new RegExp(from.charAt(i), 'g'), to.charAt(i));
            }

            str = str.replace(/[^a-z0-9 -]/g, '') // remove invalid chars
                .replace(/\s+/g, '-') // collapse whitespace and replace by -
                .replace(/-+/g, '-'); // collapse dashes

            return str;
        },
    },
    watch:{
        raw_line(value){
            const pattern = /[^.]+/g;
            this.raw_matches = value.match(pattern);
            this.raw_words = value.replace(/\./g, " ").split(" ");
            this.watched_phonemes = value;
        },
    },
    async created(){
        await this.getPhonemes();
        await this.getWords();
    },
}
</script>

<style>
html {
    background-color: rgb(50,50,50);
}

body {
    color: rgb(230,230,230);
    font-family: 'Roboto';
}

header {
    padding-top:5%;
}

header h1 {
    font-size: 2.6em;    
}

h1,h2,h3,h4,h5,h6 {
    text-shadow: 4px 4px rgb(30,30,30);
    margin: 0;    
}

.container {
    width: 80%;
    max-width: 960px;
    margin: 0 auto;
}

.top-label {
    display: block;  
}

.divide {
  padding-top: 20px;
  display: flex;
  justify-content: space-around;
}

.divide label{
  font-weight: bold;
  font-size: 1.1em;
}

.divide #phrase-info {
  width: 60%;
  font-size: 1.2em;
}

.role-select {
  display: flex;
  justify-content: flex-end;
  align-items: flex-end;
  width: 92%;
  border-bottom: solid 2px white;
  padding-bottom:9px;
  padding-top:7px;
  margin-bottom: 15px;
}

#assign{
    margin-left: 67%;
    border:3px solid #eee;
    border-radius: 8px;
    color: #eee;
    font-size: .9em;
    font-weight: bold;
    padding:4px 13px;
    background: #0d518c;
}

.role-select span {
  margin-right: auto;
  font-size: 1.1em;
}

.role-select select{
    width:22%;
    font-weight:bold;
    display: inline-block;
    border:3px solid #eee;
    border-radius: 8px;
    padding:4px;
    color: #eee;
    background: #2c2c2c;
}

.go-button{
    border:3px solid #eee;
    border-radius: 8px;
    color: #eee;
    padding:8px 16px;
    font-weight: bold;
    font-size: 1em;
    background: #00b461;
    float:right;
    margin-right: 8%;
}

.go-button:hover{
  cursor: pointer;
  background: #34e594;
}

#phrase-info .w90 {
  width: 90%;
}
/*
.divide div {
  width: 45%;
}
*/

#make-syllables {
    margin-top: 20px;
}

#make-syllables h3 {
    font-size: 1.9em;
}

#make-syllables h4 {
    font-size: 1.7em;
    margin-top:20px;
    margin-bottom: 30px;
}

#make-syllables span {
    display:inline-block;
    font-size: 1.2em;
    margin: 5px;
    padding: 5px 5px;
    border: 3px white solid;
    border-radius: 9px;
    font-weight: bold;
    background-color: #555;
}

#make-syllables span:hover {
    cursor: pointer;
    background-color: dodgerblue;
}

.split{
    display: flex;    
    justify-content: space-around;
    padding-bottom: 30px;
    border-bottom: 3px solid #eee;
}

.threeway {
    display: flex;    
    justify-content: space-around;
    padding-bottom: 30px;
    border-bottom: 3px solid #eee;
}

.threeway div {
    width:30%;    
}

#phoneme-list {
    max-width: 45%;
    max-height: 500px;
    overflow-y:scroll;
}

#syllable-UI {
    width: 45%;
}

.threeway #syllable-UI{
    width: 30%;    
}

#syllable-UI #pending {
    padding: 10px;
    border: solid 4px #7c7c7c;
    border-radius: 4px;
    background-color: #2c2c2c;
    width:100%;
    margin-bottom: 25px;
}

#syllable-UI label {
    font-size: 1.2em;
    font-weight:bold;
    display:inline-block;
    margin-bottom: 5px;
}

#syllable-UI input {
    display: inline-block;
    width: 100%;
    border:3px solid #eee;
    border-radius: 8px;
    font-size: 1.0em;
    padding:4px;
    margin-bottom: 15px;
    color: #eee;
    background: #2c2c2c;
}

input {
    display: inline-block;
    border:3px solid #eee;
    border-radius: 8px;
    font-size: 1.0em;
    padding:4px;
    margin-bottom: 15px;
    color: #eee;
    background: #2c2c2c;
    width:65%;
}

#syllable-UI #word-spelling {
    border-color: gray;
    color: #dfdfdf;
}

#syllable-UI .disabled{
    color: gray;
    display:none;
}

.UI-button {
    margin-top: 10px;
    float:right;    
    border:3px solid #eee;
    border-radius: 8px;
    color: #eee;
    font-size: 1.2em;
    font-weight: bold;
    padding:7px 13px;
    background: #b01414;
}

#load-button {
    margin-left: 5px;
    border:3px solid #eee;
    border-radius: 8px;
    color: #eee;
    font-size: 1.2em;
    font-weight: bold;
    padding:2px 13px;
    background: #0d518c;
}

.blu-button {
    margin-left: 5px;
    border:3px solid #eee;
    border-radius: 8px;
    color: #eee;
    font-size: 1.2em;
    font-weight: bold;
    padding:2px 13px;
    background: #0d518c;
}

.UI-button:hover{
    background: #d72323;
    cursor: pointer;
}

#load-button:hover, .blu-button:hover{
    background: #1978cc;
    cursor: pointer;
}

.rolegrid {
    display: flex;
    justify-content: space-around;
}

.rolegrid div {
    width: 23%;
}

#vid_info {
    width:30%;
}

.wrap_role {
    margin-bottom: 30px;    
}

#main {
  margin-bottom: 150px;
}
</style>

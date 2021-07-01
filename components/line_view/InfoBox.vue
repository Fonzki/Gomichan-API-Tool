<template>
  <div>
    <div id="info-header">
      <div id="top">
        <span id="phrase">{{role.phrase_string}}</span>
        <div id="buttons">
          <button
            @click="playPhrase()"
          >play sound</button>
          <button>close info</button>
        </div>
      </div>
      <span id="note">&gt; {{role.note}}</span>
    </div>
    <div id="words">
      <word-info
        v-for="word_inst in role.phrase.words" 
        :key="word_inst.id"
        :word="word_inst.word"
        :display="word_inst.display"
      ></word-info>
    </div>
  </div>
</template>

<script>
import WordInfo from './WordInfo.vue';
export default {
  props:['role'],
  components:{
    'word-info':WordInfo,
  },
  data(){
    return {
      phrase_sound: null,
    };
  },
  methods:{
    playPhrase(){
      var sound = new Audio(this.role.phrase.sound_link);
      sound.play();
    }
  },
  created(){
  },
}
</script>

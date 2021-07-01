<template>
<div>
<div class="word-syllable">
  <div class="display-syllable">
    <div class="space-right">
      <span>{{display}}</span>
    </div>
    <div class="sound">
      <div v-if="multi_syllable">
        <button 
          v-for="(s_inst, index) in word.syllables" 
          :key="s_inst.id"
          :class="[index === word.stress ? 'stressed': '']"
          @click="playSyllable(s_inst, index === word.stress)"
        >
          {{getSpelling(index)}}
        </button>
      </div>
      <button
        @click="playAll()"
      >全部</button>
    </div>
  </div>
  <div class="hatsuon">
    <div>
      <span>発音ヘルプ: </span>
      /
      <a v-for="(phoneme, index) in makePhonemeList()" 
        :key="index" 
        :href="getPhonemeLink(phoneme)"
        class="phoneme-link"
      >{{phoneme.symbol}}</a>
      /
    </div>
  </div>
</div>
</div>
</template>

<script>
export default {
  props:['word', 'display'],
  data(){
    return {
      phonemes: this.makePhonemeList(),
    };
  },
  computed:{
    multi_syllable(){
      return this.word.syllables.length > 1;
    },
  },
  methods:{
    playAll(){
      console.log(this.word)
      const sound = new Audio(this.word.sound_link);
      sound.play();
    },
    playSyllable(instance, stressed){
      console.log(instance.syllable.sound_link);
      void(stressed);
      const sound = new Audio(instance.syllable.sound_link);
      sound.play();
    },
    getSpelling(index){
      if(!this.word) return "unchi";
      void(index);
      const spelling = this.word.syllables[index].syllable.spelling;
      if(index === this.word.stress)
        return spelling.toUpperCase();
      return spelling;
    },
    makePhonemeList(){
      var phonemes = [];
      this.word.syllables.forEach((s_inst)=>{
        s_inst.syllable.phonemes.forEach((p_inst)=>{
          phonemes.push(p_inst.phoneme);
        });
      });
      return phonemes;
    },
    getPhonemeLink(phoneme){
      if(phoneme.daijiro_link)
        return phoneme.daijiro_link;
      return phoneme.aiueo_link;
    }
  },
  created(){
  }
}
</script>

<style scoped>
.display-syllable {
  display: flex;
  align-items:center;
}

.sound {
  display:flex;
  align-items:center;
}

.phoneme-link {
    color: white;
    padding: 4px;
    margin: 0 4px;
    background-color: rgb(70,70,70);
    font-weight: bold;
    text-decoration: none;
  }
.space-right {
  margin-right: 20px;  
}
</style>

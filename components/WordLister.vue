<template>
<div>
  <h2>Words</h2>
  <div id="words">
    <syllable-assigner
      v-for="(word, index) in raw_words"
      :key="word+index"
      :word="word"
      :index="index"
      :defined="isDefined(word, index)"
      :repeated="isRepeat(word, index)"
      @updateWordSlot="updateHandler"
    ></syllable-assigner>
  </div>
  <!--<button id="assign">Assign Roles</button>-->
</div>
</template>

<script>
import SyllableAssigner from './SyllableAssigner.vue'
export default {
  inject:['app'],
  components:{
    'syllable-assigner': SyllableAssigner,
  },
  emits:['wordListUpdate'],
  props:['raw_words'],
  data(){
    return {
      word_slots: new Array(50),
    };
  },
  methods:{
    isDefined(word, index){
      void(index);
      var slug = this.app.slugify(word);
      const match = this.app.words.filter((w)=>
        w.spelling.toLowerCase() === slug.toLowerCase()
      );

      if(match.length > 0){
        this.word_slots[index] = {
          defined: true,
          repeat: -1,
          display: word,
          id: match[0].id,
          sound_link: match[0].sound_link,
          is_one_syllable: match[0].stress === -1,
        }
        this.$emit('wordListUpdate', this.word_slots);
      }
      return match.length > 0;
    },
    isRepeat(word, index){
      for(var i=0;i<index;i++){
        if(this.raw_words[i] === word){
          return true;
        }
      }
      return false;
    },
    updateHandler(index, word, syllable_count, stress, syllable_slots){
      var slug = this.app.slugify(word);
      var sound_link = this.app.SOUND_ROOT+slug+".mp3";

      if(stress === -1){
        sound_link = syllable_slots[0].sound_link;
      }
      
      this.word_slots[index] = {
        defined: false,
        is_one_syllable: stress === -1,
        repeat: -1,
        spelling: slug,
        display: word,
        sound_link: sound_link,
        stress: stress,
        syllables_to_get: syllable_slots,
        syllable_count: syllable_count,
      }

      for(var i=index;i<this.raw_words.length;i++){
        if(i === index) continue;
        if(this.raw_words[i] === word){
          this.word_slots[i] = {
            defined: false,
            repeat: index,
            display: word
          };
        }
      }
      this.$emit('wordListUpdate', this.word_slots);
    }
  }
}
</script>

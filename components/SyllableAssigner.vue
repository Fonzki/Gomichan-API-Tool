<template>
<div v-if="!defined && !repeated" class="bottom-space">
<div class="controls">
<span>{{word}}</span>
<div class="stacked">
  <label for="count">Count</label>
  <input type="number" name="count" min=1 class="short" 
    v-model.number="count"  
  >
</div>
<div class="stacked">
  <label for="stress">Stress</label>
  <input type="number" name="stress" :min="stressMin" :max="stressMax" class="short" 
    v-model.number="stress"
  >
</div>
</div>
<div class="syllables">
<syllable-box
  v-for="(sc, index) in syllable_count"
  :key="index"
  :isStressed="sc"
  :index="index"
  @displayUpdate="updateHandler"
></syllable-box>
</div>
</div>
<div v-else-if="repeated && !defined" class="repeated">
<span>{{word}}</span>
</div>
<div v-else class="defined">
<span>{{word}}</span>
</div>
</template>

<script>
import SyllableBox from './SyllableBox.vue';
export default {
  components:{
    'syllable-box': SyllableBox
  },
  inject:['app'],
  props:['word', 'defined', 'repeated', 'index'],
  emits:['updateWordSlot'],
  data(){
    return {
      count: 1,
      syllable_count: [false],
      syllable_slots: new Array(20),
      stress: -1,
    };
  },
  computed:{
    stressMin(){
      if(this.count > 1) 
        return 0;
      else 
        return -1;
    },
    stressMax(){
      if(this.count > 1) 
        return this.count-1;
      else 
        return -1;
    },
  },
  watch:{
    count(value){
      if(this.stress >= value)
        this.stress = -1;
      if(value > 1 && this.stress === -1)
        this.stress = 0;
      if(value === 1)
        this.stress = -1;
      this.updateSyllables();
    },
    stress(){
      this.updateSyllables();
    }
  },
  methods:{
    updateSyllables(){
      this.syllable_count = [];
      for(var i=0;i<this.count;i++){
        this.syllable_count.push(false);
      }
      if(this.stress > -1)
        this.syllable_count[this.stress] = true;
    },
    updateHandler(index, phonemes, display){
      var filename = "";
      phonemes.forEach((p)=>{
        filename += p.name;
      });
      filename += ".mp3";

      var file_location = this.app.SOUND_ROOT + filename;

      this.syllable_slots[index] = {
        phonemes: phonemes,
        sound_link: file_location,
        name: display,
      };

      this.$emit('updateWordSlot', Number(this.index), this.word, Number(this.count), Number(this.stress), this.syllable_slots.slice(0,this.count));
    }
  }
}
</script>

<style scoped>
.short{
  width: 80%;
  max-width: 45px;
  margin-bottom: 0;
}
.not-as-short{
  width: 80%;
  max-width: 60px;
  margin-bottom: 0;
}
.space-right {
  margin-right: 10px;
}

.controls {
  display:flex;
  align-items: flex-end;
}

.controls {
  width: 91.5%;
  padding-bottom:5px;
  border-bottom: 2px solid white;
}

.defined, .repeated {
  display: inline;
}

.controls span{
  width:30%;
  font-size: 1.5em;
}

.defined span{
  font-size: 1.5em;
  color: #58d361;
  margin-right: 4px;
}

.repeated span{
  font-size: 1.5em;
  color: #ee5;
  margin-right: 4px;
}

.controls div{
  width: 20%;    
  max-width: 75px;
}

.stacked label{
  display:block;
  font-weight: normal;
  font-size: .9em;
}

.syllables {
  margin-top: 10px;
  display: flex;  
}

.syllables label {
    margin-bottom: 3px;
  }

.syllables div{
  padding-right: 20px;
  }

.bottom-space {
  margin-bottom: 10px;
}
</style>

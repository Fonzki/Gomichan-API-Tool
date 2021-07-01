<template>
<div class="stacked">
  <label for="syllable" :class="{stressed: isStressed}">
    {{betterDisplay}}
  </label>
  <input 
    type="text" 
    name="syllable" 
    class="not-as-short space-right"
    :class="{stressed: isStressed}"
    v-model="entry"
    @keyup.delete="removeLastPhoneme"
  >
</div>
</template>

<script>
export default {
  inject:['app'],
  props:['isStressed', 'index'],
  emits:['displayUpdate'],
  data(){
    return {
      display: "",
      name: "",
      phonemes: [],
      entry: "",
    }
  },
  computed:{
    betterDisplay(){
      var the_name;
      var the_display;

      if(this.display.length)
        the_display = this.display;
      else 
        the_display = "...";

      if(this.name.length)
        the_name = this.name;
      else 
        the_name = "...";
      
      if(this.isStressed)
        the_name = the_name.toUpperCase();
      return the_name + "/ " + the_display;
    }
  },
  watch:{
    entry(value){
      this.fastAddPhoneme(value);

      if(value.includes(" "))
        this.findPhonemeByName(value.trim().toLowerCase());

      if(value.startsWith("/")){
        this.name = value.substring(1).trim().toLowerCase();
        if(value.includes(" ")){
          this.entry = "";
          this.updateDisplay();
        }
      }
    },
  },
  methods:{
    fastAddPhoneme(value){
      value = value.replace(/x/,'schwa');
      const pmatches = this.app.phonemes.filter((p)=>
        p.name.startsWith(value)
      );
      if(pmatches.length === 1){
        this.phonemes.push({
          name: pmatches[0].name,
          symbol: pmatches[0].symbol,
        });
        this.updateDisplay();
        this.entry = "";
      }
    },
    findPhonemeByName(value){
      value = value.replace(/x/,'schwa');
      const pmatches = this.app.phonemes.filter((p)=>
        p.name === value
      );
      if(pmatches.length){
        this.phonemes.push({
          name: pmatches[0].name,
          symbol: pmatches[0].symbol,
        });
        this.updateDisplay();
        this.entry = "";
      }
    },
    removeLastPhoneme(){
      if(!this.entry.length){
        this.phonemes.pop();
        this.updateDisplay();
      }
    },
    updateDisplay(){
      this.display = "";
      this.phonemes.forEach((p)=>{
        this.display += p.symbol;
      });

      // emit displayUpdate w/ phoneme list & display
      this.$emit('displayUpdate', this.index, this.phonemes, this.name);
    }
  }
}
</script>

<style scoped>
.not-as-short{
  width: 80%;
  max-width: 60px;
  margin-bottom: 0;
}
.space-right {
  margin-right: 10px;
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
</style>

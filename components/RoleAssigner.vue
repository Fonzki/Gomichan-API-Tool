<template>
<div>
  <h2>Roles</h2>
  <div id="phrases">
    <role-selector
      v-for="(match, index) in raw_matches"
      :key="match+index"
      :phrase="match"
      :index="index"
      @selectorUpdate="selectorUpdateHandler"
    ></role-selector>
  </div>
  <!--<button id="assign">Assign Roles</button>-->
</div>
</template>

<script>
import RoleSelector from './RoleSelector.vue';
export default {
  components:{
    'role-selector': RoleSelector,
  },
  props:['raw_matches'],
  emits:['phraseListUpdate'],
  inject:['app'],
  data(){
    return {
      phrase_slots: new Array(40),  
    };
  },
  methods:{
    selectorUpdateHandler(index, translation, role){
      var word_count = this.raw_matches[index].split(" ").length;
      const filename = this.app.slugify(this.raw_matches[index]) + ".mp3";
      const sound_link = this.app.SOUND_ROOT + filename;
      this.phrase_slots[index] = {
        display: this.raw_matches[index],
        word_count: word_count,
        sound_link: sound_link,
        role: role,
        translation: translation,
        punctuation: "None",
      }
      this.$emit('phraseListUpdate', this.phrase_slots);
    }
  },
  created(){
    for(var i=0;i<40;i++){
      this.phrase_slots[i] = {
        word_count: 0,
      }
    }
  }
}
</script>

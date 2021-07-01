<template>
<div>
{{line_string}}<br>
{{translation}}<br>
<info-toggler 
  v-for="role in role_phrases" 
  :key="role.id" 
  :role="role"
  @set-toggle="toggleInfo"
></info-toggler>
<info-box
  v-if="phrase_info"
  :role="phrase_info"
></info-box>
</div>
</template>

<script>
import InfoBox from './InfoBox.vue';
import InfoToggler from './InfoToggler.vue';
export default {
  props:['line'],
  components:{
    'info-toggler':InfoToggler,
    InfoBox,
  },
  data(){
    return {
      phrase_info: null,
      count: 0,
    };
  },
  computed:{
    line_string(){
      if(!this.line)
        return "NULL LINE";
      return "PENDING"
    },
    translation(){
      if(!this.line)
        return "NULL";
      return this.line.line.translation
    },
    role_phrases(){
      if(!this.line) return [];
      var roles = this.line.line.role_phrases;
      return roles;
    },
  },
  methods:{
    toggleInfo(info){
      if(this.phrase_info && this.phrase_info.id == info.id) {
        this.phrase_info = null;
        console.log("info closed.")
        return;
      }
      this.phrase_info = info;
      console.log(this.phrase_info.phrase_string)
    },
  },
}
</script>

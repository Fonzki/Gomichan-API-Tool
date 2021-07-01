<template>
    <div class="role-select">
      <span>{{phrase}}</span>
      <input type="text" id="translation" placeholder="translation" v-model="translation">
      <select name="roles" v-model="role">
        <option value="other" selected>0. Other</option>
        <option value="main-subject">1. Main Subject</option>
        <option value="main-verb">2. Main Verb</option>
        <option value="main-object">3. Main Object</option>
        <option value="subject">4. Subject</option>
        <option value="verb">5. Verb</option>
        <option value="object">6. Object</option>
        <option value="subject-mod">7. Subject Modifier</option>
        <option value="verb-mod">8. Verb Modifier</option>
        <option value="object-mod">9. Object Modifier</option>
      </select>
      <!--<button>X</button>-->
    </div>
</template>

<script>
export default {
  props:['phrase', 'index'],
  emits:['selectorUpdate'],
  data(){
    return{
      translation: "",
      role: "other",
    };
  },
  watch:{
    translation(){
      this.$emit('selectorUpdate', this.index, this.translation, this.role);
    },
    role(){
      this.$emit('selectorUpdate', this.index, this.translation, this.role);
    },
  }
}
</script>

<style scoped>
#translation {
  width: 25%;
  margin: 0;
  margin-right: 6px;
  padding-top: 3px;
  font-size: .8em;
}
</style>

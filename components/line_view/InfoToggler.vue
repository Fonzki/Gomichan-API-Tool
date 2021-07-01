<template>
  <span 
    :class="[isInfoSet ? 'opened': '', role_class]"
    @click="$emit('set-toggle', role)"
  >{{phrase_text}}</span>
</template>

<script>
export default {
  props:[
    'role', 'isInfoSet'
  ],
  emits:[
    'set-toggle',
  ],
  computed:{
    phrase_text(){
      if(!this.role) return "PHRASE NULL"
      return this.role.phrase_string;
    },
    role_class(){
      if(!this.role) return ""
      return this.role.role;
    },
  }
}
</script>

<style scoped>
span{
  margin-right: 2px;
  padding: 3px 5px;
  background: rgb(85, 85, 85);
}
span:hover{
  cursor: pointer;
}
</style>

<template>
  <div class="alert alert-primary">
    <h3>Success: {{ stats.success }}</h3>
    <h3>Error: {{ stats.error }}</h3>
    <hr>
    <button class="btn btn-primary"
            @click="$emit('repeatGame')">Repeat the game</button>
    <button class="btn btn-primary"
            @click="$emit('nextLevel')">{{ nextBtn }}</button>
  </div>
</template>

<script>
  export default{
    props: ['stats', 'nextBtnStatus'],
    computed:{
      nextBtn(){
        if (this.stats.success > this.stats.error) {
          this.nextBtnStatus = 'Next level'
        }else {
          this.nextBtnStatus = 'Repeat the level'
        }
        return this.nextBtnStatus
      }
    }
  }
</script>

<style>
</style>

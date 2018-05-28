<template>
  <div class="alert">
    <h2>{{ countDown }}</h2>
    <h3>{{ x }} + {{ y }} = ?</h3>
    <hr>
    <div class="buttons">
      <button class="btn btn-success"
              v-for="number in answers"
              @click="onAnswer(number)">{{number}}</button>
    </div>
  </div>
</template>

<script>
  export default{
    props: ['settings'],
    data(){
      return{
        x: mtRand(this.settings.from,this.settings.to),
        y: mtRand(this.settings.from,this.settings.to),
        countDown: this.settings.time // set default time in new question of the lvl
      }
    },
    computed:{
      good(){ // right answer
        return this.x + this.y
      },
      answers(){
        let res = [this.good]
        while (res.length < this.settings.variants) { // generate wrong answers
          let num = mtRand(this.good - this.settings.range, this.good + this.settings.range)
          if (res.indexOf(num) === -1) { // if not copy
            res.push(num)
          }
        }
        return res.sort(function(){
          return 0.5 - Math.random()
        })
      }
    },
    methods:{
      onAnswer(num){
        if (num == this.good) {
          this.$emit('success')
        }else{
          this.$emit('error', `${this.x} + ${this.y} = ${this.good}!`)
        }
      }
    },
    created(){
      let timer = setInterval( () => {
        if (this.countDown > 0) {
          this.countDown--
        }else {
          clearInterval(timer)
          this.$emit('error', 'Too slow!')
        }
      }, 1000)
    }
  }
  function mtRand(min,max) {
    let diff = max - min;
    return Math.floor(Math.random() * (diff +1)) + min;
  }
</script>

<style scoped>
  h2{
    margin-top: 0;
    margin-bottom: 10px;
  }
  .alert{
    padding-top: 20px;
    background-color: #eee;
    text-align: center;
  }
  .buttons{
    display: flex;
    justify-content: space-around;
  }
</style>

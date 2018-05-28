<template>
  <div class="tranning">
    <h1><b>Infinity HARDCORE</b> math tranning</h1>
    <h4>Level {{ level + 1 }}</h4>
    <hr>
    <div class="progress">
      <div class="progress-bar" :style="progressStyles" role="progressbar" aria-valuenow="0" aria-valuemin="0" aria-valuemax="100"></div>
    </div>
    <div class="box">
      <transition name="flip" mode="out-in">
        <app-start-screen v-if="state == 'start'"
                          @onStart="onStart"></app-start-screen>
        <app-question v-else-if="state == 'question'"
                      @success="onQuestSuccess"
                      @error="onQuestError"
                      :settings="levels[level]"></app-question>
        <app-message v-else-if="state == 'message'"
                     :type="message.type"
                     :text="message.text"
                     @onNext="onNext"></app-message>
        <app-result-screen v-else-if="state == 'result'"
                           :nextBtnStatus="nextBtnStatus"
                           :stats="stats"
                           @repeatGame="onRepeatGame"
                           @nextLevel="onNextLevel"></app-result-screen>
        <div v-else>Unknown state</div>
      </transition>
    </div>
  </div>
</template>

<script>

import AppMessage from './components/Message'
import AppQuestion from './components/Question'
import AppResultScreen from './components/ResultScreen'
import AppStartScreen from './components/StartScreen'
export default {
  components:{
    AppMessage,
    AppQuestion,
    AppResultScreen,
    AppStartScreen
  },
  data(){
    return{
      state: 'start',
      stats:{
        success: 0,
        error: 0
      },
      message:{
        type: '',
        text: ''
      },
      questMax: 3,
      nextBtnStatus: '',
      level: 0,
      levels: [
        {
          from: 10,
          to: 40,
          range: 5,
          variants: 2,
          time: 10
        }
      ]
    }
  },
  computed:{
    questDone(){
      return this.stats.success + this.stats.error
    },
    progressStyles(){
      return{
        width: (this.questDone / this.questMax * 100) + '%'
      }
    },
    setTimer(){
      if (this.levels[this.level - 1].time > 5) { // min time == 5
        return this.levels[this.level - 1].time - 1
      }else{
        return this.levels[this.level - 1].time
      }
    }
  },
  methods:{
    onStart(){
      this.state = 'question'
      this.stats.success = 0
      this.stats.error = 0
    },
    onRepeatGame(){
      this.onStart()
      this.level = 0
    },
    onNext(){
      if (this.questDone < this.questMax) {
        this.state = 'question'
      }else {
        this.state = 'result'
      }
    },
    onQuestSuccess(){
      this.state = 'message'
      this.message.text = 'Good job!'
      this.message.type = 'success'
      this.stats.success++
    },
    onQuestError(msg){
      this.state = 'message'
      this.message.text = msg
      this.message.type = 'warning'
      this.stats.error++
    },
    onNextLevel(){
      if (this.stats.success > this.stats.error) {
        this.level++           // THIS
        this.infiniteLevels() // order
        this.onStart()
      }else {
        this.onStart()
      }
    },
    infiniteLevels(){
      this.levels.push({ // push new lvl
        from: this.levels[this.level - 1].from + 40,
        to: this.levels[this.level - 1].to + 90,
        range: this.levels[this.level - 1].range + 20,
        variants: this.levels[this.level - 1].variants + 1,
        time: this.setTimer
      })
    }
  }
}
</script>

<style scoped>
  .tranning{
    max-width: 700px;
    margin: 20px auto;
  }
  .box{
    margin: 10px 0;
  }
  .progress-bar{
    transition: width 1s;
  }
  .flip-enter{

  }
  .flip-enter-active{
    animation: flipInX 0.3s linear;
  }
  .flip-leave{

  }
  .flip-leave-active{
    animation: flipOutX 0.3s linear;
  }
  @keyframes flipInX {
    from{transform: rotateX(90deg);}
    to{transform: rotateX(0deg);}
  }
  @keyframes flipOutX {
    from{transform: rotateX(0deg);}
    to{transform: rotateX(90deg);}
  }
</style>

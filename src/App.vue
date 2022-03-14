<script setup>
import {ref, onMounted} from 'vue'
const isRecording = ref(false)
// This is how initiate to new recognation Speech
const Recognition = window.SpeechRecognition || window.webkitSpeechRecognition
const sr = new Recognition()
const transcript = ref('')
// End
onMounted(()=>{
  sr.continuous = true
  sr.interimResults = true
  sr.onstart = ()=>{
    console.log('SR started')
    isRecording.value = true
  }
sr.onend=()=>{
  console.log('SR STOPED')
  isRecording.value = false
}

sr.onresult =(event)=>{
  console.log('event',event)
  for(let i = 0; i<event.results.length; i++){
    const result = event.results[i]
    if(result.isFinal) checkForCommand(result)
  }
  //event.results will be a object or list because of that we need to firstly make it an array
  const t =Array.from(event.results).map(result => result[0]).map(result=> result.transcript).join('')
transcript.value = t
console.log('TRANSKRİPT', transcript)
}

const checkForCommand = (result)=>{
  const t = result[0].transcript
  console.log('RESULT',t)
  if(t.includes('stop recording')){
    sr.stop()
  } else if (t.includes('what is the time') || t.includes('what\'s the time')){
    
    sr.stop()
    alert(new Date().toLocaleTimeString())
    setTimeout(()=>{
    sr.start()

    },200)
  }

}


})
const ToggleMic = () =>{
  if (isRecording.value){
    sr.stop()
  }else {
    sr.start()
  }
}
</script>

<template>
  <div class="app">
    <button @click="ToggleMic" :class="`mic`">Record</button>
    <div class="transcript" v-text="this.transcript"></div>
  </div>
</template>

<style>
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body{
  background-color: rgb(53, 4, 99);
  color: #ffff;
}
</style>

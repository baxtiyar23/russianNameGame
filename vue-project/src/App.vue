<script setup lang="ts">
import GameHeader from "./components/GameHeader.vue";
import GameFigure from "./components/GameFigure.vue";
import GameWrongLetters from "./components/GameWrongLetters.vue";
import GameWord from "./components/GameWord.vue";
import GamePopUp from "./components/GamePopUp.vue";
import GameNotification from "./components/GameNotification.vue";
import {computed, ref, watch} from "vue";
import {useRandomWord} from "@/compositions/useRandomWord";
import {useLetters} from "@/compositions/useLetters";



const {word, getRandomWord} = useRandomWord()
const {letters, correctLetters, wrongLetters, isLose, isWin} = useLetters(word)

const notification = ref<InstanceType<typeof GameNotification> | null>(null)
const popup = ref<InstanceType<typeof GamePopUp> | null>(null)

watch(wrongLetters, () => {
  if(isLose.value){
    popup.value?.open('lose')
  }
})
watch(correctLetters, () => {
  if(isWin.value){
    popup.value?.open('win')
  }
})

window.addEventListener('keydown', ({key}) => {
  if (isLose.value || isWin.value){
    return
  }

  if (letters.value.includes(key)){
         notification.value?.open()
    setTimeout(() => notification.value?.close(), 2000)
    return
  }
  if (/[а-яА-ЯёЁ]/.test(key)) {
    letters.value.push(key.toLowerCase())
  }
})

const restart = async () => {
  await getRandomWord()
  letters.value = []
  popup.value?.close()
}
</script>

<template>

  <GameHeader/>
  <div class="game-container">
  <GameFigure :wrong-letters-count="wrongLetters.length"/>
  <GameWrongLetters :wrong-letters="wrongLetters"/>
  <GameWord :word="word" :correct-letters = "correctLetters"/>

  </div>

  <!-- Container for final message -->
<GamePopUp ref="popup" :word="word" @restart="restart"/>

 <GameNotification ref="notification"/>
</template>

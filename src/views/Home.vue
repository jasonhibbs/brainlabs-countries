<template lang="pug">

  main

    p Good luck, Sam

    form(
      v-if="country"
      @submit.prevent="onSubmit"
    )

      label.question
        p What is the capital of:
        p.country {{ country.name }}

      .answer
        input(
          placeholder="Enter your guess"
          spellcheck="false"
          autocapitalize="off"
          autocomplete="off"
          autocorrect="off"
          v-model="answer"
        )
        button(
          :disabled="!answer.length"
        ) Check

    template(v-if="correct === true")
      p That’s right!

    template(v-if="correct === false")
      p Oops, the answer is {{ country.capital }}


</template>
<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'

interface CountryData {
  name: string
  capital: string
}

@Component
export default class Home extends Vue {
  api = 'https://restcountries.eu/rest/v2/all?fields=name;capital'
  answer = ''
  country: CountryData | null = null
  correct: boolean | null = null

  // Lifecycle

  mounted() {
    this.startNewQuestion()
  }

  // Questions

  startNewQuestion() {
    this.answer = ''
    this.correct = null
    this.getRandomCountry()
  }

  checkAnswer() {
    this.correct =
      this.forgivenString(this.answer) ===
      this.forgivenString(this.country.capital)

    setTimeout(() => {
      this.startNewQuestion()
    }, 2000)
  }

  // Utility

  forgivenString(str: string) {
    return str
      .toLowerCase()
      .replace('.', '')
      .replace(',', '')
      .replace('ç', 'c')
      .replace('ē', 'e')
      .replace('é', 'e')
      .replace('ó', 'o')
      .trim()
  }

  getRandomCountry() {
    fetch(this.api)
      .then(response => response.json())
      .then(data => {
        const length = data.length
        const randomIndex = ~~(Math.random() * length)
        const found = data[randomIndex]
        this.country = found.capital ? found : data[randomIndex + 1]
      })
      .catch(error => {
        console.warn(error)
      })
  }

  // Events

  onSubmit() {
    this.checkAnswer()
  }
}
</script>

<template lang="pug">

  main

    template(v-if="streak < 2")
      p Good luck, Sam.

    template(v-else)
      p #[span.emoji 🔥] Nice one Sam, your streak is {{ streak }}!

    form(
      v-if="country"
      @submit.prevent="onSubmit"
    )

      label(for="answer")
        p.question Tell me the capital of:
        p.country {{ country.name }}

      .answer
        input#answer(
          placeholder="Enter your guess"
          spellcheck="false"
          autocapitalize="off"
          autocomplete="off"
          autocorrect="off"
          v-model="answer"
        )
        button(
          :disabled="!answer.length"
        )
          span Check

    template(v-if="correct === true")
      p.result #[span.emoji 🎉] That’s right!

    template(v-if="correct === false")
      p.result #[span.emoji 😞] Oops, it’s {{ country.capital }}


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
  countries: CountryData[] = []
  country: CountryData | null = null
  correct: boolean | null = null
  streak = 0

  // Lifecycle

  async mounted() {
    this.countries = await this.getCountries()
    this.startNewQuestion()
  }

  // Questions

  startNewQuestion() {
    this.answer = ''
    this.correct = null
    this.getRandomCountry()
  }

  checkAnswer() {
    if (!this.country) {
      return
    }

    this.correct =
      this.forgivenString(this.answer) ===
      this.forgivenString(this.country.capital)

    if (this.correct) {
      this.streak++
    } else {
      this.streak = 0
    }

    setTimeout(() => {
      this.startNewQuestion()
    }, 2000)
  }

  // Utility

  async getCountries() {
    return fetch(this.api)
      .then(response => response.json())
      .then(data => {
        return data
      })
      .catch(error => {
        console.warn(error)
      })
  }

  forgivenString(str: string) {
    // I suspect there’s an easier way to do this
    return str
      .toLowerCase()
      .replace('.', '')
      .replace(',', '')
      .replace('ă', 'a')
      .replace('å', 'a')
      .replace('ç', 'c')
      .replace('ē', 'e')
      .replace('é', 'e')
      .replace('ñ', 'n')
      .replace('ó', 'o')
      .replace('ș', 's')
      .replace('ú', 'o')
      .trim()
  }

  getRandomCountry() {
    const length = this.countries.length
    const randomIndex = ~~(Math.random() * length)
    const found = this.countries[randomIndex]
    return (this.country = found.capital
      ? found
      : this.countries[randomIndex + 1])
  }

  // Events

  onSubmit() {
    this.checkAnswer()
  }
}
</script>
<style lang="scss">
@import '@/assets/scss/_util';

main {
  margin: 0 auto;
  width: percentage(343/375);
  max-width: rem(400);
}

.emoji {
  display: inline-block;
  margin-right: em(4);
}

label {
  display: block;
}

.country {
  font-size: rem(24);
  font-weight: 800;
  margin-top: rem(8);
}

.answer {
  display: flex;

  input,
  button {
    appearance: none;
    margin: 0;
    font-size: rem(16);
    line-height: (20/16);
    padding: rem(8) rem(12);
  }

  input {
    flex: auto;
    border: 1px solid var(--color-contrast-20);
    border-right: none;
    border-radius: 0;
    transition: border-color 0.1s, background-color 0.1s, box-shadow 0.1s,
      outline-color 0.1s;
  }

  button {
    background-color: var(--color-key);
    color: var(--color-white);
  }
}
</style>

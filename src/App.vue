<script setup>
import "bootstrap/dist/css/bootstrap.min.css"
import "bootstrap"
import { Tooltip } from "bootstrap"

import { computed, onMounted, reactive } from "vue"

const state = reactive({
  amountOfCards: 14,
  numberRangeFrom: 0,
  numberRangeTo: 100,
  showIndex: true,
  flipMaxOneCard: false,
  moveCards: false,
  numbersSorted: true,
  numberRangeRandom: true,
  numbers: [],
  renderKey: 0,
  flipCounter: 0,
})

onMounted(() => {
  createNumbers()

  // init tooltips
  const tooltipTriggerList = document.querySelectorAll('[data-bs-toggle="tooltip"]')
  const tooltipList = [...tooltipTriggerList].map(tooltipTriggerEl => new Tooltip(tooltipTriggerEl))
})

function calculateNumberRange() {
  let startRange = Math.floor(Math.random() * 1900) - 999 // startRange von -999 bis 899
  let end = startRange + Math.floor(Math.random() * 901) + 100; // endRange von (startRange + 100) bis (startRange + 1000)
  if (end > 999) end = 999
  return [startRange, end]
}

function createNumbers() {
  state.flipCounter = 0

  state.numbers = []

  let startRange, endRange = 0
  if (state.numberRangeRandom) {
    [startRange, endRange] = calculateNumberRange()
  } else {
    startRange = state.numberRangeFrom
    endRange = state.numberRangeTo
  }

  for (let i = 0; i < state.amountOfCards; i++) {
    state.numbers.push({ value: Math.floor(Math.random() * (endRange - startRange + 1)) + startRange, flipped: false })
  }

  if (state.numbersSorted) {
    state.numbers.sort((a, b) => a.value - b.value)
  }

  state.renderKey++
}

const existingNumbers = computed(() => {
  if (state.numbers.length < 2) return [0, 0]

  // return 2 random numbers 
  const numbers = []
  while (numbers.length < 2) {
    let nextIndex = Math.floor(Math.random() * state.numbers.length)
    if (!numbers.includes(state.numbers[nextIndex].value)) {
      numbers.push(state.numbers[nextIndex].value)
    }
  }
  return numbers
})

const notExistingNumbers = computed(() => {
  if (state.numbers.length < 2) return [0, 0]

  // return 2 random numbers 
  const numbers = []
  while (numbers.length < 2) {
    let nextNumber = Math.floor(Math.random() * 2000) - 999
    if (!state.numbers.map((card) => card.value).includes(nextNumber)) {
      numbers.push(nextNumber)
    }
  }
  return numbers
})

function flipCard(card) {
  state.flipCounter++

  if (card.flipped) {
    card.flipped = false
    return
  }

  if (state.flipMaxOneCard) {
    state.numbers.forEach((card) => {
      card.flipped = false
    })
  }
  card.flipped = !card.flipped
}

function resetFlipCounter() {
  state.flipCounter = 0

  //close all cards
  state.numbers.forEach((card) => {
    card.flipped = false
  })
}
</script>

<template>
  <h1>Nummerierte Karten</h1>
  <div id="settingsRow" class="d-flex flex-row justify-content-center">
    <div id="mixBox" class="backgroundBox">
      <h2>Karten neu mischen:</h2>
      <div class="d-flex flex-row">
        <div class="inputBox d-flex flex-row align-items-center me-2">
          <label for="" class="me-3">Zahlenbereich:</label>
          <div class="d-flex flex-column">
            <div class="inputBox mb-1">
              <div class="form-check">
                <input class="form-check-input" type="radio" :value="true" v-model="state.numberRangeRandom"
                  name="radioNumberRange" id="radioNumberRange1">
                <label class="form-check-label" for="radioNumberRange1">
                  Zufälliger Zahlenbereich
                  <span class="badge rounded-circle bg-info" data-bs-toggle="tooltip" data-bs-placement="top"
                    data-bs-custom-class="custom-tooltip"
                    data-bs-title="Zugunsten der Lesbarkeit werden Zufallszahlen zwischen -999 und +999 erstellt. Die Differenz von der kleinsten zur größten Zahl liegt dabei zwischen 100 und 1000.">?</span>
                </label>
              </div>
            </div>

            <div class="inputBox form-check d-flex flex-row align-items-center">
              <div class="form-check">
                <div class="d-flex flex-row align-items-center">
                  <input class="form-check-input" type="radio" :value="false" v-model="state.numberRangeRandom"
                    name="radioNumberRange" id="radioNumberRange2">
                  <div class="d-flex flex-column ms-2">
                    <div class="d-flex flex-row align-items-center">
                      <label for="numberRangeFrom" class="rangeLabel me-2">Von:</label>
                      <input id="numberRangeFrom" class="form-control" type="number" v-model="state.numberRangeFrom"
                        step="1" />
                    </div>
                    <div class="d-flex flex-row align-items-center">
                      <label for="numberRangeTo" class="rangeLabel me-2">Bis:</label>
                      <input id="numberRangeTo" class="form-control" type="number" v-model="state.numberRangeTo"
                        step="1" />
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="d-flex flex-column justify-content-start">
          <div class="inputBox d-flex flex-row align-items-center">
            <label for="inputAmount" class="me-2">Anzahl:</label>
            <input id="inputAmount" class="form-control" type="number" v-model="state.amountOfCards" min="2" max="20"
              step="1" :class="{ 'is-invalid': (state.amountOfCards < 2 || state.amountOfCards > 20) }" />
          </div>
          <div class="inputBox my-2">
            <div class="form-check">
              <input class="form-check-input" type="checkbox" v-model="state.numbersSorted" id="inputSorted">
              <label class="form-check-label" for="inputSorted">Zahlen aufsteigend sortiert</label>
            </div>
          </div>
          <button type="button" class="btn btn-success" @click.prevent="createNumbers()">Mischen!</button>
        </div>
      </div>
    </div>

    <div id="settingsBox" class="backgroundBox ms-4">
      <h2>Anzeige Einstellungen:</h2>
      <div class="d-flex flex-column">
        <div class="d-flex flex-row">
          <div class="inputBox me-2">
            <div class="form-check">
              <input class="form-check-input" type="checkbox" v-model="state.showIndex" id="showIndex">
              <label class="form-check-label" for="showIndex">Zeige Index</label>
            </div>
          </div>
          <div class="inputBox">
            <div class="form-check">
              <input class="form-check-input" type="checkbox" v-model="state.flipMaxOneCard" id="flipMaxOneCard">
              <label class="form-check-label" for="flipMaxOneCard">Maximal 1 Karte umgedreht</label>
            </div>
          </div>
        </div>
        <div class="d-flex flex-row mt-3">
          <button type="button" class="btn btn-outline-success">Füge Pfeil hinzu</button>

          <div class="inputBox ms-2">
            <div class="form-check">
              <input class="form-check-input" type="checkbox" v-model="state.moveCards" id="moveCards">
              <label class="form-check-label" for="moveCards">Karten verschieben</label>
            </div>
          </div>
        </div>
        <div class="d-flex flex-row mt-3">
          <div class="input-group">
            <span class="input-group-text">Umgedrehte Karten:</span>
            <span class="input-group-text"><b>{{ state.flipCounter }}</b></span>
            <button class="btn btn-outline-danger" type="button" id="button-addon1"
              @click.prevent="resetFlipCounter()"><font-awesome-icon icon="fa-regular fa-trash-can" /></button>
          </div>
        </div>
      </div>
    </div>
    <div id="searchBox" class="backgroundBox ms-4">
      <h2>Suchen und Finden:</h2>
      <p>Suche unter den Karten nach den nachfolgend genannten Zahlen oder weise nach, dass sie nicht vorhanden sind,
        während du <b><u>so wenige Karten wie möglich aufdeckst</u></b>.</p>

      <p>Vorhandene Zahlen: <b>{{ existingNumbers[0] }}, {{ existingNumbers[1] }}</b></p>
      <p><u>Nicht</u> vorhandene Zahlen: <b>{{ notExistingNumbers[0] }}, {{ notExistingNumbers[1] }}</b></p>
      <p v-if="state.numbersSorted">
        <i>Übrigens: Man kann jede dieser Zahlen nach maximal {{ Math.ceil(Math.log2(state.numbers.length + 1)) }}
          Schritten
          finden (bzw. nachweisen, dass sie nicht enthalten ist)! Wie? Das sollst du rausfinden 😉</i>
      </p>
    </div>
  </div>


  <div class="d-flex flex-row mt-3" :key="state.renderKey">
    <div class="flip-card shake" v-for="( card, index ) in  state.numbers " :key="card.index"
      @click.prevent="flipCard(card)" :style="{ animationDelay: index * 0.03 + 's' }">
      <div class="flip-card-inner" :class="{ flipped: card.flipped }">
        <div class="flip-card-front">
          <div v-if="state.showIndex">
            Index:<br>
            {{ index }}
          </div>
        </div>
        <div class="flip-card-back">
          <span class="mb-3"><u><b>Wert:</b></u></span>

          <span><strong>{{ card.value }}</strong></span>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
@media screen and (max-width: 1470px) {
  #settingsRow {
    flex-wrap: wrap !important;
  }
}

#mixBox {
  min-width: 648.1px;
}

#settingsBox {
  min-width: 404.5px;
}

#searchBox {
  width: 100%;
}

.rangeLabel {
  width: 2rem;
}

#numberRangeFrom,
#numberRangeTo {
  width: 100px;
}

label {
  user-select: none;
}

@keyframes shake {
  0% {
    transform: rotateY(0deg);
  }

  30% {
    transform: rotate(-5deg);
  }

  60% {
    transform: rotate(5deg);
  }

  100% {
    transform: rotate(0deg);
  }
}

.shake {
  animation: shake 0.5s ease-in-out forwards;
}

.flip-card {
  cursor: pointer;
  background-color: transparent;
  margin: 0.5rem;
  width: 300px;
  height: 200px;
  perspective: 1000px;
  border-radius: 10px;
}

.flip-card:hover .flip-card-inner {
  box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.2);
}

.flip-card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  text-align: center;
  transition: transform 0.6s, box-shadow 0.3s;
  transform-style: preserve-3d;

  border-radius: 10px;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
}

.flip-card .flip-card-inner {
  transform: rotateY(0deg);
}

.flip-card .flip-card-inner.flipped {
  transform: rotateY(-180deg);
}

.flip-card-front,
.flip-card-back {
  position: absolute;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  user-select: none;
  border-radius: 10px;
}

.flip-card-front {

  background-color: #ffba42;
  color: black;
  font-style: italic;
  margin: auto;
}

.flip-card-back {
  background-color: rgb(63, 76, 255);
  color: white;
  transform: rotateY(180deg);
}

h1 {
  text-align: center;
}

#inputAmount {
  width: 6rem;
}

.inputBox {
  padding: 0.5rem;
  border-radius: 10px;
  border: 1px solid #dcdcdc;
}

.backgroundBox {
  background-color: #d4f9da;
  padding: 1rem;
  border-radius: 10px;
  border: 1px solid #dcdcdc;
  margin-bottom: 1rem;
}
</style>

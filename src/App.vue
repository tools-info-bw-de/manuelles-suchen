<script setup>
import "bootstrap/dist/css/bootstrap.min.css"
import "bootstrap"

import { reactive } from "vue"

const state = reactive({
  amountOfCards: 10,
  numberRangeFrom: 0,
  numberRangeTo: 100,
})
</script>

<template>
  <h1>Nummerierte Karten</h1>
  <div class="backgroundBox">
    Karten neu mischen:
    <div class="d-flex flex-row">
      <div class="d-flex flex-column">
        <div class="inputBox d-flex flex-row align-items-center me-3">
          <label for="inputAmount" class="me-2">Anzahl:</label>
          <input id="inputAmount" class="form-control" type="number" v-model="state.amountOfCards" min="1" max="20"
            step="1" />
        </div>
        <div class="inputBox form-check d-flex flex-row align-items-center me-3">
          <input class="form-check-input" type="checkbox" value="" id="inputSorted">
          <label class="form-check-label" for="inputSorted">Sortiert</label>
        </div>
      </div>
      <div class="inputBox d-flex flex-row align-items-center">
        <label for="" class="me-3">Zahlenbereich:</label>
        <div class="d-flex flex-column">
          <div class="form-check text-align-left inputBox">
            <input class="form-check-input" type="radio" name="radioNumberRange" id="radioNumberRange1">
            <label class="form-check-label" for="radioNumberRange1">
              Zufallszahlen
            </label>
          </div>
          <div class="form-check d-flex flex-row align-items-center inputBox">
            <input class="form-check-input" type="radio" name="radioNumberRange" id="radioNumberRange2" checked>
            <div class="d-flex flex-column">
              <div class="d-flex flex-row">
                <label for="numberRangeFrom" class="me-2">Von:</label>
                <input id="numberRangeFrom" class="form-control" type="number" v-model="state.numberRangeFrom" step="1" />
              </div>
              <div class="d-flex flex-row">
                <label for="numberRangeTo" class="me-2">Bis:</label>
                <input id="numberRangeTo" class="form-control" type="number" v-model="state.numberRangeTo" step="1" />
              </div>

            </div>

          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="backgroundBox"></div>
</template>

<style scoped>
.text-align-left {
  text-align: left;
}

.inputBox {
  margin-left: 0.5rem;
  padding: 0.5rem;
  border-radius: 10px;
  border: 1px solid #dcdcdc;
}

.backgroundBox {
  background-color: #f5f5f5;
  padding: 1rem;
  border-radius: 10px;
  border: 1px solid #dcdcdc;
  margin-bottom: 1rem;
}
</style>

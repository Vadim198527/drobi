<template>
    <div id="app">
        <div>
            Инструкция: <br>
            <ol>
                <li>Для ввода щелкните по числу</li>
                <li>Введите число</li>
                <li>Результат генерируется сразу</li>
            </ol>
        </div>
        <p class="wrong-section" v-if="isWrong">
            {{wrongMessage}}
        </p>
        <div class="work-space">
            <div class="calculation">
                <template v-for="(fract, index) in fracts">
                    <template>
                        <app-fract
                                :key="fract.id"
                                :fract=fract
                                :itemsCount="fracts.length"
                                @updateItem="correctUpdatItem"
                                @wrong="onWrong"
                                @cancelWrong="cancelWrong"
                                @deleteItem="deleteItem"
                        ></app-fract>
                        <span v-if="index !== fracts.length - 1" :key="'span' + fract.id">+</span>
                    </template>

                </template>

                <div
                        id="result"
                        class="result"
                        v-if="!isWrong && result.numerator !== result.denomerator && result.denomerator !== 1"
                >
                    <span>=</span>
                    <div class="drobChast">
                        <div class="result_numerator">{{result.numerator}}</div>
                        <div class="result_denomerator">{{result.denomerator}}</div>
                    </div>
                </div>

                <div id="resultNepr" class="result" v-if="!isWrong && result.numerator >= result.denomerator">
                    <span>=</span>
                    <div class="intPart">
                        {{resultNeprav.celayaChast}}
                    </div>
                    <div class="drobChast" v-if="resultNeprav.numerator !== 0">
                        <div class="result_numerator">{{resultNeprav.numerator}}</div>
                        <div class="result_denomerator">{{resultNeprav.denomerator}}</div>
                    </div>
                </div>
            </div>

            <div class="operation">
                <button class="button" @click="addNewElement">Add new element</button>
            </div>
        </div>

    </div>
</template>

<script>
   import Fract from './components/Fract'

   export default {
      computed: {
         result() {
            if (!this.isWrong) {
               const result = this.fracts.reduce(this.sumOfTwo, {numerator: 0, denomerator: 1})
               return this.shorten(result)
            }
            return null
         },

         resultNeprav() {
            if (!this.isWrong) {
               const value = this.result
               const celayaChast = Math.floor(value.numerator / value.denomerator)
               const newNumerator = value.numerator - celayaChast * value.denomerator
               return {
                  celayaChast,
                  numerator: newNumerator,
                  denomerator: value.denomerator
               }
            }
            return null
         },

      },

      data() {
         return {
            currentIdNumber: 3,
            isWrong: false,
            wrongMessage: '',
            fracts: [
               {
                  id: 'item1',
                  numerator: 1,
                  denomerator: 2
               },

               {
                  id: 'item2',
                  numerator: 3,
                  denomerator: 4
               }
            ]
         }
      },

      methods: {
         correctUpdatItem(item) {
            this.isWrong = false
            this.updateItem(item)
         },

         updateItem(item) {
            const currentFract = this.fracts.find(fract => fract.id === item.id)
            currentFract.numerator = item.numerator
            currentFract.denomerator = item.denomerator
         },

         addNewElement() {
            if (this.fracts.length < 5) {
               this.fracts.push({
                  id: `item${this.currentIdNumber++}`,
                  numerator: 5,
                  denomerator: 6
               })
            }
         },

         onWrong(wrongMessage) {
            this.isWrong = true
            this.wrongMessage = wrongMessage
         },

         cancelWrong(){
            this.isWrong = false
         },

         sumOfTwo(item1, item2) {
            const resultNumerator = item1.numerator * item2.denomerator + item1.denomerator * item2.numerator
            const resultDenomerator = item1.denomerator * item2.denomerator
            return {
               numerator: resultNumerator,
               denomerator: resultDenomerator
            }
         },

         shorten(item) {
            let a = item.numerator
            let b = item.denomerator

            while (a !== b) {
               if (a > b) {
                  a = a - b
               } else {
                  b = b - a
               }
            }

            const nod = a
            const result = {
               numerator: item.numerator / nod,
               denomerator: item.denomerator / nod
            }
            return result
         },

         deleteItem(id) {
            const indexItem = this.fracts.findIndex(fract => fract.id === id)
            this.fracts.splice(indexItem, 1)
         }
      },
      components: {
         AppFract: Fract
      }
   }
</script>

<style scoped>
    .wrong-section {
        color: red;
        position: absolute;
        top: 150px;
        left: 20px;
    }

    .work-space{
        margin-left: 300px;
    }

    .calculation {
        display: inline-flex;
        align-items: center;
        margin-top: 100px;
    }

    .intPart {
        margin-left: 10px;
    }



    .result {
        display: inline-flex;
        align-items: center;
        margin-right: 10px;

    }

    .result_numerator {
        border-bottom: 1px solid black;
    }

    .result_numerator, .result_denomerator {
        margin: 0 5px;
        text-align: center
    }

    .operation {
        margin-top: 50px;
    }
</style>

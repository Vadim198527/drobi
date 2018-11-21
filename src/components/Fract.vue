<template>
    <div class="fract">
        <i :class="classNameOfIcon" @click="deleteItem">
            highlight_off
        </i>
        <div :class="classNameNumerator">
            <span id="spanNumerator" class="title" v-if="!inFocusNumerator" @click="onClick">{{fract.numerator}}</span>
            <input
                    id="inputNumerator"
                    class="input"
                    v-else
                    :style="`width:${inputWidth}`"
                    type="text"
                    v-focus
                    @focus="onFocus"
                    @blur="onBlur"
                    @input="onInput"
                    v-model="localNumerator"
            >
        </div>
        <div :class="classNameDenomerator">
            <span id="spanDenomerator" class="title" v-if="!inFocusDenomerator" @click="onClick">{{fract.denomerator}}</span>
            <input
                    id="inputDenomerator"
                    class="input"
                    v-else
                    :style="`width:${inputWidth}`"
                    type="text"
                    v-focus
                    @focus="onFocus"
                    @blur="onBlur"
                    @input="onInput"
                    v-model="localDenomerator"
            >

        </div>
    </div>
</template>

<script>
   export default {
      props: ['fract', 'itemsCount'],
      data() {
         return {
            spanHelp: null,

            numeratorIsWrong: false,
            denomeratorIsWrong: false,

            inFocusNumerator: false,
            inFocusDenomerator: false,
            inputWidth: 0,

            localNumerator: this.fract.numerator,
            localDenomerator: this.fract.denomerator,
         }
      },

      computed: {
         classNameNumerator() {
            return !this.numeratorIsWrong ? 'numerator correct' : 'numerator wrong'
         },

         classNameDenomerator() {
            return !this.denomeratorIsWrong ? 'denomerator correct' : 'denomerator wrong'
         },

         classNameOfIcon() {
            return this.itemsCount <= 2 ? 'material-icons hidden' : 'material-icons'
         }


      },

      directives: {
         focus: {
            inserted: function (el) {
               el.focus()
            }
         }
      },

      methods: {
         isNumber(item) {
            const number = +item
            return !Object.is(number, NaN)
         },
         isInteger(value) {
            const number = +value
            const integerPart = Math.floor(number)
            return integerPart === number
         },
         isInInterval(value) {
            const number = +value
            return number >= 1 && number <= 99
         },
         onClick(e) {
            if (e.target.tagName === 'SPAN') {
               const span = e.target
               this.inputWidth = span.offsetWidth + 'px'
               if (span.id === 'spanNumerator') {
                  this.inFocusNumerator = true
               } else {
                  this.inFocusDenomerator = true
               }
            }
         },

         onFocus(){
            const spanHelp = document.getElementById('cancelWrong')
            if(!spanHelp){
               this.spanHelp = document.createElement('span')
               this.spanHelp.id = 'spanHelp'
               this.spanHelp.style.position = 'absolute'
               this.spanHelp.style.left = '10000px'
               document.body.appendChild(this.spanHelp)
            }
         },

         onBlur(e) {
            const input = e.target
            if (input.id === 'inputNumerator') {
               this.inFocusNumerator = false
               this.numeratorIsWrong = false
            } else {
               this.inFocusDenomerator = false
               this.denomeratorIsWrong = false
            }
            this.$emit('cancelWrong')

            this.localNumerator = this.fract.numerator
            this.localDenomerator = this.fract.denomerator

            document.body.removeChild(this.spanHelp)
            this.spanHelp = null
         },

         onInput(e) {
            const input = e.target
            let value = input.value


            this.spanHelp.innerText = value
            this.inputWidth = getComputedStyle(this.spanHelp).width

            let newFract = null
            if (this.isNumber(value) && this.isInteger(value) && this.isInInterval(value)) {
               if (input.id === 'inputNumerator') {
                  this.numeratorIsWrong = false
               } else {
                  this.denomeratorIsWrong = false
               }
               newFract = {
                  id: this.fract.id,
                  numerator: input.id === 'inputNumerator' ? +value : this.localNumerator,
                  denomerator: input.id === 'inputDenomerator' ? +value : this.localDenomerator
               }
               this.$emit('updateItem', newFract)
            } else {
               if (input.id === 'inputNumerator') {
                  this.numeratorIsWrong = true
               } else {
                  this.denomeratorIsWrong = true
               }

               let wrongMessage = ''
               if (!this.isNumber(value)) {
                  wrongMessage = 'Введено нечисловое значение!'
               } else if (!this.isInteger(value)) {
                  wrongMessage = 'Введено нецелое число!'
               } else if (!this.isInInterval(value)) {
                  wrongMessage = 'Число должно быть в пределах диапазона [1, 99]!'
               }
               this.$emit('wrong', wrongMessage)
            }
         },

         deleteItem() {
            this.$emit('deleteItem', this.fract.id)
         },


      }
   }
</script>

<style scoped>
    .fract {
        position: relative;
        box-sizing: border-box;
        display: inline-block;
        margin: 0 20px;
    }

    .numerator, .denomerator {
        cursor: default;
        text-align: center
    }

    .numerator {
        border-bottom: 1px solid black
    }

    .wrong .input, .wrong .input, .wrong .title, .wrong .title {
        color: red
    }

    .title {
        text-align: center;
    }

    .material-icons {
        position: absolute;
        top: -13px;
        right: -12px;
        font-size: 10px;
        cursor: pointer
    }

    .material-icons.hidden {
        display: none
    }

</style>
<template>
  <main id="app"
    class="h-screen w-screen min-h-500 max-h-750 flex flex-col ai-stretch"
  >
    <section class="flex ai-center px3 overflow-auto bgc-green">
      <h2 class="w-full flex jc-end ai-center break-keep">
        <code v-for="(val, index) in display"
          :class="inputClass(val)"  :key="index"
        >
          {{ numberWithCommas(val) }}
        </code>
      </h2>
    </section>

    <div class="flex">
      <div class="grow-3 basis-30 flex bgc-yellow">
        <AppendItem val="(" @append="addParenthesis"></AppendItem>
        <AppendItem val=")" @append="addParenthesis"></AppendItem>
        <AppendItem val="+|-" @append="plusMinus"></AppendItem>
        <AppendItem val="AC" @append="reset"></AppendItem>
      </div>
      <AppendItem val="+" @append="inputModifier"
        class="grow-1 basis-10 bgc-red"
      ></AppendItem>
    </div>

    <div class="flex bgc-blue">
      <AppendItem val="7" @append="inputNumber"></AppendItem>
      <AppendItem val="8" @append="inputNumber"></AppendItem>
      <AppendItem val="9" @append="inputNumber"></AppendItem>
      <AppendItem val="-" @append="inputModifier"
        class="bgc-red"
      ></AppendItem>
    </div>

    <div class="flex bgc-blue">
      <AppendItem val="4" @append="inputNumber"></AppendItem>
      <AppendItem val="5" @append="inputNumber"></AppendItem>
      <AppendItem val="6" @append="inputNumber"></AppendItem>
      <AppendItem val="/" @append="inputModifier"
        class="bgc-red"
      ></AppendItem>
    </div>

    <div class="flex  bgc-blue">
      <AppendItem val="1" @append="inputNumber"></AppendItem>
      <AppendItem val="2" @append="inputNumber"></AppendItem>
      <AppendItem val="3" @append="inputNumber"></AppendItem>
      <AppendItem val="*" @append="inputModifier"
        class="bgc-red"
      ></AppendItem>
    </div>

    <div class="flex  bgc-blue">
      <AppendItem val="0" @append="addZeros"></AppendItem>
      <AppendItem val="."></AppendItem>
      <AppendItem val="DEL" @append="removeInput"></AppendItem>
      <AppendItem val="=" @append="calculateInputs"
        class="bgc-red"
      ></AppendItem>
    </div>


  </main>
</template>

<script>
import Vue from 'vue';
import AppendItem from './components/AppendItem';

export default {
  name: 'app',

  components: {
    AppendItem
  },

  data () {
    return {
      display: [],
      isModifying: false,
      isInputting: false,
    }
  },

  methods: {
    modifierIndex(val) {
      return ['(', ')', '*', '/', '-', '+'].indexOf(val);
    },

    numberWithCommas(x) {
      const isModifier = this.modifierIndex(x) > -1;
      return isModifier ? x : x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },

    inputClass(val) {
      const index = this.modifierIndex(val);
      const inputClass = [
        ['darkyellow'],
        ['darkyellow'],
        ['darkblue', 'pr1'],
        ['darkorange', 'pr1'],
        ['darkred', 'pr1'],
        ['darkgreen', 'pr1']
      ][index];
      return index > -1 ? inputClass : ['pr1'];
    },

    inputNumber(val) {
      this.isModifying = false;

      if( this.isInputting ) {
        const lastIndex = this.display.length - 1;
        val = this.display[lastIndex] + val;
        Vue.set(this.display, lastIndex, val);
      } else {
        this.display.push(val);
        this.isInputting = true;
      }
    },


    inputModifier(modifier) {

      this.isInputting = false;

      if( this.isModifying ) {
        const lastIndex = this.display.length - 1;
        Vue.set(this.display, lastIndex, modifier);
      } else {
        if(this.display.length) {
          this.display.push(modifier);
          this.isModifying = true;
        }
      }
    },

    calculateInputs() {
      const userInput = this.display.join('');
      const result = new Function('return ' + userInput)();
      this.display = [result];
    },

    reset() {
      this.display = [];
      this.isModifying = false;
      this.isInputting = false;
    },


    plusMinus() {
      const lastIndex = this.display.length - 1;
      const lastInput = this.display[ lastIndex ];
      const isModifier = this.modifierIndex(lastInput) > -1;
      const result = isModifier ? this.display[lastIndex] : -(this.display[lastIndex]);
      Vue.set(this.display, lastIndex, result);
    },

    removeInput() {
      const lastIndex = this.display.length - 1;
      const lastInput = this.display[lastIndex];
      if(lastInput.length === 1) {
        this.display.pop();
        const modifierIndex = this.modifierIndex(lastInput);

        if(modifierIndex > -1) {
          if(modifierIndex >= 2) {
            this.isModifying = false;
          }
        } else {
          this.isInputting = false;
        }

      } else {
        const newString = lastInput.slice(0, -1);
        Vue.set(this.display, lastIndex, newString);
      }
    },

    addParenthesis(paren) {
      this.display.push(paren);
    },

    addZeros(val) {

    },

  }
}
</script>

<style scoped>
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;

  flex: 0 1 600px
}

main > section {
  flex: 2 1 80px
}

main > div {
  flex: 1 1 80px
}

</style>


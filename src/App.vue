<template>
  <main id="app"
    class="h-screen w-screen min-h-500 max-h-750 flex flex-col ai-stretch"
  >
    <section class="flex ai-center px3 overflow-auto">
      <h2 class="w-full flex jc-end ai-center break-keep">
        <code v-for="(val, index) in display"
          class="pr1"  :key="index"
        >
          {{ numberWithCommas(val) }}
        </code>
      </h2>
    </section>

    <div class="flex">
      <div class="grow-3 flex">
        <AppendItem val="("></AppendItem>
        <AppendItem val=")"></AppendItem>
        <AppendItem val="+|-" @append="plusMinus"></AppendItem>
        <AppendItem val="AC" @append="reset"></AppendItem>
      </div>
      <AppendItem val="+" @append="inputModifier"></AppendItem>
    </div>

    <div class="flex">
      <AppendItem val="7" @append="inputNumber"></AppendItem>
      <AppendItem val="8" @append="inputNumber"></AppendItem>
      <AppendItem val="9" @append="inputNumber"></AppendItem>
      <AppendItem val="-" @append="inputModifier"></AppendItem>
    </div>

    <div class="flex">
      <AppendItem val="4" @append="inputNumber"></AppendItem>
      <AppendItem val="5" @append="inputNumber"></AppendItem>
      <AppendItem val="6" @append="inputNumber"></AppendItem>
      <AppendItem val="/" @append="inputModifier"></AppendItem>
    </div>

    <div class="flex">
      <AppendItem val="1" @append="inputNumber"></AppendItem>
      <AppendItem val="2" @append="inputNumber"></AppendItem>
      <AppendItem val="3" @append="inputNumber"></AppendItem>
      <AppendItem val="*" @append="inputModifier"></AppendItem>
    </div>

    <div class="flex">
      <AppendItem val="0" @append="inputNumber"></AppendItem>
      <AppendItem val="."></AppendItem>
      <AppendItem val="DEL"></AppendItem>
      <AppendItem val="=" @append="calculateInputs"></AppendItem>
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
    numberWithCommas(x) {
      const isModifier = ['(', ')', '*', '/', '-', '+'].indexOf(x) !== -1;
      return isModifier ? x : x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },

    inputNumber(val) {
      this.isModifying = false;

      if( this.isInputting ) {
        const lastIndex = this.display.length - 1;
        val = this.display[lastIndex] + val;
        Vue.set(this.display, lastIndex, val);
      } else {
        const nextIndex = this.display.length ? this.display.length : 0;
        Vue.set(this.display, nextIndex, val);
        this.isInputting = true;
      }
    },


    inputModifier(modifier) {

      this.isInputting = false;

      if( this.isModifying ) {
        const lastIndex = this.display.length - 1;
        Vue.set(this.display, lastIndex, modifier);
      } else {
        if( !this.display.length ) {
          return undefined;
        } else {
          const nextIndex = this.display.length;
          Vue.set(this.display, nextIndex, modifier);
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

    removeInput() {

    },

    plusMinus() {
      console.log('...plus||minus', this.display);
      const lastIndex = this.display.length - 1;
      const lastInput = this.display[lastIndex];
      const isModifier = ['(', ')', '*', '/', '-', '+'].indexOf(lastInput) !== -1;
      const result = isModifier ? this.display[lastIndex] : -(this.display[lastIndex]);
      Vue.set(this.display, lastIndex, result);
    }
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


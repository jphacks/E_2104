<template>
    <div class="gameDisplay" :style="style" :class="klass">
        <progress-bar></progress-bar>
        <mondai-string></mondai-string>
        <display-string></display-string>
        <!-- <strings></strings> -->
        <b-container fluid class="bv-example-row">
          <b-row>
            <b-col>
              <b-form-input
              md="10"
              offset-md="1"
              v-model="input"
              placeholder="入力してEnter"
              ref='focusThis'
              @keypress.prevent.enter.exact="enable_submit"
              @keyup.prevent.enter.exact="submit">
              </b-form-input>
            </b-col>
          </b-row>
        </b-container>
    </div>
</template>

<script>
  import Strings from './parts/Strings'
  import DisplayString from './parts/DisplayString'
  import MondaiString from './parts/MondaiString'
  import ProgressBar from './parts/ProgressBar'

  export default {
    name: 'GameDisplay',
    components: {
      Strings,
      DisplayString,
      MondaiString,
      ProgressBar
    },
    data() {
      return {
        style: {},
        klass: [],
        input: "",
        audio: new Audio(require('@/assets/miss.mp3'))
      }
    },
    mounted() {
      window.addEventListener("keydown", this.keyAction);
    },
    computed: {
      missCount() {
        return this.$store.state.missEnter
      }
    },
    watch: {
      missCount() {
        this.klass = ['damaged']
        this.audio.play()
        setTimeout(() => {
          this.klass = []
        }, 200)
      }
    },
    methods: {
      enable_submit() {
        this.can_submit_search = true
      },
      submit() {
        if (!this.can_submit_search) return
        this.$store.commit("check", this.input);
        this.input = '';
        return this.can_submit_search = false;
      },
      keyAction(e) {
        if (e.keyCode == 32) {
          this.$store.commit("start")
          this.$store.commit("choice")
          window.removeEventListener("keydown", this.keyAction);
          this.$refs.focusThis.focus();
          setTimeout(() => {
            this.input = ''
          }, 100)
        }
      }
    }
  }
</script>

<style scoped lang="scss">
    .gameDisplay {
        width: 1000px;
        height: 800px;
        border: 5px solid #CCC;
        margin: 0;
        position: relative;
    }

    .damaged {
        animation: damage;
        animation-duration: .2s;
    }
    @keyframes damage {
        0% {
            background : rgb(255, 0, 0);
            opacity: 0.2;
        }
        100% {
            background : #FFF;
        }
    }

    .input{
      width: 700px;
      height:50px;
      margin: auto;
    }
</style>

<template>
  <section class="hero is-info is-large">
    <div class="hero-head">
      <nav class="navbar">
        <div class="container">
          <div class="navbar-brand">
            <a href="/" class="navbar-item subtitle">TyTyTyTyTyping</a>
          </div>
        </div>
      </nav>
    </div>

    <div class="hero-body">
      <div class="container has-text-centered">
        <section class="section">
          <div class="columns is-centered">
            <div class="column">
              <h1>
                <span
                  v-for="(word, i) in answers"
                  v-bind:key="i"
                  class="title is-1"
                  v-bind:style="currentStyle(i)"
                  >{{ word }}</span
                >
              </h1>
            </div>
          </div>

          <div class="columns is-centered">
            <div class="column is-half">
              <input
                type="text"
                class="input is-large"
                v-bind:class="status"
                @keypress="onKeypress"
                v-model="input"
                v-bind:placeholder="answer"
              />
            </div>
          </div>

          <div class="columns is-centered">
            <div class="column is-half">
              <progress
                class="progress is-success is-small"
                v-bind:value="progress"
                max="100"
              ></progress>
            </div>
          </div>
        </section>
      </div>
    </div>
    <ResultModal
      v-bind:active="modalActive"
      v-bind:miss-types="missTypes"
      v-on:resetEvent="reset"
    ></ResultModal>
  </section>
</template>

<script>
import ResultModal from "./ResultModal";
import Analytics from "@aws-amplify/analytics";
import _ from "lodash";

export default {
  name: "TypingPanel",
  components: {
    ResultModal
  },
  props: {
    msg: String
  },
  data() {
    return {
      answer: "",
      input: "",
      results: [],
      nextIndex: 0,
      status: "is-primary",
      questions: _.shuffle([
        "a || b",
        "a && b",
        "def hello; end",
        "if (A == B)",
        "docker container exec -it app sh",
        "git remote add origin https://github.com/repos/application.git",
        "mysql -u user -p password",
        "git add ."
      ]),
      currentQuestionIndex: 0,
      missTypes: [],
      modalActive: false
    };
  },
  computed: {
    answers() {
      if (this.answer === "" || this.answer == undefined) {
        return "";
      }

      return this.answer.split("");
    },
    isEndOfInput() {
      return this.nextIndex == this.answer.length;
    },
    isLastQuestion() {
      return this.currentQuestionIndex == this.questions.length - 1;
    },
    progress() {
      return (this.currentQuestionIndex / this.questions.length) * 100;
    }
  },
  created() {
    this.answer = this.getNextTarget();
  },
  methods: {
    getNextTarget() {
      return this.questions[this.currentQuestionIndex];
    },
    currentStyle(index) {
      return this.nextIndex == index
        ? "color: red; background-color: #f443364f;"
        : "";
    },
    onKeypress(e) {
      e.preventDefault();

      if (this.isEndOfInput && e.keyCode == 13) {
        Analytics.record({
          name: "typinggame",
          attributes: {
            answer: this.answer,
            failed: this.missTypes.length
          }
        });

        console.log({
          name: "typinggame",
          attributes: {
            answer: this.answer,
            failed: this.missTypes.length
          }
        });

        if (this.isLastQuestion) {
          this.modalActive = true;
        } else {
          this.nextQuestion();
        }
        return;
      }

      if (this.answers[this.nextIndex] == e.key) {
        this.playSound();
        this.nextIndex += 1;
        this.results.push(e.key);
        this.input += e.key;
        this.status = "is-primary";
      } else {
        this.playSound("se/error1.mp3");
        this.status = "is-danger";
        this.missTypes.push(this.answers[this.nextIndex]);
      }
    },
    playSound(sound = "se/type1.mp3") {
      const se = new Audio(sound);
      se.play();
    },
    nextQuestion() {
      this.currentQuestionIndex += 1;
      this.answer = this.getNextTarget();
      this.nextIndex = 0;
      this.input = "";
    },
    reset() {
      this.input = "";
      this.results = [];
      this.nextIndex = 0;
      this.currentQuestionIndex = 0;
      this.missTypes = [];
      this.modalActive = false;
      this.answer = this.getNextTarget();
    }
  }
};
</script>

<style scoped>
.hero {
  min-height: 100vh;
}
</style>

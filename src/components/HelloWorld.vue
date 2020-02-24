<template>
  <div class="hello">
    <h1>
      <span
        v-for="(word, i) in answers"
        v-bind:key="i"
        class="title is-1"
        v-bind:style="currentStyle(i)"
      >{{ word }}</span>
    </h1>
    <h2 class="subtitle is-w">{{ result }}</h2>

    <input
      type="text"
      class="input is-large"
      v-bind:class="status"
      @keypress="onKeypress"
      v-model="input"
      v-bind:placeholder="answer"
    />
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  data() {
    return {
      answer: "def hello; end",
      input: "",
      results: [],
      nextIndex: 0,
      status: "is-primary"
    };
  },
  computed: {
    result() {
      return this.results.join("");
    },
    answers() {
      return this.answer.split("");
    },
    isEndOfInput() {
      return this.nextIndex == this.answer.length;
    }
  },
  methods: {
    currentStyle(index) {
      return this.nextIndex == index ? "color: red" : "";
    },
    onKeypress(e) {
      console.log(e);
      e.preventDefault();

      if (this.isEndOfInput && e.keyCode == 13) {
        alert("finish!!");
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
      }
    },
    playSound(sound = "se/type1.mp3") {
      const se = new Audio(sound);
      se.play();
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>

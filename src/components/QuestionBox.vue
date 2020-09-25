<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>
        <div v-html="decodeHtml(currQuestion.question)"></div>
      </template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="(ans, index) in answers"
          :key="index"
          @click="selectAnswer(index)"
          :class="questionResults(index)"
        >
          <div v-html="decodeHtml(ans)"></div>
        </b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAns"
        :disabled="selectedIndex === null || hasAnswered"
      >
        Submit
      </b-button>
      <b-button @click="next" variant="success" :disabled="indexQ >= 9">
        Next
      </b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    currQuestion: Object,
    next: Function,
    increment: Function,
    indexQ: {
      type: Number,
    },
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      hasAnswered: false,
    };
  },
  computed: {
    answers() {
      let answers = [...this.currQuestion.incorrect_answers];
      answers.push(this.currQuestion.correct_answer);
      return answers;
    },
  },
  watch: {
    currQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.shuffleAnswers();
        this.hasAnswered = false;
      },
    },
  },
  methods: {
    decodeHtml: (html) => {
      var txt = document.createElement("textarea");
      txt.innerHTML = html;
      return txt.value;
    },
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      let answers = [
        ...this.currQuestion.incorrect_answers,
        this.currQuestion.correct_answer,
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currQuestion.correct_answer
      );
    },
    submitAns() {
      let isCorrect = false;

      if (this.selectedIndex === this.correctIndex) isCorrect = true;

      this.increment(isCorrect);
      this.hasAnswered = true;
      console.log(this.indexQ);
    },
    questionResults(index) {
      let results = "";

      if (!this.hasAnswered && this.selectedIndex === index)
        results = "selected";
      else if (this.hasAnswered && this.correctIndex === index)
        results = "correct";
      else if (
        this.hasAnswered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      )
        results = "incorrect";

      return results;
    },
  },
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}

.list-group-item:hover {
  background-color: gold;
  cursor: pointer;
}

.btn {
  margin: 0 5px;
}

.selected {
  background-color: lightblue;
}

.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: lightcoral;
}
</style
>>

<template lang="html">
  <div class="flashcard">
    <!-- <h1> Quiz </h1> -->
    <br>
    <h2 v-if="!showResults && !type"> Question {{ pageNo + 1 }} of {{ questions.length }}.</h2>
    <h2 v-if="!showResults && type"> Your current score is: {{ score }}</h2>
    <p class="please" v-bind:class="message">Please select an answer.</p>
    <flashcards v-bind:class="checkPage(index)" v-if="questions && !showResults" v-for="(question, index) in questions" :question="question" :key="index" ></flashcards>

    <div v-if="!showResults" class="pages">
      <button v-if="pageNo > 0 && !type" v-on:click="changePage('prev')" type="button" name="prevPage">Previous</button>
    <!-- <h2 class="page-no">{{ pageNo + 1 }}</h2> -->
      <button v-if="pageNo < questions.length - 1" v-on:click="changePage('next')" type="button" name="nextPage">Next</button>
      <button v-if="(!showResults && (pageNo === questions.length - 1)) "v-on:click="checkAnswers" type="submit" name="button">Submit Your Answers</button>
    </div>


    <results-view v-if="showResults" :lesson="lesson" :answers="answers" :type="type" :score="score"></results-view>
  </div>

</template>

<script>
import {eventBus} from '@/main.js'
import Flashcards from '@/components/Flashcards.vue';
import Results from '@/components/Results.vue'

export default {
  props: ['lesson', 'type'],
  data() {
    return {
      questions: this.lesson.questions,
      answers: Array(this.lesson.questions.length).fill(''),
      showResults: false,
      pageNo: 0,
      score: 0,
      message: "hideMessage",
    }
  },
  components: {
    'flashcards': Flashcards,
    'results-view': Results
  },
  calculated: {

  },
  methods: {
    checkAnswers() {
      if (this.answers[this.pageNo] !== '') {
        let count = 0;
        this.answers.forEach((answer, index) => {
          if (answer === this.questions[index].correct) {
            count += 1;
          }
        })
        this.message = "hideMessage";
        this.showResults = true;
      } else {
        this.message = "";
      }

    },

    checkPage(index) {
      if(index == this.pageNo) {
        return "show";
      } else {
        return "hidden";
      }
    },

    changePage(changeType) {
      if(changeType==='prev' && this.pageNo != 0) {
        this.pageNo -= 1;
        this.message = "hideMessage";
      }
      else if(changeType==='next' && this.pageNo < this.questions.length - 1) {
        if (this.answers[this.pageNo] !== '')
          {
            // We have a score for the quiz
            if(this.answers[this.pageNo]=== this.questions[this.pageNo].correct){
              this.score += 10;
            } else if(this.type){
              this.showResults = true;
            }
            this.pageNo += 1;
            this.message = "hideMessage";
          }
        else {
          this.message = "";
        }
      }
    }
  },
  mounted() {
    eventBus.$on(`selected-answer`, (answer) => {
      const index = answer.answerIndex;
      this.answers.splice(index, 1, answer.value);
      this.message = "hideMessage";
    });
  }
}
</script>

<style>

  .please {
    font-size: 1.3em;
  }

  h1 {
    color: white;
    padding-top: 42px;
    /* margin: 0px 42px; */
  }

  h2 {
  }

  .page-no {
    margin: 0px 30px;
  }

  .flashcard {
    color: white;
    background-color: rgba(0, 0, 0, 0.7);
    width: 97.8vw;
    border-radius: 10px;
  }

  .game {
    width: 98vw;
    display: flex;
    flex-direction: column;
    border-radius: 10px;
    background-color: rgba(0, 0, 0, 0.7);
  }

  .show {
    visibility: visible;
  }

  .warning-msg {
    color:red;
  }

  .hideMessage {
    visibility: hidden;
  }

  .hidden {
    display: none;
  }

  .pages {
    display: flex;
    justify-content: center;
  }

  button {
    display: inline-block;
    background-color: #f5ce42;
    padding: 10px 20px;
    font-family: sans-serif, Arial;
    font-size: 16px;
    border: 2px solid #444;
    border-radius: 4px;
    width: 250px;
    color: #2C3E50;
    margin: 0px 50px;
    margin-bottom: 15px;
    transition: 0.2s;
  }

  button:hover {
    background-color: white;
    border-color: orange;
    cursor: pointer;
  }

</style>

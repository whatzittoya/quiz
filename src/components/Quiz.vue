/* eslint-disable */
<template>
    <div class="antialiased text-gray-700 bg-slate-900">
        <Timer @update="setTimeEnd()" ></Timer>

        <div class="flex w-full h-screen justify-center pt-12">

            <div class="w-full max-w-xl p-3">
                <h1 class="font-bold text-center text-cyan-300">
                    <span class="text-2xl ">
                        Upstream Computing Quiz
                    </span>
                </h1>
                <div class="bg-slate-100 p-12 rounded-lg shadow-lg w-full mt-8">
                    <div v-if="loaded && idx < count">
                        <p class="text-2xl font-bold">{{ questions[idx]['question'] }}</p>
                        <label v-for="(answer, index) in questions[idx].answers" :key="index" :for="index"
                            class="block mt-4 border border-gray-300 rounded-lg py-2 px-6 text-lg"
                            :class="{ 'hover:bg-gray-50 cursor-pointer': selectedAnswer == '' }, { 'bg-green-200': index == questions[idx].correct_answer && selectedAnswer != '' }, { 'bg-red-200': selectedAnswer == index && index != questions[idx].correct_answer && selectedAnswer != '' }">
                            <input :id="index" type="radio" class="hidden" :value="index" @change="answered($event)"
                                :disabled="selectedAnswer != ''" />
                            {{ answer }}
                        </label>
                        <div class="mt-6 flow-root">
                            <button @click="nextQuestion" v-show="selectedAnswer != '' && idx < count - 1"
                                class="float-right bg-indigo-600 text-white text-sm font-bold tracking-wide rounded-full px-5 py-2">
                                Next &gt;
                            </button>
                            <button @click="showResults" v-show="selectedAnswer != '' && idx == count - 1"
                                class="float-right bg-indigo-600 text-white text-sm font-bold tracking-wide rounded-full px-5 py-2">
                                Finish
                            </button>
                        </div>
                    </div>
                    <div v-else>
                        <h2 class="text-bold text-3xl">Results</h2>
                        <div class="flex justify-start space-x-4 mt-6">
                            <p>
                                Correct Answers:
                                <span class="text-2xl text-green-700 font-bold">{{ correctAnswers }}</span>
                            </p>
                            <p>
                                Wrong Answers:
                                <span class="text-2xl text-red-700 font-bold">{{ wrongAnswers }}</span>
                            </p>
                        </div>
                        <div class="mt-6 flow-root">
                            <button @click="resetQuiz"
                                class="float-right bg-indigo-600 text-white text-sm font-bold tracking-wide rounded-full px-5 py-2">
                                Play again
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
  
<script>
import axios from "axios";
import Timer from "./Timer.vue";
export default {
  name: "Quiz",
  components: {
    Timer,
  },
  data() {
    return {
      idx: 0,
      selectedAnswer: "",
      correctAnswers: 0,
      wrongAnswers: 0,
      count: 3,
      questions: [],
      loaded: false,
      arrAnswer: [0, 0, 0, 0, 0, 0, 0, 0],
    };
  },

  methods: {
    answered(e) {
      this.selectedAnswer = e.target.value;

      if (this.selectedAnswer == this.questions[this.idx].correct_answer) {
        this.correctAnswers++;
        this.arrAnswer[this.idx] = 1;
      } else {
        this.wrongAnswers++;
      }
      if (this.idx == 0) {
        this.initResult();
      } else {
        this.updateResult();
      }
    },
    nextQuestion() {
      this.idx++;
      this.selectedAnswer = "";
      document.querySelectorAll("input").forEach((el) => (el.checked = false));
    },
    showResults() {
      console.log("finish");
      this.idx++;
      this.submitQuizResults();
    },
    resetQuiz() {
      this.idx = 0;
      this.selectedAnswer = "";
      this.correctAnswers = 0;
      this.wrongAnswers = 0;
      //window.location.href = "#/Quiz";
      //   this.$forceUpdate();
      window.location.reload();
    },
    submitQuizResults() {
      //   const resultData = {
      //     total: this.questions.length,
      //     correct: this.correctAnswers,
      //     wrong: this.wrongAnswers,
      //   };
      const resultData = {
        answer: this.arrAnswer.join(","),
        total: this.questions.length,
        correct: this.correctAnswers,
        wrong: this.wrongAnswers,
      };
      axios
        .put("https://www.ghrtmb.my.id/quiz/public/quiz-results", resultData)
        .then((response) => {
          // Handle success, e.g., show a success message
          console.log("Quiz results updated successfully:", response.data);
        })
        .catch((error) => {
          // Handle errors, e.g., show an error message
          console.error("Error updating quiz results:", error);
        });
    },
    initResult() {
      const resultData = {
        answer: this.arrAnswer.join(","),
      };
      axios
        .post("https://www.ghrtmb.my.id/quiz/public/quiz-results", resultData)
        .then((response) => {
          // Handle success, e.g., show a success message
          console.log("Quiz results submitted successfully:", response.data);
        })
        .catch((error) => {
          // Handle errors, e.g., show an error message
          console.error("Error submitting quiz results:", error);
        });
    },
    updateResult() {},
    setTimeEnd() {
      this.idx = this.questions.length;
      this.submitQuizResults();
    },
  },
  created() {
    // Make an API request using Axios
    axios
      .get("https://www.ghrtmb.my.id/quiz/public/soal")
      .then((response) => {
        const data = response.data;
        data.forEach((item) => {
          const pilihanKeys = Object.keys(item).filter((key) =>
            key.startsWith("answer_")
          );
          const pilihan = [];
          for (let i = 1; i <= pilihanKeys.length; i++) {
            if (item.type === "Tf" && i >= 3) {
              break;
            }
            pilihan.push(item["answer_" + i]);
          }

          console.log(pilihan);
          // Menghapus atribut "pilihan_1" hingga "pilihan_4"
          pilihanKeys.forEach((key) => delete item[key]);

          // Menambahkan array "pilihan" ke objek item
          item.answers = pilihan;
          item.correct_answer = item.correct_answer - 1;
        });

        // Output hasil
        this.questions = data;
        console.log(this.questions);
        this.count = this.questions.length;
        this.loaded = true; // Set loading to false when data is loaded
      })
      .catch((error) => {
        this.error = "Error loading data from the API: " + error;
        this.loaded = true; // Set loading to false in case of an error
      });
  },
  // data() {
  //     return {
  //         posts: [],
  //     };
  // },
  // mounted() {
  //     // Fetch data from the API
  //     this.fetchData();
  // },
  // methods: {
  //     fetchData() {
  //         const apiUrl = 'https://www.ghrtmb.my.id/quiz/public/soal';

  //         axios
  //             .get(apiUrl)
  //             .then((response) => {
  //                 this.posts = response.data;
  //             })
  //             .catch((error) => {
  //                 console.error('Error fetching data:', error);
  //             });
  //     },
  // },
};
</script>
  
<style scoped>
li {
  margin-bottom: 20px;
}
</style>
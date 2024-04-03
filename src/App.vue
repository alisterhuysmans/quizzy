<template>
  <div id="app">
    <quiz-home v-if="currentPage === 'quizHome'" @changePage="handleChangePage" />
    <quiz-question v-if="currentPage === 'quizQuestion'" :categoryId="currentCategoryId" @changePage="handleChangePage" />
    <quiz-result v-if="currentPage === 'quizResult'" :score="score" :total="totalQuestions" :totalPointsPossible="totalPointsPossible" @changePage="handleChangePage" />
  </div>
</template>

<script>
import QuizHome from './components/QuizHome.vue';
import QuizQuestion from './components/QuizQuestion.vue';
import QuizResult from './components/QuizResult.vue';

export default {
  name: 'App',
  components: {
    QuizHome,
    QuizQuestion,
    QuizResult
  },
  data() {
    return {
      currentPage: 'quizHome',
      currentCategoryId: null,
      score: 0,
      totalQuestions: 0,
      totalPointsPossible: 0
    };
  },
  methods: {
    handleChangePage(page, data = null) {
      if (page === 'quizResult' && typeof data === 'object') {
        this.score = data.score;
        this.totalQuestions = data.total;
        this.totalPointsPossible = data.totalPointsPossible;
      } else if (page === 'quizQuestion' && typeof data === 'number') {
        this.currentCategoryId = data;
        // Réinitialise le score et le total de questions à chaque nouveau quiz
        this.score = 0;
        this.totalQuestions = 0;
      }
      this.currentPage = page;
      
      // Si l'utilisateur veut retourner à l'accueil ou choisir une autre catégorie, pas besoin de passer des données supplémentaires
      if (page === 'quizHome') {
        this.currentCategoryId = null; // Réinitialiser l'ID de la catégorie si retour à l'accueil
      }
    },
    handleQuizEnd(data) {
      this.score = data.score;
      this.totalQuestions = data.total;
      this.totalPointsPossible = data.totalPointsPossible;
      this.currentPage = 'quizResult';
    }
  }
};
</script>

<style>
</style>

<template>
  <div class="quiz-home">
    <h1 v-if="!errorMessage">Choisissez une catégorie</h1>
    <div v-if="errorMessage" class="error-message">
      <p>Il y as eu une erreur.</p>
      {{ errorMessage }}</div>
    <div v-else>
      <div v-for="category in categories" :key="category.id">
        <button @click="selectCategory(category.id)">{{ category.title }}</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      categories: [],
      errorMessage: null,
    };
  },
  mounted() {
    this.fetchCategories();
  },
  methods: {
    fetchCategories() {
      fetch('https://quizz-musical-backend.airdev.be/api/categories')
        .then(response => {
          if (!response.ok) {
            throw new Error('Server Error');
          }
          return response.json();
        })
        .then(data => {
          this.categories = data;
          this.errorMessage = null;
        })
        .catch(error => {
          console.error('Error:', error);
          this.errorMessage = 'An error occurred while fetching categories';
        });
    },
    selectCategory(id) {
      this.$emit('changePage', 'quizQuestion', id);
    }
  }
};
</script>

<style>
.error-message {
  color: red;
  font-weight: bold;
}
</style>

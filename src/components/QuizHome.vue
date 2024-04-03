<template>
    <div class="quiz-home">
      <h1>Choisissez une catégorie</h1>
      <div v-for="category in categories" :key="category.id">
        <button @click="selectCategory(category.id)">{{ category.title }}</button>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        categories: [],
      };
    },
    mounted() {
      this.fetchCategories();
    },
    methods: {
      fetchCategories() {
        fetch('https://quizz-musical-backend.airdev.be/api/categories')
          .then(response => response.json())
          .then(data => this.categories = data)
          .catch(error => console.error('Error:', error));
      },
      selectCategory(id) {
        this.$emit('changePage', 'quizQuestion', id);
      }
    }
  };
  </script>
  
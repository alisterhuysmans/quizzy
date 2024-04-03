<template>
    <div class="quiz-result">
      <h1>Résultats du Quiz</h1>
      <p>Votre score est : {{ score }} sur {{ totalPointsPossible }}</p>
      <button @click="$emit('changePage', 'quizQuestion', currentCategoryId)">Rejouer</button>
      <button @click="$emit('changePage', 'quizHome')">Choisir une autre catégorie</button>
    </div>
    <!-- gea -->
  </template>
  
  <script>
  export default {
    props: ['score', 'total', 'totalPointsPossible', 'currentCategoryId'],
  };
  </script>

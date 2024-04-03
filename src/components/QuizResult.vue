<template>
    <div class="quiz-result">
      <h1>Résultats du Quiz</h1>
      <p>Votre score est : {{ score }} sur {{ totalPointsPossible }}</p>
      <button @click="$emit('changePage', 'quizHome')">Rejouer</button>
      <button @click="$emit('changePage', 'quizHome')">Choisir une autre catégorie</button>
    </div>
  </template>
  
  <script>
  export default {
    props: ['score', 'total', 'totalPointsPossible'],
  };
  </script>
  
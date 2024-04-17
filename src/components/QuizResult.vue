<template>
    <div class="quiz-result">
        <h1 class="page-title">Résultats du Quiz</h1>
        <p>Votre score est : {{ score }} sur {{ totalPointsPossible }}</p>
        <div class="result-actions">
            <button
                class="btn btn-primary"
                @click="$emit('changePage', 'quizQuestion', currentCategoryId)"
            >
                Rejouer
            </button>
            <button
                class="btn btn-secondary"
                @click="$emit('changePage', 'quizHome')"
            >
                Choisir une autre catégorie
            </button>
        </div>
    </div>
    <!-- gea -->
</template>

<script>
export default {
    props: ["score", "total", "totalPointsPossible", "currentCategoryId"],
};
</script>

<style>
.result-actions {
    display: flex;
    gap: 1rem;
    padding: 2rem 0;
}
</style>

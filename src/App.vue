<template>
    <AppHeader title="Quizzy" />
    <main class="main-content">
        <quiz-home
            v-if="currentPage === 'quizHome'"
            @changePage="handleChangePage"
        />
        <quiz-question
            v-if="currentPage === 'quizQuestion'"
            :categoryId="currentCategoryId"
            @changePage="handleChangePage"
        />
        <quiz-result
            v-if="currentPage === 'quizResult'"
            :score="score"
            :total="totalQuestions"
            :totalPointsPossible="totalPointsPossible"
            @changePage="handleChangePage"
        />
    </main>
    <AppFooter />
</template>

<script>
import AppHeader from "./components/AppHeader.vue";
import AppFooter from "./components/AppFooter.vue";
import QuizHome from "./components/QuizHome.vue";
import QuizQuestion from "./components/QuizQuestion.vue";
import QuizResult from "./components/QuizResult.vue";

export default {
    name: "App",
    components: {
        AppHeader,
        AppFooter,
        QuizHome,
        QuizQuestion,
        QuizResult,
    },
    data() {
        return {
            currentPage: "quizHome",
            currentCategoryId: null,
            score: 0,
            totalQuestions: 0,
            totalPointsPossible: 0,
        };
    },
    methods: {
        handleChangePage(page, data = null) {
            if (page === "quizResult" && typeof data === "object") {
                this.score = data.score;
                this.totalQuestions = data.total;
                this.totalPointsPossible = data.totalPointsPossible;
            } else if (page === "quizQuestion" && typeof data === "number") {
                this.currentCategoryId = data;
                // Réinitialise le score et le total de questions à chaque nouveau quiz
                this.score = 0;
                this.totalQuestions = 0;
            }
            this.currentPage = page;

            // Si l'utilisateur veut retourner à l'accueil ou choisir une autre catégorie, pas besoin de passer des données supplémentaires
            if (page === "quizHome") {
                this.currentCategoryId = null; // Réinitialiser l'ID de la catégorie si retour à l'accueil
            }
        },
        handleQuizEnd(data) {
            this.score = data.score;
            this.totalQuestions = data.total;
            this.totalPointsPossible = data.totalPointsPossible;
            this.currentPage = "quizResult";
        },
    },
};
</script>

<style>
:root {
    --background-color: #0c1518;
    --background-color-2: rgb(127 152 155 / 46%);
    --primary-color: rgba(76, 194, 76, 0.801);
    --text-color: rgb(225, 225, 225);
    --text-color-2: #89dc89;
}

@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap");

@keyframes pulsate {
    0% {
        filter: blur(0) brightness(2);
    }
    50% {
        filter: blur(0.06rem) brightness(1);
    }
    100% {
        filter: blur(0) brightness(2);
    }
}

@keyframes blurGrain {
    0% {
        filter: blur(0); /* No blur */
    }
    100% {
        filter: blur(10px); /* Maximum blur */
    }
}

*,
html,
body {
    margin: 0;
    padding: 0;
}

html {
    font-size: 67.5%;
}

body {
    font-family: "Roboto", sans-serif;
    font-size: 1.6rem;
    color: var(--text-color);
}

#app {
    height: 100vh;
    width: 100%;
    background-color: var(--background-color);
    background-image: url(./assets/background.png);
    background-repeat: no-repeat;
    background-position: top;
    background-size: cover;
    background-blend-mode: multiply;
}

main {
    max-width: 1300px;
    margin: auto;
    padding: 25vh 10% 0;
}

.btn {
    font-family: inherit;
    font-size: 1.4rem;
    font-weight: 600;
    padding: 0.8rem 1.2rem;
    border: none;
    border-radius: 5px;
    text-transform: uppercase;
    transition: all 0.2s;
}

.btn-primary {
    background-color: var(--primary-color);
}

.btn-secondary {
    background-color: var(--text-color);
}

.btn:hover {
    cursor: pointer;
    transform: translateY(-0.2rem);
    filter: brightness(1.3);
}

.input {
    padding: 0.8rem 1.2rem;
    background-color: var(--text-color);
    border: none;
    font-size: 1.4rem;
}
</style>

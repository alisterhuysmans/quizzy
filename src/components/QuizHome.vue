<template>
    <div class="quiz-home">
        <h2 class="page-title" v-if="!errorMessage">
            Choisissez une catégorie
        </h2>
        <div v-if="errorMessage" class="error-message">
            <p>Connection perdue avec l'API.</p>
            {{ errorMessage }}
        </div>
        <div v-else>
            <div class="cat-container">
                <div v-for="category in categories" :key="category.id">
                    <button
                        class="btn btn-primary"
                        @click="selectCategory(category.id)"
                    >
                        {{ category.title }}
                    </button>
                </div>
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
            fetch("https://quizz-musical-backend.airdev.be/api/categories")
                .then((response) => {
                    if (!response.ok) {
                        throw new Error("Server Error");
                    }
                    return response.json();
                })
                .then((data) => {
                    this.categories = data;
                    this.errorMessage = null;
                })
                .catch((error) => {
                    console.error("Error:", error);
                    this.errorMessage =
                        "An error occurred while fetching categories";
                });
        },
        selectCategory(id) {
            this.$emit("changePage", "quizQuestion", id);
        },
    },
};
</script>

<style>
.page-title {
    padding-bottom: 2rem;
}

.cat-container {
    display: flex;
    gap: 1rem;
}
.error-message {
    color: var(--text-color);
}
</style>

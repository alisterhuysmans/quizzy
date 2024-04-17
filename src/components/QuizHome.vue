<template>
    <div class="quiz-home">
        <h2 class="page-title">Choisissez une catégorie</h2>
        <div class="cat-container">
            <div v-for="category in categories" :key="category.id">
                <button
                    class="btn btn-primary cat-choice"
                    @click="selectCategory(category.id)"
                >
                    {{ category.title }}
                </button>
            </div>
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
            fetch("https://quizz-musical-backend.airdev.be/api/categories")
                .then((response) => response.json())
                .then((data) => (this.categories = data))
                .catch((error) => console.error("Error:", error));
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
</style>

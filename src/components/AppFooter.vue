<template>
    <footer>
        <p>&copy; {{ currentYear }} Quizzy</p>
    </footer>
</template>

<script>
export default {
    name: "AppFooter",
    data() {
        return {
            currentYear: new Date().getFullYear(),
        };
    },
};
</script>

<style>
/* Add your footer styles here */
footer {
    font-size: 1.4rem;
    background-color: var(--background-color);
    color: var(--background-color-2);
    padding: 10px;
    text-align: center;
    position: fixed;
    left: 0;
    bottom: 0;
    width: 100%;
}
</style>

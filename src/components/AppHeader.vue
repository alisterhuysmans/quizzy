<template>
    <header>
        <a class="logo-quizzy" href="#">
            <h1>{{ title }}</h1>
        </a>
    </header>
</template>

<script>
export default {
    name: "AppHeader",
    props: {
        title: {
            type: String,
            required: true,
        },
    },
};
</script>

<style>
/* Add your header styles here */
header {
    background-color: var(--background-color);
    box-shadow: 0px 35px 141px var(--background-color-2);
    padding: 1rem 2rem;
    display: flex;
    align-items: center;
    gap: 1rem;
}

.logo-quizzy {
    text-decoration: none;
    color: var(--primary-color);
}

.logo-quizzy:visited,
.logo-quizzy:focus {
    color: var(--primary-color);
}

nav ul {
    list-style: none;
    padding: 0;
}

nav ul li {
    display: inline;
    margin-right: 10px;
}

nav ul li a {
    color: white;
    text-decoration: none;
}
</style>

<template>
    <div class="quiz-question-container">
        <!-- Affiche un message de chargement ou rien si aucune question n'est chargée -->
        <div v-if="!currentQuestion">
            <p>Chargement des questions...</p>
        </div>
        <!-- Affiche la question actuelle s'il y en a une avec le temps restant -->
        <div v-else>
            <p class="timer">Temps restant : {{ timeLeft }} sec</p>
            <h2 class="quiz-question">
                {{ currentQuestionNumber }} - {{ currentQuestion.title }}
            </h2>

            <!-- Gère l'affichage pour les questions 'Blind test' avec un extrait audio -->
            <div v-if="currentQuestion.category_id === 2">
                <audio
                    controls
                    :src="currentQuestion.content.sound_url"
                ></audio>
                <!-- Affiche les options de réponse si présentes -->
                <div
                    class="audio-choices-container"
                    v-if="currentQuestion.content.answers"
                >
                    <button
                        class="btn btn-primary"
                        v-for="answer in currentQuestion.content.answers"
                        :key="answer"
                        @click="submitAnswer(answer)"
                        :disabled="submitted"
                    >
                        {{ answer }}
                    </button>
                </div>
            </div>

            <!-- Gère l'affichage pour les questions 'Paroles de chansons', avec saisie de texte -->
            <div
                v-if="
                    currentQuestion.category_id === 1 &&
                    currentQuestion.content.lyrics
                "
            >
                <p
                    class="lyrics"
                    v-html="formatLyrics(currentQuestion.content.lyrics)"
                ></p>
                <div class="input-container">
                    <input
                        class="input"
                        type="text"
                        v-model="userResponse"
                        placeholder="Votre réponse..."
                    />
                    <div class="actions">
                        <button
                            class="btn btn-primary"
                            @click="submitLyricsAnswer"
                            :disabled="submitted"
                        >
                            Valider
                        </button>
                        <button
                            class="btn btn-secondary"
                            @click="skipQuestion"
                            :disabled="submitted"
                        >
                            Passer
                        </button>
                    </div>
                </div>
            </div>

            <!-- Zone de feedback pour l'utilisateur après chaque réponse -->
            <div v-if="feedbackMessage" class="feedback">
                {{ feedbackMessage }}
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: ["categoryId"],
    data() {
        return {
            questions: [],
            currentQuestionIndex: 0,
            score: 0,
            totalPointsPossible: 0,
            userResponse: "",
            feedbackMessage: "",
            timeLeft: 30,
            timer: null,
            submitted: false, // Flag to track whether an answer has been submitted
        };
    },
    computed: {
        currentQuestion() {
            return this.questions.length > 0
                ? this.questions[this.currentQuestionIndex]
                : null;
        },
        currentQuestionNumber() {
            return this.currentQuestionIndex + 1; // Calcule nombre réponse
        },
    },
    mounted() {
        this.fetchQuestions();
    },
    watch: {
        currentQuestionIndex(newValue, oldValue) {
            if (newValue !== oldValue) {
                this.startCountdown();
            }
        },
    },
    beforeUnmount() {
        if (this.timer) {
            clearInterval(this.timer); // Nettoie le timer lors de la destruction du composant
        }
    },
    methods: {
        startCountdown() {
            if (this.timer) {
                clearInterval(this.timer); // Nettoie le timer précédent si existant
            }
            this.timeLeft = 30; // Réinitialise le compte à rebours à 30 secondes
            this.timer = setInterval(() => {
                if (this.timeLeft > 0) {
                    this.timeLeft--;
                } else {
                    this.handleTimeOut(); // Gère le cas où le temps est écoulé
                }
            }, 1000);
        },

        handleTimeOut() {
            clearInterval(this.timer);
            this.feedbackMessage = "Temps écoulé !";
            setTimeout(() => {
                this.moveToNextQuestion();
            }, 2000); // Délai pour permettre à l'utilisateur de lire le feedback avant de passer à la prochaine question
        },

        fetchQuestions() {
            fetch(
                `https://quizz-musical-backend.airdev.be/api/questions?category_id=${this.categoryId}`
            )
                .then((response) => response.json())
                .then((data) => {
                    this.questions = this.shuffleArray(data); // Shuffle the fetched questions
                    this.calculateTotalPoints();
                    this.startCountdown();
                })
                .catch((error) =>
                    console.error(
                        "Erreur lors de la récupération des questions:",
                        error
                    )
                );
        },

        shuffleArray(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
            return array;
        },

        calculateTotalPoints() {
            this.totalPointsPossible = this.questions.reduce(
                (total, question) => total + question.points,
                0
            );
        },

        formatLyrics(lyrics) {
            return lyrics.replace(/<br\s*\/?>/gi, "\n");
        },

        submitLyricsAnswer() {
            if (
                !this.userResponse ||
                !this.currentQuestion.answer ||
                this.submitted
            ) {
                this.feedbackMessage =
                    "Une erreur est survenue. Veuillez réessayer.";
                clearInterval(this.timer);
                return;
            }

            const isCorrect = this.compareLyricsAnswer(
                this.userResponse,
                this.currentQuestion.answer
            );

            this.feedbackMessage = isCorrect
                ? "Bonne réponse !"
                : "Mauvaise réponse !";
            if (isCorrect) {
                this.score += this.currentQuestion.points; // Incrémente le score basé sur les points de la question
            }

            // Attendre un peu avant de passer à la question suivante pour voire le feedback
            setTimeout(() => {
                this.moveToNextQuestion();
            }, 2000); // 2 secondes pour lire le feedback

            this.submitted = true; // Dit que c'est déjà répondu une fois fais
        },

        submitAnswer(selectedAnswer) {
            // Vérifie d'abord si on est dans la bonne catégorie (dans cet exemple, "Blind test" a l'id 2)
            if (this.currentQuestion.category_id === 2) {
                const isCorrect = this.compareResponses(
                    selectedAnswer,
                    this.currentQuestion.answer
                );

                this.feedbackMessage = isCorrect
                    ? "Bonne réponse !"
                    : "Mauvaise réponse !";
                clearInterval(this.timer);
                if (isCorrect) {
                    this.score += this.currentQuestion.points; // Incrémente le score basé sur les points de la question
                }
            } else {
                // Si ce n'est pas une question de type "Blind test", gérer d'autres types (à supprimer car pas utiliser!!)
            }

            // Attendre un peu avant de passer à la question suivante pour que le feedback soit visible
            setTimeout(() => {
                this.moveToNextQuestion();
            }, 2000); // 2 secondes pour lire le feedback

            this.submitted = true; // Dit que c'esrt déjà répondu
        },

        compareLyricsAnswer(userResponse, correctAnswer) {
            // Convertir les réponses en minuscules pour ignorer la casse
            const normalizedUserResponse = userResponse.toLowerCase().trim();
            const normalizedCorrectAnswer = correctAnswer.toLowerCase().trim();

            // Diviser les réponses en mots
            const userWords = normalizedUserResponse.split(/\s+/);
            const correctWords = normalizedCorrectAnswer.split(/\s+/);

            // Vérifier si les réponses ont le même nombre de mots
            if (userWords.length !== correctWords.length) {
                return false;
            }

            // Comparer chaque mot
            for (let i = 0; i < userWords.length; i++) {
                const userWord = userWords[i];
                const correctWord = correctWords[i];

                // Vérifier la correspondance exacte pour les mots de moins de 4 caractères
                if (correctWord.length < 4 && userWord !== correctWord) {
                    return false;
                }

                // Pour les mots de 4 caractères ou plus, tolérer une différence de longueur de 1 caractère
                if (correctWord.length >= 4) {
                    const lengthDifference = Math.abs(
                        userWord.length - correctWord.length
                    );
                    if (lengthDifference > 1) {
                        return false;
                    }

                    // Vérifier que les autres caractères correspondent
                    let commonLength = Math.min(
                        userWord.length,
                        correctWord.length
                    );
                    for (let j = 0; j < commonLength; j++) {
                        if (userWord[j] !== correctWord[j]) {
                            return false;
                        }
                    }
                }
            }

            // Si toutes les vérifications passent, la réponse est considérée comme correcte
            return true;
        },

        compareResponses(userResponse, correctAnswer) {
            // Normalise les réponses pour la comparaison
            const normalizedUserResponse = userResponse.trim().toLowerCase();
            const normalizedCorrectAnswer = correctAnswer.trim().toLowerCase();

            // Vérifie si la réponse correcte est contenue dans la réponse de l'utilisateur ou l'inverse
            return (
                normalizedUserResponse.includes(normalizedCorrectAnswer) ||
                normalizedCorrectAnswer.includes(normalizedUserResponse)
            );
        },

        moveToNextQuestion() {
            this.userResponse = "";
            this.feedbackMessage = "";
            this.submitted = false; // Reset el bouton disable
            if (this.currentQuestionIndex < this.questions.length - 1) {
                this.currentQuestionIndex++;
            } else {
                this.endQuiz();
            }
        },
        endQuiz() {
            // Envoie le score final et le nombre total de questions au composant parent
            this.$emit("changePage", "quizResult", {
                score: this.score,
                total: this.questions.length,
                totalPointsPossible: this.totalPointsPossible,
            });
        },

        skipQuestion() {
            this.moveToNextQuestion();
        },
    },
};
</script>

<style>
.quiz-question-container {
    max-width: 51rem;
}

.quiz-question {
    padding-bottom: 2rem;
}

.timer {
    color: var(--background-color-2);
    animation: pulsate 1s infinite;
    padding-bottom: 1rem;
}

.input-container {
    display: flex;
    justify-content: end;
    align-items: center;
    gap: 1rem;
    padding: 1rem 0;
}

.audio-choices-container {
    display: flex;
    gap: 1rem;
    padding: 2rem 0;
}

audio {
    width: 100%;
}

.input {
    width: 100%;
    height: fit-content;
}

.lyrics {
    color: var(--text-color-2);
}

.actions {
    display: flex;
    justify-content: flex-end;
    gap: 1rem;
    padding: 1rem 0;
}
</style>

<script setup lang="ts">
import QuizComp from './components/QuizComp.vue';
import { ref } from 'vue';

const showQuiz = ref(false);
</script>

<template>
    <div class="fullscreen bg">
        <Transition name="fade">
            <div v-if="!showQuiz" class="title-section">
                <div class="title-container">
                    <h1>FGF Youth Jersey Sunday</h1>
                </div>

                <button class="enter-quiz-btn" @click="showQuiz = true">Start Quiz</button>

                <div class="footer">
                    Created by Chiebidolu Chinaemerem
                </div>
            </div>
            <QuizComp v-else />
        </Transition>
    </div>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}

.title-container {
    padding-top: 150px;
    display: flex;
    justify-content: center;
    align-items: center;
}

.title-container h1 {
    font-size: 5rem;
    font-family: 'Inter';
}

.title-section {
    position: absolute;
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
}

.enter-quiz-btn {
    margin-top: 150px;
    background-color: transparent;
    border: 2px solid var(--primary-color);
    color: var(--primary-color);
    /* padding: 6rem 6rem; */
    text-align: center;
    width: 250px;
    height: 250px;
    border-radius: 50%;
    font-size: 2rem;
    font-weight: bold;
    cursor: pointer;
    transition: all 0.2s ease-in-out;
}

.enter-quiz-btn:hover {
    background-color: var(--primary-color);
    color: white;
}

.footer {
    position: absolute;
    bottom: 75px;
    text-align: center;
    color: var(--primary-color);
    font-size: 4rem;
}
</style>

<style>
:root {
    --primary-color: #2f4858;
    --secondary-color: #e8d0ad;
}

* {
    margin: 0;
    padding: 0;
    font-family: 'Inter';
    box-sizing: border-box;
}

.fullscreen,
#app,
html,
body {
    width: 100%;
    height: 100%;
}

.text {
    color: var(--primary-color);
}

.bg {
    /* background-color: #f4c28e; */
    background-color: var(--secondary-color);
}
</style>

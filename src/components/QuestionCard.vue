<script setup lang="ts">
import type { Coord, Question } from '@/types';
import { animate } from 'motion-v';
import { onMounted, ref, useTemplateRef } from 'vue';

const props = defineProps<{
    number: number;
    question: Question,
    coord: Coord
}>();

const emit = defineEmits<{
    answer: [number: number]
    close: []
}>()

const questionCard = useTemplateRef("questionCard")
const isAnswerActive = ref(false)
function handleAnswer() {
    emit('answer', props.number)
    isAnswerActive.value = true
}
function handleGoBack() {
    const firstChild = questionCard.value?.firstChild
    if (!firstChild) return

    Promise.all([animate(firstChild as Element, {
        opacity: [1, 0],
        scale: [1, 0]
    }, {
        duration: 0.25
    }),
    animate(questionCard.value, {
        opacity: [1, 0],
        x: [0, props.coord.x],
        y: [0, props.coord.y],
        width: ['100%', props.coord.width + 'px'],
        height: ['100%', props.coord.height + 'px'],
    }, {
        duration: 0.3
    })]).then(() => {
        emit('close')
        isAnswerActive.value = false
    })
}

function onEnter(el: Element, done: () => void) {
    const firstChild = el.firstChild

    Promise.all([animate(firstChild as Element, {
        opacity: [0, 1],
        scale: [0.2, 1]
    }, {
        duration: 0.25
    }),

    animate(el, {
        opacity: [0, 1],
        scale: [0.4, 1]
    }, {
        duration: 0.3
    })]).then(done)
}

function onLeave(el: Element, done: () => void) {
    const firstChild = el.firstChild

    Promise.all([animate(firstChild as Element, {
        opacity: [1, 0],
        scale: [1, 0]
    }, {
        duration: 0.25
    }),
    animate(el, {
        opacity: [1, 0],
        scale: [1, 0.2]
    }, {
        duration: 0.3
    })]).then(done)
}

onMounted(() => {
    animate(questionCard.value, {
        opacity: [0.6, 1],
        x: [props.coord.x, 0],
        y: [props.coord.y, 0],
        width: [props.coord.width, '100%'],
        height: [props.coord.height, '100%'],
    }, {
        duration: 0.3
    })
})
</script>

<template>
    <div class="question-card" ref="questionCard"
        :style="{ transform: `translate(${coord.width}px, ${coord.height}px)`, width: coord.width + 'px', height: coord.height + 'px' }">
        <Transition :css="false" @enter="onEnter" @leave="onLeave">
            <div v-if="!isAnswerActive" class="question-container">
                <div>
                    <h2 class="question-header">{{ `Question ${number}` }}</h2>
                    <div class="question-content">
                        <p>{{ question.question }}</p>
                        <button @click="handleAnswer" class="answer-button">Answer</button>
                    </div>
                </div>
            </div>
            <div v-else class="answer-container">
                <div>
                    <span class="go-back" @click="handleGoBack">Go Back</span>
                    <h2 class="answer-header">{{ `Answer for Question ${number}` }}</h2>
                    <div class="answer-content">
                        <p>{{ question.answer }}</p>
                    </div>
                </div>
            </div>
        </Transition>
    </div>
</template>

<style scoped>
.question-card {
    position: fixed;
    top: 0;
    left: 0;
    z-index: 99;
    background-color: var(--secondary-color);
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 30px;
}

.answer-container {
    position: absolute;
    z-index: 10;
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 30px;
}

.go-back {
    position: absolute;
    display: inline-block;
    padding: 10px 20px;
    border: 2px solid var(--primary-color);
    top: 20px;
    right: 20px;
    cursor: pointer;
    font-size: 1.2rem;
    font-weight: bold;
    color: var(--primary-color);
}

.question-header,
.answer-header {
    text-align: center;
    font-size: 3em;
    font-weight: bold;
    margin-bottom: 20px;
}

.question-header {
    margin-bottom: 40px;
}

.question-content,
.answer-content {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    gap: 100px;
}

.question-content p,
.answer-content p {
    text-align: center;
    min-width: 300px;
    max-width: 75%;
    font-size: 4rem;
}

.answer-button {
    position: relative;
    overflow: hidden;
    z-index: 1;
    background-color: transparent;
    border: 2px solid var(--primary-color);
    color: var(--primary-color);
    padding: 1rem 6rem;
    font-size: 1.2rem;
    font-weight: bold;
    cursor: pointer;
    transition: all 0.2s ease-in-out;
}

.answer-button::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    z-index: -1;
    width: 250%;
    height: 700%;
    background-color: var(--primary-color);
    transform: rotate(45deg) translate(0%, 100%);
    transition: transform 0.5s ease;
}

.answer-button:active {
    transform: scale(0.90);
}

.answer-button:hover::before {
    transform: rotate(45deg) translate(-30%, 0%);
}

.answer-button:hover {
    color: white;
}
</style>
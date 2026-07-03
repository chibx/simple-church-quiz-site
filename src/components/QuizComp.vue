<script setup lang="ts">
import { reactive, ref, useTemplateRef } from 'vue';
import QuestionCard from './QuestionCard.vue';
import { questions } from '../questions.ts';
import type { Coord } from '../types.ts';
import { motion } from "motion-v"
import SvgClose from './SvgClose.vue';
const answered = reactive(new Set<number>())
const activeQuestion = ref<number | null>(null)
const questionCardRef = useTemplateRef("questionCardRef")

const currentQuestionBoxCoord = ref<Coord>({
    x: 0,
    y: 0,
    width: 0,
    height: 0
})

const openQuestion = (evt: PointerEvent, num: number) => {
    if (answered.has(num)) return;
    const target = evt.target as HTMLElement;
    const rect = target.getBoundingClientRect();
    currentQuestionBoxCoord.value = {
        x: rect.x,
        y: rect.y,
        width: rect.width,
        height: rect.height
    }
    activeQuestion.value = num
}
</script>

<template>
    <div class="fullscreen quiz-container">
        <div class="fullscreen my-grid">
            <motion.div v-for="i in 30" :key="i" class="question-box" :class="{ 'slide-in': !answered.has(i) }"
                :initial="{ opacity: 0, scale: 0.2 }"
                :animate="{ opacity: 1, scale: 1, transition: { delay: i * 0.08, duration: 0.2 } }" :style="{
                    borderRight: i % 6 === 0 ? 'none' : '2px solid var(--primary-color)',
                    borderBottom: i > 24 ? 'none' : '2px solid var(--primary-color)'
                }" :number="i" @click="(evt) => openQuestion(evt, i)">
                <span>{{ i }}</span>
                <div v-if="answered.has(i)" class="overlay">
                    <SvgClose class="close-svg" />
                </div>
            </motion.div>

            <QuestionCard ref="questionCardRef" v-if="activeQuestion !== null && questions[activeQuestion - 1]"
                @answer="(num) => answered.add(num)" @close="activeQuestion = null" :number="activeQuestion"
                :question="questions[activeQuestion - 1]!" :coord="currentQuestionBoxCoord" />
        </div>
    </div>
</template>

<style scoped>
.quiz-container {
    position: absolute;
}

.my-grid {
    /** 1 - 30 */
    display: grid;
    grid-template-columns: repeat(6, 1fr);
    grid-template-rows: repeat(5, 1fr);
}

.overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: rgba(255, 255, 255, 0.3);
    z-index: 3;
}

.close-svg {
    width: 10rem;
    height: 10rem;
    color: rgb(255, 0, 0);
}

.question-box {
    font-family: "Times New Roman";
    position: relative;
    overflow: hidden;
    background-color: transparent;
    color: var(--primary-color);
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 5rem;
    font-weight: bold;
    cursor: pointer;
    z-index: 1;
    transition: all 0.2s ease;
}

.question-box span {
    font-family: 'Alfa Slab One';
}

.slide-in::before {
    content: '';
    position: absolute;
    overflow: hidden;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
    transform: translateY(-100%);
    background-color: var(--primary-color);
    transition: all 0.2s ease;
}

.slide-in:hover::before {
    transform: translateY(0);
}

.slide-in:hover {
    color: var(--secondary-color);
}
</style>
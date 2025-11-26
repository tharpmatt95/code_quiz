<script setup>
import { ref, computed } from 'vue'

defineProps({
  msg: String,
})

const questions = ref([
  {
    id: 1,
    title: 'What does this code log?',
    code: `const arr = [1, 2, 3]
console.log(arr.map(x => x * 2))`,
    options: ['[1, 2, 3]', '[2, 4, 6]', 'Error', 'undefined'],
    correctIndex: 1,
    explanation: 'Array.map returns a new array with each value transformed.',
  },
])

const currentIndex = ref(0)
const selectedIndex = ref(null)
const showAnswer = ref(false)
const score = ref(0)

const currentQuestion = computed(() => questions.value[currentIndex.value])

function selectOption(index) {
  if (showAnswer.value) return
  selectedIndex.value = index
}

function submitAnswer() {
  if (selectedIndex.value === null || showAnswer.value) return
  showAnswer.value = true
  if (selectedIndex.value === currentQuestion.value.correctIndex) {
    score.value++
  }
}

function nextQuestion() {
  currentIndex.value = 0
  selectedIndex.value = null
  showAnswer.value = false
  score.value = 0
}
</script>

<template>
  <div class="quiz-card">
    <header class="quiz-header">
      <h2>Code Quiz</h2>
      <p class="score">Score: {{ score }}</p>
    </header>

    <section class="question">
      <p class="question-title">
        Q{{ currentIndex + 1 }}. {{ currentQuestion.title }}
      </p>

      <pre class="code"><code>{{ currentQuestion.code }}</code></pre>

      <ul class="options">
        <li
          v-for="(option, index) in currentQuestion.options"
          :key="index"
        >
          <button
            type="button"
            class="option-btn"
            :class="{
              selected: selectedIndex === index,
              correct:
                showAnswer && index === currentQuestion.correctIndex,
              incorrect:
                showAnswer &&
                selectedIndex === index &&
                index !== currentQuestion.correctIndex,
            }"
            @click="selectOption(index)"
          >
            {{ option }}
          </button>
        </li>
      </ul>

      <div class="actions">
        <button
          type="button"
          class="primary"
          @click="submitAnswer"
          :disabled="selectedIndex === null || showAnswer"
        >
          Check answer
        </button>

        <button
          type="button"
          class="secondary"
          @click="nextQuestion"
          :disabled="!showAnswer"
        >
          Next
        </button>
      </div>

      <p v-if="showAnswer" class="feedback">
        <span v-if="selectedIndex === currentQuestion.correctIndex">
          Correct!
        </span>
        <span v-else>
          Not quite. The correct answer is
          "{{ currentQuestion.options[currentQuestion.correctIndex] }}".
        </span>
      </p>

      <p v-if="showAnswer" class="explanation">
        {{ currentQuestion.explanation }}
      </p>
    </section>
  </div>
</template>

<style scoped>
/* FULL DARK MODE (no light theme) */

.quiz-card {
  max-width: 640px;
  margin: 2rem auto;
  padding: 1.5rem 2rem;
  border-radius: 12px;
  background: #0d0d0d;
  border: 1px solid #252525;
  color: #e5e5e5;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.7);
}

.quiz-header {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  margin-bottom: 1rem;
}

.quiz-header h2 {
  margin: 0;
  color: #fafafa;
}

.score {
  font-size: 0.9rem;
  color: #bbbbbb;
}

.question-title {
  font-weight: 600;
  margin-bottom: 0.75rem;
  color: #dddddd;
}

.code {
  background: #111111;
  color: #e0e0e0;
  padding: 0.75rem 1rem;
  border-radius: 8px;
  overflow-x: auto;
  font-size: 0.9rem;
  margin-bottom: 1rem;
  border: 1px solid #333;
}

.options {
  list-style: none;
  padding: 0;
  margin: 0 0 1rem;
}

.option-btn {
  width: 100%;
  text-align: left;
  padding: 0.6rem 0.75rem;
  margin-bottom: 0.5rem;
  border-radius: 8px;
  border: 1px solid #3a3a3a;
  background: #1b1b1b;
  color: #e5e5e5;
  cursor: pointer;
  transition: background 0.15s ease, border-color 0.15s ease;
}

.option-btn:hover {
  border-color: #6666ff;
  background: #232323;
}

.option-btn.selected {
  border-color: #818cf8;
  background: #252559;
}

.option-btn.correct {
  background: #0f3d24;
  border-color: #16a34a;
}

.option-btn.incorrect {
  background: #4b0f16;
  border-color: #dc2626;
}

.actions {
  display: flex;
  gap: 0.75rem;
  margin-bottom: 0.5rem;
}

.actions button {
  flex: 1;
  padding: 0.6rem 0.75rem;
  border-radius: 8px;
  border: none;
  cursor: pointer;
  font-weight: 500;
}

.actions .primary {
  background: #4f46e5;
  color: white;
}

.actions .primary:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.actions .secondary {
  background: #333;
  color: #ddd;
}

.feedback {
  font-weight: 600;
  margin-top: 0.25rem;
  color: #e5e5e5;
}

.explanation {
  font-size: 0.9rem;
  margin-top: 0.25rem;
  color: #bbbbbb;
}
</style>

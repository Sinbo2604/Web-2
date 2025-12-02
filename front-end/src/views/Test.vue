<template>
  <div>
    <h1 class="ui header center aligned">Vocabulary Quiz</h1>

    <!-- Loading State -->
    <div v-if="loading" class="ui active centered inline loader"></div>

    <!-- No Words State -->
    <div v-else-if="words.length < 1" class="ui message warning">
      <div class="header">No Words Found</div>
      <p>Please add some words to the vocabulary list to start the quiz.</p>
    </div>

    <!-- Setup Screen -->
    <div v-else-if="quizStatus === 'setup'" class="ui centered card" style="width: 500px;">
      <div class="content">
        <div class="header">Quiz Setup</div>
      </div>
      <div class="content">
        <form class="ui form" @submit.prevent="startQuiz">
          
          <div class="two fields">
            <div class="field">
              <label>Show me (Question)</label>
              <select v-model="questionLanguage" class="ui dropdown">
                <option v-for="lang in availableLanguages" :key="lang.value" :value="lang.value">
                  {{ lang.text }}
                </option>
              </select>
            </div>
            <div class="field">
              <label>Answer in (Target)</label>
              <select v-model="answerLanguage" class="ui dropdown">
                <option v-for="lang in availableLanguages" :key="lang.value" :value="lang.value">
                  {{ lang.text }}
                </option>
              </select>
            </div>
          </div>

          <div class="field">
            <label>Number of Questions</label>
            <input 
              type="number" 
              v-model.number="totalQuestions" 
              min="1" 
              :max="words.length"
            >
            <div class="ui pointing label">
              Max available: {{ words.length }}
            </div>
          </div>

          <div v-if="questionLanguage === answerLanguage" class="ui error message visible">
            <p>Question and Answer languages must be different.</p>
          </div>

          <button class="ui button primary fluid" :disabled="!isValidSetup">
            Start Quiz
          </button>
        </form>
      </div>
    </div>

    <!-- Quiz Active Screen -->
    <div v-else-if="quizStatus === 'active'" class="ui centered card" style="width: 500px;">
      <div class="content">
        <div class="right floated meta">
          Question {{ currentQuestionIndex + 1 }} of {{ totalQuestions }}
        </div>
        <div class="header">Translate to {{ getLanguageName(answerLanguage) }}</div>
      </div>
      
      <div class="content" style="text-align: center; padding: 2em;">
        <h2 class="ui header blue">{{ currentWord[questionLanguage] }}</h2>
      </div>

      <div class="content">
        <form class="ui form" @submit.prevent="onSubmit">
          <div class="field" :class="{ error: isError }">
            <label>Your Answer ({{ getLanguageName(answerLanguage) }})</label>
            <div class="ui action input">
              <input 
                type="text" 
                v-model="userInput" 
                placeholder="Type the meaning..." 
                :disabled="showResult"
                ref="answerInput"
              >
              <button 
                class="ui button primary" 
                :class="{ loading: processing }"
                :disabled="showResult || !userInput"
              >
                Submit
              </button>
            </div>
          </div>
          
          <div v-if="showResult" class="ui message" :class="resultClass">
            <div class="header">{{ resultHeader }}</div>
            <p>{{ resultMessage }}</p>
          </div>
        </form>
      </div>

      <div class="extra content" v-if="showResult">
        <button class="ui button fluid" @click="nextQuestion">
          {{ isLastQuestion ? 'Finish Quiz' : 'Next Question' }}
        </button>
      </div>
    </div>

    <!-- Results Screen -->
    <div v-else-if="quizStatus === 'finished'" class="ui centered card" style="width: 500px;">
      <div class="content">
        <div class="header center aligned">Quiz Completed!</div>
      </div>
      <div class="content">
        <div class="ui statistics mini">
          <div class="green statistic">
            <div class="value">{{ correctCount }}</div>
            <div class="label">Correct</div>
          </div>
          <div class="red statistic">
            <div class="value">{{ incorrectCount }}</div>
            <div class="label">Incorrect</div>
          </div>
          <div class="statistic">
            <div class="value">{{ totalQuestions }}</div>
            <div class="label">Total</div>
          </div>
        </div>
        <div class="ui divider"></div>
        <h3 class="ui header center aligned">
          Your Score: {{ Math.round((correctCount / totalQuestions) * 100) }}%
        </h3>
      </div>
      <div class="extra content">
        <button class="ui button primary fluid" @click="resetQuiz">
          Play Again
        </button>
      </div>
    </div>

  </div>
</template>

<script>
import { api } from '../helpers/helpers';

export default {
  name: 'test',
  data() {
    return {
      words: [],
      loading: true,
      
      // Quiz State
      quizStatus: 'setup', // setup, active, finished
      totalQuestions: 5,
      currentQuestionIndex: 0,
      shuffledWords: [],
      
      // Language Settings
      questionLanguage: 'german',
      answerLanguage: 'vietnamese',
      availableLanguages: [
        { text: 'German', value: 'german' },
        { text: 'English', value: 'english' },
        { text: 'Vietnamese', value: 'vietnamese' }
      ],

      // Current Question State
      userInput: '',
      showResult: false,
      isCorrect: false,
      processing: false,
      
      // Stats
      correctCount: 0,
      incorrectCount: 0
    };
  },
  computed: {
    currentWord() {
      return this.shuffledWords[this.currentQuestionIndex];
    },
    isValidTotal() {
      return this.totalQuestions > 0 && this.totalQuestions <= this.words.length;
    },
    isValidSetup() {
      return this.isValidTotal && this.questionLanguage !== this.answerLanguage;
    },
    isLastQuestion() {
      return this.currentQuestionIndex >= this.totalQuestions - 1;
    },
    isError() {
      return this.showResult && !this.isCorrect;
    },
    resultClass() {
      return this.isCorrect ? 'positive' : 'negative';
    },
    resultHeader() {
      return this.isCorrect ? 'Correct!' : 'Incorrect';
    },
    resultMessage() {
      return this.isCorrect 
        ? 'Great job!' 
        : `The correct answer is: ${this.currentWord[this.answerLanguage]}`;
    }
  },
  async mounted() {
    this.loading = true;
    try {
      this.words = await api.getWords();
      // Default total questions to 5 or max available
      this.totalQuestions = Math.min(5, this.words.length);
    } catch (error) {
      console.error(error);
    } finally {
      this.loading = false;
    }
  },
  methods: {
    getLanguageName(value) {
      const lang = this.availableLanguages.find(l => l.value === value);
      return lang ? lang.text : value;
    },
    startQuiz() {
      if (!this.isValidSetup) return;
      
      // Shuffle words and pick the first N
      this.shuffledWords = [...this.words]
        .sort(() => 0.5 - Math.random())
        .slice(0, this.totalQuestions);
        
      this.currentQuestionIndex = 0;
      this.correctCount = 0;
      this.incorrectCount = 0;
      this.quizStatus = 'active';
      this.resetQuestionState();
    },
    resetQuestionState() {
      this.userInput = '';
      this.showResult = false;
      this.isCorrect = false;
      this.processing = false;
      
      this.$nextTick(() => {
        if (this.$refs.answerInput) {
          this.$refs.answerInput.focus();
        }
      });
    },
    onSubmit() {
      if (!this.userInput) return;
      
      this.processing = true;
      const correctAnswer = this.currentWord[this.answerLanguage].trim().toLowerCase();
      const userAnswer = this.userInput.trim().toLowerCase();

      this.isCorrect = correctAnswer === userAnswer;
      this.showResult = true;

      if (this.isCorrect) {
        this.correctCount++;
        // Auto advance after 1 second if correct
        setTimeout(() => {
          if (this.showResult) { // Check if user hasn't already clicked next
             this.nextQuestion();
          }
        }, 1000);
      } else {
        this.incorrectCount++;
        this.processing = false;
      }
    },
    nextQuestion() {
      if (this.isLastQuestion) {
        this.quizStatus = 'finished';
      } else {
        this.currentQuestionIndex++;
        this.resetQuestionState();
      }
    },
    resetQuiz() {
      this.quizStatus = 'setup';
      this.totalQuestions = Math.min(5, this.words.length);
    }
  }
};
</script>

<style scoped>
.ui.centered.card {
  margin-top: 2rem;
}
.ui.statistics {
  justify-content: center;
}
</style>
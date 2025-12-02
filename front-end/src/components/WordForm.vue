<template>
  <form action="#" @submit.prevent="onSubmit" class="ui form">
    <div class="ui segment raised">
      <h3 class="ui header">
        <i class="edit outline icon"></i>
        <div class="content">
          Word Details
          <div class="sub header">Enter the word in all three languages.</div>
        </div>
      </h3>
      <div class="ui divider"></div>

      <div class="ui negative message" v-if="errorsPresent">
        <i class="close icon" @click="errorsPresent = false"></i>
        <div class="header">Validation Error</div>
        <p>Please fill out all fields before submitting.</p>
      </div>

      <div class="field">
        <label>German</label>
        <div class="ui left icon input">
          <i class="germany flag"></i>
          <input type="text" placeholder="Enter German word..." v-model="word.german" />
        </div>
      </div>

      <div class="field">
        <label>English</label>
        <div class="ui left icon input">
          <i class="united kingdom flag"></i>
          <input type="text" placeholder="Enter English word..." v-model="word.english" />
        </div>
      </div>

      <div class="field">
        <label>Vietnamese</label>
        <div class="ui left icon input">
          <i class="vietnam flag"></i>
          <input type="text" placeholder="Enter Vietnamese word..." v-model="word.vietnamese" />
        </div>
      </div>

      <button class="ui positive button fluid">
        <i class="save icon"></i> Submit
      </button>
    </div>
  </form>
</template>

<script>
export default {
  name: 'word-form',
  props: {
    word: {
      type: Object,
      required: false,
      default: () => {
        return {
          english: '',
          german: '',
          vietnamese: ''
        };
      }
    }
  },
  data() {
    return {
      errorsPresent: false
    };
  },
  methods: {
    onSubmit: function() {
      if (this.word.english === '' || this.word.german === '' || this.word.vietnamese === '') {
        this.errorsPresent = true;
      } else {
        this.errorsPresent = false;
        this.$emit('createOrUpdate', this.word);
      }
    }
  }
};
</script>

<style scoped>
.ui.form .field {
  margin-bottom: 1.5em;
}
</style>
<template>
  <div>
    <div class="ui centered card">
      <div class="content">
        <div class="header">{{ word.english }}</div>
        <div class="meta">English</div>
        <div class="description">
          <div class="ui list">
            <div class="item">
              <i class="germany flag"></i>
              <div class="content">
                <div class="header">German</div>
                {{ word.german }}
              </div>
            </div>
            <div class="item">
              <i class="vietnam flag"></i>
              <div class="content">
                <div class="header">Vietnamese</div>
                {{ word.vietnamese }}
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="extra content">
        <div class="ui two buttons">
          <router-link
            :to="{ name: 'edit', params: { id: this.$route.params.id } }"
            class="ui basic yellow button"
          >
            <i class="edit icon"></i> Edit
          </router-link>
          <router-link to="/words" class="ui basic primary button">
            <i class="arrow left icon"></i> Back
          </router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { api } from '../helpers/helpers';

export default {
  name: 'show',
  data() {
    return {
      word: ''
    };
  },
  async mounted() {
    this.word = await api.getWord(this.$route.params.id);
  }
};
</script>

<style scoped>
.ui.centered.card {
  width: 400px;
  margin-top: 2rem;
}
.ui.list .item {
  padding: 10px 0;
}
</style>
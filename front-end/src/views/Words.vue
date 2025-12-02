<template>
  <div>
    <div class="ui grid">
      <div class="sixteen wide column">
        <h1 class="ui header">
          <i class="list alternate outline icon"></i>
          <div class="content">
            Vocabulary List
            <div class="sub header">Manage your English, German, and Vietnamese vocabulary.</div>
          </div>
        </h1>
      </div>
    </div>

    <div class="ui segment raised">
      <table id="words" class="ui celled striped table selectable">
        <thead>
          <tr>
            <th><i class="united kingdom flag"></i> English</th>
            <th><i class="germany flag"></i> German</th>
            <th><i class="vietnam flag"></i> Vietnamese</th>
            <th class="center aligned" style="width: 180px;">Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(word, i) in words" :key="i">
            <td>{{ word.english }}</td>
            <td>{{ word.german }}</td>
            <td>{{ word.vietnamese }}</td>
            <td class="center aligned">
              <div class="ui icon buttons small">
                <router-link
                  :to="{ name: 'show', params: { id: word._id } }"
                  class="ui button teal"
                  title="View Details"
                >
                  <i class="eye icon"></i>
                </router-link>
                <router-link
                  :to="{ name: 'edit', params: { id: word._id } }"
                  class="ui button yellow"
                  title="Edit Word"
                >
                  <i class="edit icon"></i>
                </router-link>
                <button
                  class="ui button red"
                  @click.prevent="onDestroy(word._id)"
                  title="Delete Word"
                >
                  <i class="trash alternate icon"></i>
                </button>
              </div>
            </td>
          </tr>
          <tr v-if="words.length === 0">
            <td colspan="4" class="center aligned">
              <div class="ui placeholder segment">
                <div class="ui icon header">
                  <i class="file outline icon"></i>
                  No words found.
                </div>
                <router-link to="/words/new" class="ui primary button">Add New Word</router-link>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import { api } from '../helpers/helpers';

export default {
  name: 'Words',
  data() {
    return {
      words: []
    };
  },
  methods: {
    async onDestroy(id) {
      const sure = window.confirm('Are you sure you want to delete this word?');
      if (!sure) return;
      await api.deleteWord(id);
      this.flash('Word deleted successfully', 'success');
      const newWords = this.words.filter(word => word._id !== id);
      this.words = newWords;
    }
  },
  async mounted() {
    this.words = await api.getWords();
  }
};
</script>

<style scoped>
.ui.table th {
  background-color: #f9fafb;
}
</style>
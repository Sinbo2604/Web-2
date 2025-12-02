<template>
  <div>
    <div class="ui centered card">
      <div class="image">
        <img src="https://semantic-ui.com/images/avatar2/large/matthew.png" alt="Profile Image">
      </div>
      <div class="content">
        <a class="header">Nguyễn Trần Minh Nhật</a>
        <div class="meta">
          <span class="date">Student</span>
        </div>
        <div class="description">
          This page belongs to <b>Nguyễn Trần Minh Nhật</b> and has been added to the system working on my own to demonstrate my understanding of the concepts taught in the module.
        </div>
      </div>
      <div class="extra content">
        <a>
          <i class="university icon"></i>
          Greenwich University
        </a>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'about'
};
</script>

<style scoped>
.ui.centered.card {
  margin-top: 2rem;
  width: 400px;
}
</style>
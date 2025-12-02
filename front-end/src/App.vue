<template>
  <div id="app">
    <div class="ui inverted primary fixed menu">
      <div class="ui container">
        <router-link to="/" exact class="header item">
          <i class="book icon"></i> Vocab Manager
        </router-link>
        <router-link to="/words" class="item" active-class="active" exact>
          <i class="list icon"></i> Words
        </router-link>
        <router-link to="/words/new" class="item" active-class="active">
          <i class="plus circle icon"></i> New
        </router-link>
        <router-link to="/test" class="item" active-class="active">
          <i class="graduation cap icon"></i> Test
        </router-link>
        <div class="right menu">
          <router-link to="/about" class="item" active-class="active">
            <i class="user circle icon"></i> Profile
          </router-link>
        </div>
      </div>
    </div>

    <div class="ui main container">
      <flash-message class="myFlash"></flash-message>
      <transition name="fade" mode="out-in">
        <router-view></router-view>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app'
};
</script>

<style>
body {
  background-color: #f7f9fa;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

.ui.main.container {
  margin-top: 7em;
  min-height: 80vh;
}

.myFlash {
  width: 100%;
  margin-bottom: 20px;
  z-index: 1000;
}

/* Global Transition Effects */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}

/* Custom Scrollbar */
::-webkit-scrollbar {
  width: 8px;
}
::-webkit-scrollbar-track {
  background: #f1f1f1;
}
::-webkit-scrollbar-thumb {
  background: #888;
  border-radius: 4px;
}
::-webkit-scrollbar-thumb:hover {
  background: #555;
}
</style>
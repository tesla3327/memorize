<template>
  <div class="container">
    <div id="app">
      <add-passage v-if="passage === ''"
        @new-passage="addPassage"/>
      <memorizer v-else :text="passage" />
    </div>
  </div>
</template>

<script>
import AddPassage from './components/AddPassage.vue';
import Memorizer from './components/Memorizer.vue';
import './main.css';

export default {
  name: 'app',
  
  components: {
    AddPassage,
    Memorizer,
  },

  data() {
    return {
      passage: '',
    };
  },

  methods: {
    addPassage(passage) {
      this.passage = passage;
    }
  }
}
</script>

<style>
#app {
  /* width: 800px; */
}

.container {
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: row;
  justify-content: center;
}
</style>

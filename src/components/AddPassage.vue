<template>
  <div class="add-passage">
    <p>Enter some text:</p>
    <form @submit.prevent="onSubmit">
      <textarea autofocus
        autocomplete="off"
        rows="20"
        v-model.trim="text"
      />
      <button type="submit">Start</button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'AddPassage',

  data() {
    return {
      text: '',
    };
  },

  methods: {
    onSubmit() {
      this.$emit('new-passage', this.text);
    }
  }
}
</script>

<style>
.add-passage {
  width: 800px;
}
</style>
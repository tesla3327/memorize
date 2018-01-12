<template>
  <div class="memorizer">
    <h2>Memorizer</h2>
    <passage-part v-for="part in passage"
      :text="part"
      :key="part"
      @split="pos => splitPart(part, pos)"
      @merge="mergeParts"
    />
  </div>
</template>

<script>
import PassagePart from './PassagePart.vue';

export default {
  name: 'Memorizer',

  components: {
    PassagePart,
  },

  props: {
    text: String,
  },

  data() {
    return {
      passage: [],
    };
  },

  mounted() {
    this.passage = this.computePassage();
  },

  methods: {    
    computePassage() {
      return this.text
        .split('.')
        .filter(e => e !== '')
        .map(e => e.trim())
    },
    splitPart(part, pos) {
      const partIndex = this.passage.indexOf(part);
      const first = part.slice(0, pos);
      const second = part.slice(pos);
      this.passage.splice(partIndex, 1, first, second);
    },
    mergeParts(part) {
      const index = this.passage.indexOf(part);
      if (index < 0) {
        return;
      } else {
        const merged = `${this.passage[index]} ${this.passage[index + 1]}`;
        this.passage.splice(index, 2, merged);
      }
    }
  }
}
</script>

<style>
  .memorizer {

  }
</style>
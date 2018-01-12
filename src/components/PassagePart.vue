<template>
  <div :class="wrapperClassObj" ref="root">
    <div class="text" @click.stop="handleClick">
      <div v-if="splitMode">
        <p>
          <span v-for="(word, index) in words"
            :key="word + index"
            ref="wordElems">
            {{ word + ' ' }}
            <span class="splitter" v-if="index === closestWordToCursor">
              |
            </span>
          </span>
        </p>
      </div>
      <div v-else-if="editMode">
        <textarea autocomplete="off"
          rows="5"
          v-model.trim="passage" />
      </div>
      <div v-else>
        <p>{{ showCompressed ? compressed : passage }}</p>
      </div>
    </div>
    <div class="btn-group">
      <button @click="showCompressed = !showCompressed">
        {{ showCompressed ? 'Uncompress' : 'Compress' }}
      </button>
      <button @click="splitMode = !splitMode">
        {{ splitMode ? 'Cancel Split' : 'Split' }}
      </button>
      <button @click="editMode = !editMode">
        {{ editMode ? 'Save' : 'Edit' }}
      </button>
      <button @click="$emit('merge', passage)">Merge</button>
    </div>
  </div>
</template>

<script>
const isLetter = ch => ch.toLowerCase() !== ch.toUpperCase();

export default {
  name: 'PassagePart',

  props: {
    text: String,
  },

  data() {
    return {
      passage: '',
      showCompressed: false,
      splitMode: false,
      editMode: false,
      closestWordToCursor: -1,
    };
  },

  mounted() {
    // Attach listener to listen for mouse movements....
    this.$refs.root.addEventListener('mousemove', this.mouseMove.bind(this));
    this.passage = this.text;
  },

  methods: {
    handleClick() {
      if (this.splitMode) {
        const splitAtChar = this.words
          .slice(0, this.closestWordToCursor + 1)
          .join(' ')
          .length;

        this.$emit('split', splitAtChar);
        this.closestWordToCursor = -1;
        this.splitMode = false;
      }
    },
    mouseMove(e) {
      // Ignore if we aren't splitting anything
      if (this.splitMode) {
        const mousePos = {
          x: e.clientX,
          y: e.clientY,
        };
        const wordElems = this.$refs.wordElems;

        wordElems.forEach(elem => {
          elem.style.color = 'black';
        });

        const closest = wordElems.find(el => {
          const box = el.getBoundingClientRect();
          return (box.right > mousePos.x) &&
                (box.bottom > mousePos.y);
        });

        this.closestWordToCursor = wordElems.indexOf(closest);
      }
    },
  },

  computed: {
    wrapperClassObj() {
      return {
        'passage-part': true,
        'editing': this.splitMode,
      };
    },
    words() {
      return this.passage
        .split(' ')
        .map(e => e.trim())
        .filter(e => e !== '');
    },
    compressed() {
      const chars = this.passage.split('');
      const result = [];
      let inWord = false;

      chars.forEach(ch => {
        if (isLetter(ch) && !inWord) {
          inWord = true;
          // Convert uppercase I to lowercase to differentiate
          // between 'i' and 'l' in most fonts
          result.push(ch === 'I' ? 'i' : ch);
        } else if (!isLetter(ch)) {
          inWord = false;
          result.push(ch);
        }
      });

      return result.join('');
    }
  }
}
</script>

<style>
  .passage-part .splitter {
    color: coral;
  }

  .passage-part .text {
    width: 550px;
  }

  .passage-part p {
    padding: 0 1em;
  }

  .passage-part .btn-group {
    padding: 0.5em;
    width: 250px;
  }

  .passage-part {
    display: flex;
    border: 1px solid white;
  }

  .passage-part:hover {
    border-color: skyblue;
  }
  
  .passage-part.editing {
    border-color: lightcoral; 
  }
</style>
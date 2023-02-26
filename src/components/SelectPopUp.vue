<template>
  <div class="select-pop-up">
    <h2 class="select-pop-up__title">{{ title }}</h2>
    <select  class="select-pop-up__select" v-model="selectedOption">
      <option v-for="(option, index) in options" :key="index" :value="option">{{ option }}</option>
    </select>
    <button class="select-pop-up__btn" @click="close">Close</button>
    <button class="select-pop-up__btn" @click="add">Add to the current list</button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  props: {
    options: {
      type: Array,
      required: true,
    },
    title: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      selectedOption: '',
    };
  },
  methods: {
    close() {
      this.$emit('close');
    },
    add() {
      this.$emit('add', this.selectedOption);
    },
  },
});
</script>


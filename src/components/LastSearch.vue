<script setup>
import { ref, watch } from 'vue';

defineProps({
	lastSearch: {
		type: String,
		default: ''
	}
});
</script>

<template>
	<div><span v-if="lastSearch" class="search-label">Results for "{{ lastSearch }}"</span></div>
</template>

<style scoped>
  div {
    display:flex;
  }

	.search-label {
      font-family: 'Poppins';
      margin-right: auto;
      font-size: 2rem;
      font-weight: 900;
      color: #000000;
      background-color: var(--gcc-v-lt-green);
      background: radial-gradient(ellipse, rgba(114,169,127,0.6) 30%, rgba(114,169,127,0.02) 99%);
      flex-grow: 1;
      padding: 1rem;
    }

    @media screen and (max-width: 1600px) {
      .search-label {
        font-size: 1.5rem;
      }
    }
</style>
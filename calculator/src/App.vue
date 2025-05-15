<script lang="ts">
import { defineComponent } from 'vue';
import Calculator from './components/Calculator.vue';

export default defineComponent({
  name: 'App',
  components: {
    Calculator,
  },
});
</script>

<template>
  <div class="container">
    <Calculator />
  </div>
</template>

<style scoped>
header {
  line-height: 1.25;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

.container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0 auto;
  width: calc(100vw - 40px);
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>

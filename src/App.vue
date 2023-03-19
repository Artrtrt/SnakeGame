<template>
  <div>
    <SnakeGame />
  </div>
</template>


<script>
import Vue from 'vue';
import SnakeGame from '@/components/SnakeGame.vue'
export default Vue.extend({
  name:'App',
  components: {
    SnakeGame,
  },
  data() {
    return {
    }
  },
})
</script>
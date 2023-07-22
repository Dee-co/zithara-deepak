<template>
  <v-app>
    <v-main>
      <CurdOperation/>
    </v-main>
  </v-app>
</template>

<script>
import CurdOperation from './components/CurdOperation.vue'

export default {
  name: 'App',

  components: {
    CurdOperation,
  },

  data: () => ({
    //
  }),
}
</script>

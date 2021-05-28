<template>
  <v-container class="pa-5">
    <v-row align="end" justify="center">
      <v-col
        cols="12"
        sm="12"
        md="6"
        lg="5"
        xl="3"
      >
        <Registration />
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  layout: 'default',

  components: {
    Registration: () => import('@/components/Registration')
  },

}
</script>

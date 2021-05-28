<template>
  <v-container>
    <v-row align="center" justify="center">
      <v-col cols="6" md="8" lg="6" xl="4">
       <v-card>
        <v-list>
          <v-list-item>
            <v-list-item-avatar size="100">
              <img
                src="https://cdn.vuetifyjs.com/images/john.jpg"
                :alt="userData.name"
              >
            </v-list-item-avatar>

            <v-list-item-content>
              <v-list-item-title>{{ userData.name}}</v-list-item-title>
              <v-list-item-subtitle>{{ userData.email }}</v-list-item-subtitle>
              <v-list-item-subtitle>Rest of the user profile</v-list-item-subtitle>
              <v-list-item-subtitle>Rest of the user profile</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  export default {
    layout: 'basic',

     computed: {
      userData () {
        return this.$auth.user
      },
    },
  }
</script>
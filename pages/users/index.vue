<template>
  <v-data-table
    :headers="headers"
    :items="users"
    class="elevation-1"
    :loading="loading"
  >
   <template v-slot:top>
      <v-toolbar
        flat
      >
        <v-toolbar-title>User Lists</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
      </v-toolbar>
   </template>
  </v-data-table>
</template>

<script>
  export default {
    layout: 'basic',

    data: () => ({
      loading: false,
      headers: [
        {
          text: 'Name',
          align: 'start',
          sortable: false,
          value: 'name',
        },
        { text: 'Email', value: 'email' },
        { text: 'Locale', value: 'locale' },
        { text: 'Status', value: 'isActive', sortable: false },
      ],
      users: [],
    }),

    created () {
      this.initialize()
    },

    methods: {
      initialize () {
        this.loading  = true
        this.$axios.get('/user')
        .then(response => {
            this.users = response.data.data
        })
        .finally(() => {
          this.loading = false
        })
      },

    },
  }
</script>
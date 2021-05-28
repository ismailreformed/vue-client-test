<template>
  <v-app dark>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    
  </v-app>
</template>

<script>
export default {
  data () {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Welcome',
          to: '/'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Registraion',
          to: '/registraion'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Role',
          to: '/role'
        },
        { 
          icon: 'mdi-chart-bubble',
          title: 'User',
          to: '/user'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vue Client Test'
    }
  }
}
</script>

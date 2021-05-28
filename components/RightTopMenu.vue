<template>
  <div class="text-center">
    <v-menu
      v-model="menu"
      :close-on-content-click="false"
      :nudge-width="200"
      offset-x
    >
      <template v-slot:activator="{ on, attrs }">
 
        <v-list-item  
            v-bind="attrs"
            v-on="on"
        >
            <v-list-item-avatar>
                <img
                    src="https://cdn.vuetifyjs.com/images/john.jpg"
                    alt="John"
                >
            </v-list-item-avatar>
        </v-list-item>
      </template>

      <v-card>
        <v-list>
          <v-list-item>
            <v-list-item-avatar>
              <img
                src="https://cdn.vuetifyjs.com/images/john.jpg"
                alt="John"
              >
            </v-list-item-avatar>

            <v-list-item-content>
              <v-list-item-title>{{ userData.name}}</v-list-item-title>
              <v-list-item-subtitle>Creator of this app</v-list-item-subtitle>
            </v-list-item-content>

            <v-list-item-action>
              <v-btn
                :class="fav ? 'red--text' : ''"
                icon
                @click="fav = !fav"
              >
                <v-icon>mdi-heart</v-icon>
              </v-btn>
            </v-list-item-action>
          </v-list-item>
        </v-list>

        <v-divider></v-divider>

        <v-list>
          <v-list-item to="/profile">
            <v-list-item-title>Profile</v-list-item-title>
          </v-list-item>
        </v-list>
        
        <v-divider></v-divider>

        <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
                text
                color="red"
                @click="logout()"
            >
                Logout
            </v-btn>
            <v-spacer></v-spacer>
        </v-card-actions>
      </v-card>
    </v-menu>
  </div>
</template>

<script>
  export default {
    data: () => ({
      fav: true,
      menu: false,
      message: false,
      hints: true,
    }),

    computed: {
      userData () {
        return this.$auth.user
      },
    },

    methods: {
      logout() {
        this.$auth.logout()
      }
    }
  }
</script>
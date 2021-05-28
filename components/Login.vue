<template>
  <v-form
    ref="form"
    v-model="valid"
    lazy-validation
  >
    <v-card class="pa-5">
      <v-row dense align="center">
       <v-col>
        <h2 class="text-center">Login</h2>
        </v-col>

        <v-col align-self="start" cols="12">
          <v-text-field
            v-model="email"
            color="secondary"
            :rules="emailRules"
            outlined
            shaped
            label="Email"
            hide-details="auto"
            required
            type="text"
          />
        </v-col>
        <v-col align-self="start" cols="12">
          <v-text-field
            v-model="password"
            outlined
            color="secondary"
            :rules="passwordRules"
            :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
            label="password"
            :type="showPassword ? 'text' : 'password'"
            hide-details="auto"
            required
            @click:append="showPassword = !showPassword"
          />
        </v-col>

        <v-col align-self="end" cols="12" class="mt-5">
          <v-btn
            class="white--text"
            color="primary"
            block
            :loading="isLoging"
            @click="userLogin()"
          >
            Sign In
          </v-btn>
        </v-col>
      </v-row>
      <v-row align="end" justify="center" class="mt-5">
        <span>Don`t have an account ?  <v-btn
          :disabled="isLoging"
          color="red darken-2"
          class="px-0 mt-0 text-capitalize"
          text
          dense
          @click="gotoSignUp()"
        >
          Sign Up
        </v-btn>
        </span>
      </v-row>
    </v-card>
    <v-snackbar
      v-model="snackbar"
      :color="errorColor"
    >
      {{ errorMessage }}

      <template v-slot:action="{ attrs }">
        <v-btn
          color="white"
          text
          v-bind="attrs"
          @click="snackbar = false"
        >
          <v-icon>mdi-close</v-icon>
        </v-btn>
      </template>
    </v-snackbar>
  </v-form>
</template>

<script>
export default {
  data: () => ({
    valid: true,
    showPassword: false,

    phone: '',
    email: '',
    password: '',

    phoneRules: [
      v => !!v || 'Phone is required',
      // v => /\+?(88)?0?1[3456789][0-9]{8}\b/.test(v) || 'Number must be valid' | /.+@.+\..+/.test(v) || 'E-mail must be valid'
      v => /\+?(88)?0?1[3456789][0-9]{8}\b/.test(v) || 'Phone Number must be valid'
    ],
    
    emailRules: [
      v => !!v || 'Email is required',
      v => /.+@.+\..+/.test(v) || 'E-mail must be valid'
    ],

    passwordRules: [
      v => !!v || 'Password is required'
    ],

    isLoging: false,
    snackbar: false,
    errorMessage: '',
    errorColor: ''
  }),

  computed: {
    loginInfo () {
      const data = {
        email: this.email,
        password: this.password
      }
      return data
    }
  },

  methods: {
    validate () {
      this.$refs.form.validate()
    },

    reset () {
      this.$refs.form.reset()
    },

    resetValidation () {
      this.$refs.form.resetValidation()
    },

    gotoSignUp () {
      this.$router.push('/registration')
    },

    async userLogin () {
      if (!this.$refs.form.validate()) {
        this.errorMessage = 'Please input valid data'
        this.errorColor = 'error'
        this.snackbar = true
      } else {

        try {
          this.resetValidation()
          this.isLoging = true
          const response = await this.$auth.loginWith('local', { data: this.loginInfo })
          if (response) {
            this.$router.push('/')
          }
          this.isLoging = false
        } catch (err) {
          this.errorMessage = err.response.data.message
          this.errorColor = 'error'
          this.snackbar = true
          this.isLoging = false
        }
      }
    }
  }
}
</script>

<template>
  <v-form
    ref="form"
    v-model="valid"
    lazy-validation
  >
    <v-card class="pa-5">
      <v-row dense align="center">
        <v-col>
        <h2 class="text-center">Registartion</h2>
        </v-col>
        
        <v-col align-self="start" cols="12">
          <v-text-field
            v-model="name"
            outlined
            color="secondary"
            hide-details="auto"
            :rules="nameRules"
            label="Your Name"
            :error-messages="errorMessages.name"
            required
          />
        </v-col>
    
        <v-col align-self="start" cols="12">
          <v-text-field
            v-model="email"
            color="secondary"
            :error-messages="errorMessages.email"
            :rules="emailRules"
            outlined
            hide-details="auto"
            label="E-mail"
            required
          />
        </v-col>

         
        <v-col align-self="start" cols="12">
          <v-text-field
            v-model="password"
            color="secondary"
            :error-messages="errorMessages.password"
            :rules="passwordRules"
            outlined
            hide-details="auto"
            label="Password"
            required
          />
        </v-col>
  
        <v-col align-self="center" cols="12">
          <v-btn
            class="text-capitalize white--text"
            color="primary"
            block
            :loading="isRegistering"
            @click="registration()"
          >
            Sign Up
          </v-btn>
        </v-col>
      </v-row>

      <v-row align="end" justify="center" class="mt-5">
        <span>Already have an account ?
          <v-btn
            :disabled="isRegistering"
            color="red darken-2"
            class="px-0  text-capitalize"
            text
            dense
            @click="gotoLogin()"
          >
            Sign In
          </v-btn>
        </span>
      </v-row>
    </v-card>
    <v-snackbar
      v-model="snackbar"
      :color="errorColor"
    >
      {{ message }}

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
  props: {
    isDialog: {
      type: Boolean,
      required: false,
      default () {
        return false
      }
    }
  },

  data: () => ({
    valid: true,
    name: '',
    email: null,
    password: '',
    
    nameRules: [
      v => !!v || 'Name is required',
      v => (v && v.length >= 3) || 'Name must be greater than 2 characters'
    ],

    passwordRules: [
      v => !!v || 'Password is required',
    ],

    emailRules: [
      v => /.+@.+\..+/.test(v) || 'E-mail must be valid'
    ],

    isRegistering: false,
    snackbar: false,
    message: '',
    errorColor: '',
    errorMessages: {
      name: '',
      email: '',
      password: ''
    }
  }),

  computed: {
    registrationInfo () {
      const data = {
        name: this.name,
        email: this.email,
        password: this.password
      }
      return data
    },
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
      this.resetErrorMessages(this.errorMessages)
    },

    gotoLogin () {
      this.$router.push('/login')
    },

    registration () {
      if (!this.$refs.form.validate()) {
        this.message = 'Please Input valid data'
        this.errorColor = 'error'
        this.snackbar = true
      } else {
        this.resetValidation()
        this.isRegistering = true
        this.$axios.post('/user', this.registrationInfo)
          .then((response) => {
            this.message = 'Succss, You account has created.'
            this.errorColor = 'success'
            this.snackbar = true
            this.$router.push('/')
          })
          .catch((error) => {
            this.message = error.response.data.message
            this.errorColor = 'error'
            this.snackbar = true
            this.setErrorMessages(error.response.data.errors)
          })
          .finally(() => {
            this.isRegistering = false
          })
      }
    },

    setErrorMessages (messages) {
      for (const field in messages) {
        if (field === 'user.email') {
          this.errorMessages.email = 'Email has already been Registered.'
        }
        if (field === 'user.phone') {
          this.errorMessages.phone = 'Phone has already been Registered'
        }
        this.errorMessages[field] = messages[field]
      }
    },

    resetErrorMessages (messages) {
      for (const field in messages) {
        this.errorMessages[field] = ''
      }
    }
  }
}
</script>

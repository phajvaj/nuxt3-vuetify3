<template>
  <v-img
      class="mx-auto my-6"
      max-width="228"
      src="https://cdn.vuetifyjs.com/docs/images/logos/vuetify-logo-v3-slim-text-light.svg"
  ></v-img>
  <v-card
      class="mx-auto pa-12 pb-8"
      elevation="8"
      max-width="448"
      rounded="lg"
  >
    <div class="text-subtitle-1 text-medium-emphasis">Register</div>
    <v-divider class="border-opacity-50" color="warning"></v-divider>
    <v-form ref="form" @submit.prevent="signUp">
      <v-text-field
          v-model="firstname"
          :rules="rules.firstnameRules"
          label="First name"
          required
      ></v-text-field>

      <v-text-field
          v-model="lastname"
          :rules="rules.lastnameRules"
          label="Last name"
          required
      ></v-text-field>

      <v-text-field
          v-model="company"
          :rules="rules.companyRules"
          label="Company (Optional)"
          required
      ></v-text-field>

      <v-text-field
          v-model="username"
          density="compact"
          :rules="rules.emailRules"
          placeholder="Username"
          prepend-inner-icon="mdi-email-outline"
          required
      ></v-text-field>

      <v-text-field
          v-model="password"
          :append-inner-icon="visible ? 'mdi-eye-off' : 'mdi-eye'"
          :type="visible ? 'text' : 'password'"
          density="compact"
          placeholder="Enter your password"
          prepend-inner-icon="mdi-lock-outline"
          :rules="rules.passwordRules"
          required
          @click:append-inner="visible = !visible"
      ></v-text-field>

      <v-checkbox
          v-model="checkbox"
          :rules="[v => !!v || 'You must agree to continue!']"
          label="Do you agree?"
          required
      ></v-checkbox>

      <div class="d-flex flex-column">
        <v-btn
            class="mt-4"
            color="success"
            block
            type="submit"
        >
          Register
        </v-btn>

        <v-btn
            class="mt-4"
            color="error"
            block
            type="reset"
        >
          Reset Form
        </v-btn>
      </div>
    </v-form>
  </v-card>
  <v-snackbar
      v-model="snackbar"
      timeout="2500"
  >
    {{ txtAlert }}

    <template v-slot:actions>
      <v-btn
          color="blue"
          variant="text"
          @click="snackbar = false"
      >
        Close
      </v-btn>
    </template>
  </v-snackbar>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { VForm } from 'vuetify/components'
// import { useSupabaseClient } from '@nuxtjs/supabase';

definePageMeta({
  layout: 'noauth'
})

const form = ref<InstanceType<typeof VForm> | null>(null)
const txtAlert = ref('')
const username = ref('')
const password = ref('')
const firstname = ref('')
const lastname = ref('')
const company = ref('')
const client = useSupabaseClient()
const user = useSupabaseUser()
const checkbox = ref(false)
const loading = ref(false)
const isSignUp = ref(false)
const snackbar = ref(false)
const visible = ref(false)

const rules = {
  firstnameRules: [
    v => !!v || 'Firstname is required',
    v => (v && v.length >= 6) || 'Firstname must be less than 6 characters',
  ],
  lastnameRules: [
    v => !!v || 'Lastname is required',
    v => (v && v.length >= 6) || 'Lastname must be less than 6 characters',
  ],
  companyRules: [
    v => !!v || 'Company is required',
    v => (v && v.length >= 10) || 'Company must be less than 10 characters',
  ],
  emailRules: [
    value => {
      if (value) return true

      return 'Username is requred.'
    },
  ],
  passwordRules: [
    v => !!v || 'Password is required',
    v => (v && v.length >= 8) || 'Password must be less than 8 characters',
  ],
};

watchEffect(async () => {
  console.log(`data-user: ${user}`);
  // if (user.value) {
  //   await navigateTo('/')
  // }
});

const signUp = async () => {
  const { valid } = await form.value.validate();
  console.log(`valid: ${valid}`)
  if (valid) {
    loading.value = true
    const {error} = await client.auth.signUp({
      email: `${username.value}@phingosoft.com`,
      password: password.value,
      options: {
        data: {
          first_name: firstname.value,
          last_name: lastname.value,
          company: company.value
        }
      }
    })
    if (error) {
      loading.value = false
      txtAlert.value = `Failed to fetch: ${error.toString()}`
    }else {
      txtAlert.value = 'Register success!';
      navigateTo('/login')
    }
    snackbar.value = true;
  }
}

const clearError = () => {
  authError.value = ''
}
</script>

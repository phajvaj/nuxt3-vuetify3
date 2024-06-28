<template>
  <div>
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
      <div class="text-subtitle-1 text-medium-emphasis">Login</div>

      <v-form ref="form" @submit.prevent="submitForm">
        <v-text-field
            v-model="username"
            density="compact"
            placeholder="Username"
            prepend-inner-icon="mdi-email-outline"
            variant="outlined"
            :rules="[rules.required, rules.minLength]"
        ></v-text-field>

        <div class="text-subtitle-1 text-medium-emphasis d-flex align-center justify-space-between">
          Password

          <a
              class="text-caption text-decoration-none text-blue"
              href="#"
              rel="noopener noreferrer"
              target="_blank"
          >
            Forgot login password?</a>
        </div>

        <v-text-field
            v-model="password"
            :append-inner-icon="visible ? 'mdi-eye-off' : 'mdi-eye'"
            :type="visible ? 'text' : 'password'"
            density="compact"
            placeholder="Enter your password"
            prepend-inner-icon="mdi-lock-outline"
            variant="outlined"
            @click:append-inner="visible = !visible"
            :rules="[rules.required, rules.minLength]"
        ></v-text-field>

        <v-card
            class="mb-12"
            color="surface-variant"
            variant="tonal"
        >
          <v-card-text class="text-medium-emphasis text-caption">
            Warning: After 3 consecutive failed login attempts, you account will be temporarily locked for three hours. If you must login now, you can also click "Forgot login password?" below to reset the login password.
          </v-card-text>
        </v-card>

        <v-btn
            class="mb-8"
            color="blue"
            size="large"
            variant="tonal"
            block
            type="submit"
        >
          Log In
        </v-btn>
      </v-form>

      <v-card-text class="text-center">
        <a
            class="text-blue text-decoration-none"
            href="/register"
            rel="noopener noreferrer"
        >
          Sign up now <v-icon icon="mdi-chevron-right"></v-icon>
        </a>
      </v-card-text>
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
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { VForm } from 'vuetify/components'

definePageMeta({
  layout: "noauth"
})

const visible = ref(false)
const snackbar = ref(false)
const txtAlert = ref('')
const username = ref('')
const password = ref('')
const isSubmitting = ref(false)
const form = ref<InstanceType<typeof VForm> | null>(null)
const supabase = useSupabaseClient()

const rules = {
  required: (v: string) => !!v || 'จำเป็นต้องกรอกข้อมูล',
  minLength: (v: string) => v.length >= 6 || 'ต้องมีความยาวอย่างน้อย 6 ตัวอักษร',
}

const submitForm = async () => {
  const { valid } = await form.value!.validate()

  if (valid) {
    isSubmitting.value = true
    try {
      const { data, error } = await supabase.auth.signInWithPassword({
        email: `${username.value}@phingosoft.com`,
        password: password.value,
      })
      if (error){
        txtAlert.value = `เกิดข้อผิดพลาดในการเข้าสู่ระบบ: ${error}`;
        snackbar.value = true;
      } else {
        // ล็อกอินสำเร็จ นำทางไปยังหน้าหลัก
        txtAlert.value = `ยินดีต้อนเข้าสู่ระบบ`;
        snackbar.value = true;
        await navigateTo('/home');
      }
    } catch (error) {
      txtAlert.value = `เกิดข้อผิดพลาดในการเข้าสู่ระบบ: ${error}`
      console.error('เกิดข้อผิดพลาดในการเข้าสู่ระบบ:', error)
      snackbar.value = true;
      // แสดงข้อความแจ้งเตือนให้ผู้ใช้ทราบ
    } finally {
      isSubmitting.value = false
    }
  }
}
</script>

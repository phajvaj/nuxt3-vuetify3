<template>
  <v-container>
    <v-row justify="center">
      <v-col cols="12" sm="8" md="6">
        <v-card>
          <v-card-title class="text-center">เข้าสู่ระบบ</v-card-title>
          <v-card-text>
            <v-form ref="form" @submit.prevent="submitForm">
              <v-text-field
                  v-model="username"
                  :rules="[rules.required, rules.minLength]"
                  label="ชื่อผู้ใช้"
                  required
                  prepend-icon="mdi-account"
              ></v-text-field>
              <v-text-field
                  v-model="password"
                  :rules="[rules.required, rules.minLength]"
                  label="รหัสผ่าน"
                  type="password"
                  required
                  prepend-icon="mdi-lock"
              ></v-text-field>
              <v-btn
                  type="submit"
                  color="primary"
                  block
                  class="mt-4"
                  :disabled="isSubmitting"
                  :loading="isSubmitting"
              >
                เข้าสู่ระบบ
              </v-btn>
            </v-form>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { VForm } from 'vuetify/components'

definePageMeta({
  layout: "noauth"
})

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
        username: username.value,
        password: password.value,
      })
      if (error) throw error
      // ล็อกอินสำเร็จ นำทางไปยังหน้าหลัก
      navigateTo('/')
    } catch (error) {
      console.error('เกิดข้อผิดพลาดในการเข้าสู่ระบบ:', error)
      // แสดงข้อความแจ้งเตือนให้ผู้ใช้ทราบ
    } finally {
      isSubmitting.value = false
    }
  }
}
</script>

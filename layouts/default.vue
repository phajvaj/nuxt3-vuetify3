<template>
  <v-app>
    <v-app-bar app>
      <v-app-bar-title>My Nuxt 3</v-app-bar-title>
      <v-spacer></v-spacer>
      <v-btn to="/" text>Home</v-btn>
      <v-btn to="/login" text>Login</v-btn>
      <v-btn @click="toggleTheme" icon>
        <v-icon>{{ isDark ? 'mdi-weather-sunny' : 'mdi-weather-night' }}</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <v-container>
        <slot />
      </v-container>
    </v-main>

    <v-footer app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script setup>
import { ref, watch } from 'vue';
import { useTheme } from 'vuetify';

const theme = useTheme()
const isDark = ref(theme.global.current.value?.dark || {value: false})

const toggleTheme = () => {
  theme.global.name.value = isDark.value ? 'myDrakTheme' : 'myTheme'
  isDark.value = !isDark.value
}

// Optional: Persist theme preference
watch(isDark, (newValue) => {
  localStorage.setItem('darkTheme', newValue)
})
// Optional: Load theme preference on mount
onMounted(() => {
  const savedTheme = localStorage.getItem('darkTheme')
  if (savedTheme !== null) {
    isDark.value = savedTheme === 'true'
    theme.global.name.value = isDark.value ? 'myDarkTheme' : 'myTheme'
  }
})
</script>

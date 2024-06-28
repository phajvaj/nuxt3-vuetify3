<template>
  <v-bottom-navigation :elevation="2" grow>
    <v-btn value="recent">
      <v-icon>mdi-history</v-icon>

      <span>Recent</span>
    </v-btn>

    <v-btn value="favorites">
      <v-icon>mdi-heart</v-icon>

      <span>Favorites</span>
    </v-btn>

    <v-btn value="nearby">
      <v-icon>mdi-map-marker</v-icon>

      <span>Nearby</span>
    </v-btn>
  </v-bottom-navigation>

  <p v-if="user" class="fVeafc in">Hi {{ user.user_metadata.first_name }} {{ user.user_metadata.last_name }}</p>
  <p v-else class="fVeafc">unauthenticated</p>
</template>
<script setup>
const client = useSupabaseClient()
const user = useSupabaseUser()

definePageMeta({
  layout: "auth"
})

console.log(user)

useHead({
  title: 'หน้าหลัก',
  meta: [
    { name: 'description', content: 'nuxt3, vuetify3, doisoft' }
  ]
})
</script>

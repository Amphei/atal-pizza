<script setup lang="ts">
import { toast } from 'vue3-toastify'
import type { CategoryOrOptional } from '~/types'

definePageMeta({
  middleware: 'unauthenticated',
})

const isAdmin = await useIsAdmin()

const isLoading = ref(false)
const { data: categories } = useFetch<CategoryOrOptional>('/api/categories')

const itemInfo = reactive({
  name: '',
  description: '',
  basePrice: 0,
  image: '',
  sizes: [],
  extraIngredients: [],
})

async function handleCreateMenuItem() {
  isLoading.value = true
  try {
    const res = await $fetch('/api/menu-items', {
      method: 'POST',
      body: itemInfo,
    })

    if (res) {
      navigateTo('/menu-items')
    }
  } catch (err: any) {
    toast.error(err.data.message)
  } finally {
    isLoading.value = false
  }
}

watchEffect(async () => {
  if (!isAdmin) {
    await navigateTo('/')
  }
})
</script>

<template>
  <section class="mt-8 max-w-2xl mx-auto">
    <Title>{{ 'Menu Items | Create' }}</Title>

    <UserTabs :isAdmin="isAdmin" />

    <Loader v-if="isLoading" />

    <div class="max-w-xl mx-auto mt-8">
      <NuxtLink to="/menu-items" class="button"
        >Show all menu items
        <Icon class="h-6 w-6" name="material-symbols:arrow-circle-left-outline"
      /></NuxtLink>
    </div>
    <MenuItemForm
      :itemInfo="itemInfo"
      :categories="categories"
      :onSubmit="handleCreateMenuItem" />
  </section>
</template>

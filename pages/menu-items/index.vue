<script lang="ts" setup>
import type { MenuItemType } from '~/types'

definePageMeta({
  middleware: 'unauthenticated',
})

const isAdmin = await useIsAdmin()

const { data: menuItems, pending } = await useFetch<MenuItemType[]>(
  '/api/menu-items',
)

watchEffect(async () => {
  if (!isAdmin) {
    await navigateTo('/')
  }
})
</script>

<template>
  <section class="mt-8 max-w-xl mx-auto">
    <Title>{{ 'Menu Items' }}</Title>

    <UserTabs :isAdmin="isAdmin" />

    <div class="mt-8">
      <NuxtLink to="/menu-items/new" class="button"
        >Create new menu item
        <Icon
          class="h-6 w-6"
          name="material-symbols:arrow-circle-right-outline"
      /></NuxtLink>
    </div>
    <Loader v-if="pending" />

    <div class="text-sm text-slate-500 mt-8">Edit menu items:</div>
    <div class="grid md:grid-cols-3 gap-2" v-if="menuItems!.length > 0">
      <NuxtLink
        v-for="item in menuItems"
        :key="item.name"
        class="bg-slate-200 rounded-lg p-4"
        :to="`/menu-items/edit/${item._id}`">
        <div class="relative">
          <NuxtImg
            :src="item.image"
            :alt="item.name"
            provider="s3Provider"
            class="rounded-md block mx-auto" />
        </div>
        <p class="text-center">{{ item.name }}</p>
      </NuxtLink>
    </div>
  </section>
</template>

<template>
  <header class="bg-indigo-600">
    <div class="px-4 md:px-0 container mx-auto h-20 flex justify-between items-center text-white">
      <NuxtLink :to="{ name: 'index' }" class="inline-flex items-center gap-x-5">
        <SvgIcon name="icons/logo" class="w-10 h-10" />
        Task editor
      </NuxtLink>
      <NuxtLink v-if="$nuxt.$route.name == 'index'" :to="{ name: 'add' }" class="bg-white text-indigo-600 px-4 py-2 font-medium rounded-md">
        Add
      </NuxtLink>
    </div>
  </header>
</template>

<template>
  <div class="min-h-screen bg-gray-950 text-white flex flex-col items-center justify-center px-6 py-10">
    <header class="mb-16 text-center">
      <h1 class="text-5xl md:text-6xl font-extrabold text-white tracking-wide mb-4">Benedyk Blog</h1>
    </header>

    <div class="grid grid-cols-1 sm:grid-cols-2 gap-10 w-full max-w-4xl">
      <NuxtLink
          to="/BlogPosts"
          class="bg-gray-800 rounded-xl p-8 hover:bg-gray-700 transition duration-300 shadow-md hover:shadow-xl"
      >
        <h2 class="text-2xl font-semibold text-cyan-400 mb-3">Кастомна таблиця</h2>
      </NuxtLink>

      <NuxtLink
          to="/BlogPostsUI"
          class="bg-gray-800 rounded-xl p-8 hover:bg-gray-700 transition duration-300 shadow-md hover:shadow-xl"
      >
        <h2 class="text-2xl font-semibold text-emerald-400 mb-3">NuxtUI таблиця</h2>
      </NuxtLink>
    </div>
  </div>
</template>

<template>
  <div class="min-h-screen p-8" style="background: linear-gradient(135deg, #f8fafc 0%, #e0f2fe 100%);">
    <!-- Home Button -->
    <NuxtLink
        to="/"
        class="fixed top-8 left-8 z-20 inline-flex items-center px-4 py-2 border text-gray-700 rounded-xl font-medium shadow-lg hover:shadow-xl hover:-translate-y-0.5 transition-all duration-200 group"
        style="background: rgba(255, 255, 255, 0.9); backdrop-filter: blur(8px); border-color: #e5e7eb;"
    >
      <svg class="w-5 h-5 mr-2 group-hover:-translate-x-0.5 transition-transform duration-200" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 19l-7-7m0 0l7-7m-7 7h18" />
      </svg>
      На головну
    </NuxtLink>

    <!-- Main Content -->
    <div class="max-w-7xl mx-auto pt-16">
      <!-- Header Section -->
      <div class="text-center mb-10">
        <h1 class="text-4xl md:text-5xl font-bold text-gray-900 mb-4">
          Кастомна таблиця
        </h1>
      </div>

      <!-- Action Bar -->
      <div class="flex justify-between items-center mb-8">
        <div class="text-gray-600 font-medium">
          Всього постів: {{ posts.length }}
        </div>
        <NuxtLink
            to="/admin/blog/posts/create"
            class="inline-flex items-center px-6 py-3 text-white font-semibold rounded-xl shadow-lg hover:shadow-xl hover:-translate-y-0.5 transition-all duration-200 group"
            style="background: linear-gradient(135deg, #3b82f6 0%, #8b5cf6 100%);"
        >
          <svg class="w-5 h-5 mr-2 group-hover:rotate-90 transition-transform duration-200" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6" />
          </svg>
          Додати новий пост
        </NuxtLink>
      </div>

      <!-- Table Card -->
      <div class="rounded-2xl shadow-xl border overflow-hidden" style="background: rgba(255, 255, 255, 0.8); backdrop-filter: blur(8px); border-color: rgba(255, 255, 255, 0.2);">
        <!-- Loading State -->
        <template v-if="loading">
          <div class="flex items-center justify-center py-20">
            <div class="flex items-center space-x-3">
              <div class="animate-spin rounded-full h-8 w-8 border-b-2" style="border-color: #3b82f6;"></div>
              <span class="text-lg font-medium text-gray-600">Завантаження постів...</span>
            </div>
          </div>
        </template>

        <!-- Empty State -->
        <template v-else-if="posts.length === 0">
          <div class="flex items-center justify-center py-20">
            <div class="text-center">
              <div class="w-16 h-16 rounded-full flex items-center justify-center mx-auto mb-4" style="background-color: #f3f4f6;">
                <svg class="w-8 h-8 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
                </svg>
              </div>
              <p class="text-lg font-medium text-gray-600">Постів поки що немає</p>
              <p class="text-gray-500 mt-1">Створіть свій перший пост, щоб почати</p>
            </div>
          </div>
        </template>

        <!-- Table -->
        <template v-else>
          <div class="overflow-x-auto">
            <table class="w-full">
              <!-- Table Header -->
              <thead class="bg-gray-50 border-b-2 border-gray-200">
              <tr>
                <th class="text-left py-4 px-6 text-sm font-bold text-gray-600 uppercase tracking-wider">#</th>
                <th class="text-left py-4 px-6 text-sm font-bold text-gray-600 uppercase tracking-wider">Автор</th>
                <th class="text-left py-4 px-6 text-sm font-bold text-gray-600 uppercase tracking-wider">Категорія</th>
                <th class="text-left py-4 px-6 text-sm font-bold text-gray-600 uppercase tracking-wider">Заголовок</th>
                <th class="text-left py-4 px-6 text-sm font-bold text-gray-600 uppercase tracking-wider">Дата публікації</th>
                <th class="text-left py-4 px-6 text-sm font-bold text-gray-600 uppercase tracking-wider">Статус</th>
              </tr>
              </thead>
              <!-- Table Body -->
              <tbody class="divide-y divide-gray-100">
              <tr
                  v-for="post in posts"
                  :key="post.id"
                  class="hover:bg-gray-50 transition-colors duration-200"
                  :class="{ 'bg-yellow-50': !post.is_published }"
              >
                <!-- ID -->
                <td class="py-4 px-6 text-sm text-gray-700 font-medium">
                  #{{ post.id }}
                </td>

                <!-- Author -->
                <td class="py-4 px-6 text-sm text-gray-700">
                  {{ post.user?.name || 'Невідомий автор' }}
                </td>

                <!-- Category -->
                <td class="py-4 px-6 text-sm text-gray-700">
                  {{ post.category?.title || 'Без категорії' }}
                </td>

                <!-- Title -->
                <td class="py-4 px-6 text-sm">
                  <NuxtLink
                      :to="`/admin/blog/posts/${post.id}/edit`"
                      class="font-semibold hover:underline transition-colors duration-200"
                      :class="post.is_published ? 'text-blue-600 hover:text-blue-800' : 'text-gray-600 hover:text-gray-800'"
                  >
                    {{ post.title }}
                  </NuxtLink>
                </td>

                <!-- Date -->
                <td class="py-4 px-6 text-sm text-gray-700">
                  {{ post.published_at ? new Date(post.published_at).toLocaleDateString('uk-UA', { year: 'numeric', month: 'short', day: 'numeric', hour: '2-digit', minute: '2-digit' }) : 'Не опубліковано' }}
                </td>

                <!-- Status -->
                <td class="py-4 px-6 text-sm">
                    <span
                        class="inline-flex items-center px-3 py-1 rounded-full text-xs font-semibold"
                        :class="post.is_published
                        ? 'bg-green-100 text-green-800 border border-green-200'
                        : 'bg-yellow-100 text-yellow-800 border border-yellow-200'"
                    >
                      {{ post.is_published ? 'Опубліковано' : 'Чернетка' }}
                    </span>
                </td>
              </tr>
              </tbody>
            </table>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

interface Post {
  id: number;
  title: string;
  slug: string;
  is_published: boolean;
  published_at: string | null;
  user_id: number;
  category_id: number;
  user: { name: string };
  category: { title: string };
}

const posts = ref<Post[]>([]);
const loading = ref(false);
const config = useRuntimeConfig();

const getPosts = async () => {
  loading.value = true;
  try {
    let allPosts: Post[] = [];
    let currentPage = 1;
    let totalPages = 1;

    do {
      const apiUrl = `${config.public.apiBase}/blog/posts?page=${currentPage}&per_page=50`;
      console.log(`Завантажуємо сторінку ${currentPage} з ${apiUrl}`);

      const response = await $fetch<{
        data: Post[],
        meta: {
          current_page: number,
          last_page: number,
          total: number
        }
      }>(apiUrl);

      allPosts = [...allPosts, ...response.data];
      totalPages = response.meta.last_page;
      currentPage++;

      console.log(`Завантажено ${response.data.length} постів з сторінки ${response.meta.current_page}`);
      console.log(`Всього сторінок: ${totalPages}, поточна сторінка: ${response.meta.current_page}`);

    } while (currentPage <= totalPages);

    console.log(`Всього завантажено постів: ${allPosts.length}`);
    posts.value = allPosts;
  } catch (error) {
    console.error("Error fetching posts:", error);
  } finally {
    loading.value = false;
  }
};

onMounted(() => {
  getPosts();
});
</script>
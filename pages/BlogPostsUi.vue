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
        <div class="inline-flex items-center justify-center w-16 h-16 rounded-2xl mb-6 shadow-lg" style="background: linear-gradient(135deg, #3b82f6 0%, #8b5cf6 100%);">
          <svg class="w-8 h-8 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 20H5a2 2 0 01-2-2V6a2 2 0 012-2h10a2 2 0 012 2v1m2 13a2 2 0 01-2-2V7m2 13a2 2 0 002-2V9a2 2 0 00-2-2h-2m-4-3v8m0 0V9a2 2 0 012-2h2M7 3a2 2 0 012 2v1M9 7h6" />
          </svg>
        </div>
        <h1 class="text-4xl md:text-5xl font-bold text-gray-900 mb-4">
          Управління постами
        </h1>
      </div>

      <!-- Action Button -->
      <div class="flex justify-end mb-8">
        <NuxtLink
            to="/admin/blog/posts/create"
            class="inline-flex items-center px-6 py-3 text-white font-semibold rounded-xl shadow-lg hover:shadow-xl hover:-translate-y-0.5 transition-all duration-200 group"
            style="background: linear-gradient(135deg, #3b82f6 0%, #8b5cf6 100%);"
            @mouseover="$event.target.style.background = 'linear-gradient(135deg, #2563eb 0%, #7c3aed 100%)'"
            @mouseout="$event.target.style.background = 'linear-gradient(135deg, #3b82f6 0%, #8b5cf6 100%)'"
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
        <template v-if="pending">
          <div class="flex items-center justify-center py-20">
            <div class="flex items-center space-x-3">
              <div class="animate-spin rounded-full h-8 w-8 border-b-2" style="border-color: #3b82f6;"></div>
              <span class="text-lg font-medium text-gray-600">Завантаження постів...</span>
            </div>
          </div>
        </template>

        <!-- Error State -->
        <template v-else-if="error">
          <div class="flex items-center justify-center py-20">
            <div class="text-center">
              <div class="w-16 h-16 rounded-full flex items-center justify-center mx-auto mb-4" style="background-color: #fee2e2;">
                <svg class="w-8 h-8" style="color: #dc2626;" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
              </div>
              <p class="text-lg font-medium" style="color: #dc2626;">Помилка завантаження постів</p>
              <p class="text-gray-500 mt-1">{{ error.message }}</p>
            </div>
          </div>
        </template>

        <!-- Empty State -->
        <template v-else-if="paginatedPosts.length === 0 && posts.length === 0">
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
                <th class="text-left py-4 px-6 text-sm font-bold text-gray-600 uppercase tracking-wider">Дата публікації</th>
                <th class="text-left py-4 px-6 text-sm font-bold text-gray-600 uppercase tracking-wider">Автор</th>
                <th class="text-left py-4 px-6 text-sm font-bold text-gray-600 uppercase tracking-wider">Категорія</th>
                <th class="text-left py-4 px-6 text-sm font-bold text-gray-600 uppercase tracking-wider">Заголовок</th>
                <th class="text-left py-4 px-6 text-sm font-bold text-gray-600 uppercase tracking-wider">Статус</th>
              </tr>
              </thead>
              <!-- Table Body -->
              <tbody class="divide-y divide-gray-100">
              <tr
                  v-for="post in paginatedPosts"
                  :key="post.id"
                  class="hover:bg-gray-50 transition-colors duration-200"
                  :class="{ 'bg-yellow-50': !post.is_published }"
              >
                <!-- ID -->
                <td class="py-4 px-6 text-sm text-gray-700 font-medium">
                  #{{ post.id }}
                </td>

                <!-- Date -->
                <td class="py-4 px-6 text-sm text-gray-700">
                  {{ formatDate(post.date) }}
                </td>

                <!-- Author -->
                <td class="py-4 px-6 text-sm text-gray-700">
                  {{ post.name || 'Невідомий автор' }}
                </td>

                <!-- Category -->
                <td class="py-4 px-6 text-sm text-gray-700">
                  {{ post.category || 'Без категорії' }}
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

          <!-- Pagination Footer -->
          <div class="px-6 py-6 bg-gray-50 border-t border-gray-200">
            <!-- Results Info -->
            <div class="text-center mb-4">
              <span class="text-sm text-gray-600 font-medium">
                Показано {{ startIndex }} - {{ endIndex }} з {{ posts.length }} результатів
              </span>
            </div>

            <!-- Pagination Controls - Centered -->
            <div class="flex justify-center items-center">
              <div class="flex items-center bg-white rounded-lg shadow-sm border border-gray-200 overflow-hidden">
                <!-- Previous Button -->
                <button
                    @click="goToPage(currentPage - 1)"
                    :disabled="currentPage === 1"
                    class="px-4 py-2 text-sm font-medium text-gray-500 hover:bg-gray-50 hover:text-gray-700 disabled:opacity-50 disabled:cursor-not-allowed transition-colors duration-200 border-r border-gray-200"
                >
                  <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
                  </svg>
                </button>

                <!-- Page Numbers -->
                <button
                    v-for="(page, index) in visiblePages"
                    :key="page"
                    @click="goToPage(page)"
                    class="px-4 py-2 text-sm font-medium transition-colors duration-200 min-w-[40px]"
                    :class="[
                    page === currentPage
                      ? 'bg-blue-600 text-white'
                      : 'text-gray-700 hover:bg-gray-50',
                    index < visiblePages.length - 1 ? 'border-r border-gray-200' : ''
                  ]"
                >
                  {{ page }}
                </button>

                <!-- Next Button -->
                <button
                    @click="goToPage(currentPage + 1)"
                    :disabled="currentPage === totalPages"
                    class="px-4 py-2 text-sm font-medium text-gray-500 hover:bg-gray-50 hover:text-gray-700 disabled:opacity-50 disabled:cursor-not-allowed transition-colors duration-200 border-l border-gray-200"
                >
                  <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
                  </svg>
                </button>
              </div>
            </div>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import { NuxtLink } from '#components';

type Post = {
  id: string;
  name: string;
  category: string;
  title: string;
  date: string;
  is_published: boolean;
};

const posts = ref<Post[]>([]);
const config = useRuntimeConfig();
const currentPage = ref(1);
const pageSize = ref(10);

const { data, pending, error, refresh } = await useAsyncData(
    'all-blog-posts',
    async () => {
      let allPosts: Post[] = [];
      let currentPage = 1;
      let lastPage = 1;

      do {
        const response: any = await $fetch(`${config.public.apiBase}/blog/posts?page=${currentPage}`);
        const pagePosts = response.data.map((post: any) => ({
          id: String(post.id),
          name: post.user?.name || '',
          category: post.category?.title || '',
          title: post.title,
          date: post.published_at,
          is_published: post.is_published
        })) as Post[];

        allPosts = allPosts.concat(pagePosts);
        lastPage = response.meta.last_page;
        currentPage++;
      } while (currentPage <= lastPage);

      return allPosts;
    }
);

if (data.value) {
  posts.value = data.value;
}

// Computed properties for pagination
const totalPages = computed(() => Math.ceil(posts.value.length / pageSize.value));

const paginatedPosts = computed(() => {
  const start = (currentPage.value - 1) * pageSize.value;
  const end = start + pageSize.value;
  return posts.value.slice(start, end);
});

const startIndex = computed(() => {
  return posts.value.length === 0 ? 0 : (currentPage.value - 1) * pageSize.value + 1;
});

const endIndex = computed(() => {
  return Math.min(currentPage.value * pageSize.value, posts.value.length);
});

const visiblePages = computed(() => {
  const pages = [];
  const maxVisible = 5;
  let start = Math.max(1, currentPage.value - Math.floor(maxVisible / 2));
  let end = Math.min(totalPages.value, start + maxVisible - 1);

  if (end - start + 1 < maxVisible) {
    start = Math.max(1, end - maxVisible + 1);
  }

  for (let i = start; i <= end; i++) {
    pages.push(i);
  }

  return pages;
});

// Methods
const goToPage = (page: number) => {
  if (page >= 1 && page <= totalPages.value) {
    currentPage.value = page;
  }
};

const formatDate = (dateString: string) => {
  if (!dateString) return 'Немає';

  return new Date(dateString).toLocaleString('uk-UA', {
    day: 'numeric',
    month: 'short',
    year: 'numeric',
    hour: '2-digit',
    minute: '2-digit',
    hour12: false
  });
};
</script>
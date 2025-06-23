<template>
  <div class="min-h-screen bg-gradient-to-br from-indigo-50 via-white to-purple-50">
    <!-- Header Section -->
    <div class="bg-gradient-to-r from-indigo-600 via-purple-600 to-pink-600 shadow-2xl">
      <div class="container max-w-8xl mx-auto p-8">
        <div class="text-center">
          <h1 class="text-4xl font-bold text-white mb-3 tracking-tight">Список постів</h1>

          <NuxtLink
              to="/admin/blog/posts/create"
              class="inline-flex items-center gap-3 px-8 py-4 bg-white text-indigo-600 font-bold rounded-2xl shadow-xl transition-all duration-300 ease-in-out hover:bg-gray-50 hover:-translate-y-1 hover:shadow-2xl focus:outline-none focus:ring-4 focus:ring-white/30 transform"
          >
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"></path>
            </svg>
            Додати новий пост
          </NuxtLink>
        </div>
      </div>
    </div>

    <!-- Main Content Container -->
    <div class="container max-w-8xl mx-auto p-8">
      <!-- Main Table Card -->
      <UCard class="bg-white rounded-3xl shadow-2xl border-0 overflow-hidden backdrop-blur-sm">
        <template #header>
          <div class="px-8 py-6 bg-gradient-to-r from-gray-50 to-gray-100 border-b border-gray-200">
            <div class="flex items-center justify-between">
              <div>
                <h2 class="text-2xl font-bold text-gray-900 flex items-center gap-3">
                  <div class="p-2 bg-indigo-100 rounded-xl">
                    <svg class="w-6 h-6 text-indigo-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"></path>
                    </svg>
                  </div>
                  Управління постами
                </h2>
                <p class="text-sm text-gray-600 mt-2">Перегляд та керування постами блогу в красивому інтерфейсі</p>
              </div>
              <div class="flex items-center gap-3">
                <div class="px-4 py-2 bg-indigo-100 text-indigo-800 rounded-xl text-sm font-medium">
                  {{ posts.length }} постів
                </div>
              </div>
            </div>
          </div>
        </template>

        <div class="min-h-[500px] flex items-center justify-center">
          <template v-if="pending">
            <div class="text-center py-16">
              <div class="inline-flex items-center gap-3 text-xl text-indigo-600 font-semibold">
                <div class="w-8 h-8 border-4 border-indigo-200 border-t-indigo-600 rounded-full animate-spin"></div>
                Завантаження постів...
              </div>
            </div>
          </template>
          <template v-else-if="error">
            <div class="text-center py-16">
              <div class="inline-flex items-center gap-3 text-xl text-red-600 font-semibold bg-red-50 px-6 py-4 rounded-2xl">
                <svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                </svg>
                Помилка завантаження постів: {{ error.message }}
              </div>
            </div>
          </template>
          <template v-else-if="posts.length === 0">
            <div class="text-center py-16">
              <div class="mb-4">
                <svg class="w-24 h-24 text-gray-300 mx-auto" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path>
                </svg>
              </div>
              <div class="text-xl text-gray-500 font-medium">Постів поки що немає</div>
              <p class="text-gray-400 mt-2">Створіть свій перший пост, щоб побачити його тут</p>
            </div>
          </template>
          <template v-else>
            <div class="w-full">
              <UTable
                  ref="table"
                  v-model:pagination="pagination"
                  :data="posts"
                  :columns="columns"
                  :pagination-options="{ getPaginationRowModel: getPaginationRowModel() }"
                  class="w-full"
                  :thead-class="'bg-gradient-to-r from-indigo-50 to-purple-50'"
                  :tbody-tr-class="(row) => getRowClass(row)"
                  :tbody-td-class="'px-8 py-6 text-sm border-b border-gray-50'"
                  :th-class="'px-8 py-4 text-left text-xs font-bold text-gray-700 uppercase tracking-wider border-b-2 border-indigo-100'"
              />
            </div>
          </template>
        </div>

        <div v-if="!pending && !error && posts.length > 0" class="px-8 py-6 bg-gradient-to-r from-gray-50 to-gray-100 border-t border-gray-200 flex items-center justify-between min-h-[80px] flex-wrap">
          <div class="text-sm text-gray-600 font-medium bg-white px-4 py-2 rounded-xl shadow-sm">
            Показано {{ ((pagination.pageIndex) * pagination.pageSize) + 1 }} -
            {{ Math.min((pagination.pageIndex + 1) * pagination.pageSize, posts.length) }}
            з {{ posts.length }} результатів
          </div>
          <UPagination
              :default-page="(table?.tableApi?.getState().pagination.pageIndex || 0) + 1"
              :items-per-page="table?.tableApi?.getState().pagination.pageSize"
              :total="table?.tableApi?.getFilteredRowModel().rows.length"
              @update:page="p => table?.tableApi?.setPageIndex(p - 1)"
              :max="5"
              :active-button="{ variant: 'solid', color: 'indigo' }"
              :inactive-button="{ color: 'gray', variant: 'ghost' }"
              show-first
              show-last
              class="ml-auto"
          />
        </div>
      </UCard>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, h } from 'vue';
import { getPaginationRowModel } from '@tanstack/vue-table';
import type { TableColumn } from '@nuxt/ui';

const table = ref();

type Post = {
  id: string;
  name: string;
  category: string;
  title: string;
  date: string;
  is_published: boolean;
  slug: string;
};

const posts = ref<Post[]>([]);
const config = useRuntimeConfig();

const getRowClass = (row: any) => {
  const isPublished = row.original.is_published;
  return isPublished
      ? 'bg-white hover:bg-gradient-to-r hover:from-blue-50 hover:to-indigo-50 hover:shadow-lg transform transition-all duration-300 ease-in-out cursor-pointer border-l-4 border-l-transparent hover:border-l-blue-500'
      : 'bg-gradient-to-r from-amber-50 to-yellow-50 text-gray-700 hover:from-amber-100 hover:to-yellow-100 hover:shadow-lg transform transition-all duration-300 ease-in-out cursor-pointer border-l-4 border-l-amber-400';
};

const handleRowClick = async (post: Post) => {
  try {
    console.log('Navigating to:', `/blog/${post.slug}?source=nuxt-ui`);
    await navigateTo(`/blog/${post.slug}?source=nuxt-ui`);
  } catch (error) {
    console.error('Navigation error:', error);
    window.location.href = `/blog/${post.slug}?source=nuxt-ui`;
  }
};

const handleDeleteClick = async (post: Post) => {
  const confirmed = confirm(`Ви впевнені, що хочете видалити пост "${post.title}"? Цю дію неможливо скасувати.`);

  if (!confirmed) return;

  try {
    await $fetch(`${config.public.apiBase}/blog/posts/${post.slug}`, {
      method: 'DELETE'
    });

    posts.value = posts.value.filter(p => p.slug !== post.slug);

    alert('Пост успішно видалено!');

  } catch (error) {
    console.error('Delete error:', error);
    alert('Помилка при видаленні поста');
  }
};

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
          is_published: post.is_published,
          slug: post.slug
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

const columns: TableColumn<Post>[] = [
  {
    accessorKey: 'id',
    header: '🆔 ID',
    cell: ({ row }) => h('div', {
      onClick: () => handleRowClick(row.original),
      class: 'cursor-pointer w-full h-full flex items-center font-mono text-indigo-600 font-semibold'
    }, `#${row.getValue('id')}`)
  },
  {
    accessorKey: 'name',
    header: '👤 Автор',
    cell: ({ row }) => h('div', {
      onClick: () => handleRowClick(row.original),
      class: 'cursor-pointer w-full h-full flex items-center font-medium'
    }, row.getValue('name') || 'Невідомий автор')
  },
  {
    accessorKey: 'category',
    header: '📂 Категорія',
    cell: ({ row }) => {
      const category = row.getValue('category');
      return h('div', {
        onClick: () => handleRowClick(row.original),
        class: 'cursor-pointer w-full h-full flex items-center'
      }, [
        h('span', {
          class: 'px-3 py-1 bg-purple-100 text-purple-800 rounded-full text-xs font-semibold border border-purple-200'
        }, category || 'Без категорії')
      ]);
    }
  },
  {
    accessorKey: 'title',
    header: '📄 Заголовок',
    cell: ({ row }) => {
      const postTitle = row.getValue('title');
      return h('div', {
        class: 'cursor-pointer text-gray-800 font-semibold hover:text-indigo-600 transition-colors duration-300 w-full h-full flex items-center break-words',
        onClick: () => handleRowClick(row.original)
      }, postTitle);
    }
  },
  {
    accessorKey: 'date',
    header: '📅 Дата публікації',
    cell: ({ row }) => {
      const dateValue = row.getValue('date');
      const formattedDate = dateValue ? new Date(dateValue).toLocaleString('uk-UA', {
        day: 'numeric',
        month: 'short',
        year: 'numeric',
        hour: '2-digit',
        minute: '2-digit',
        hour12: false
      }) : 'Немає';
      return h('div', {
        onClick: () => handleRowClick(row.original),
        class: 'cursor-pointer w-full h-full flex items-center font-mono text-sm text-gray-600'
      }, formattedDate);
    }
  },
  {
    accessorKey: 'is_published',
    header: '🚀 Статус',
    cell: ({ row }) => {
      const isPublished = row.getValue('is_published');
      return h('div', {
        onClick: () => handleRowClick(row.original),
        class: 'cursor-pointer w-full h-full flex items-center',
        innerHTML: `<span class="inline-flex items-center gap-2 px-4 py-2 rounded-full text-xs font-bold whitespace-nowrap text-center min-w-[120px] shadow-sm ${isPublished ? 'bg-gradient-to-r from-green-400 to-emerald-500 text-white border border-green-300' : 'bg-gradient-to-r from-red-400 to-pink-500 text-white border border-red-300'}">${isPublished ? '✅ Опубліковано' : '⏳ Чернетка'}</span>`
      });
    }
  },
  {
    accessorKey: 'actions',
    header: '⚡ Дії',
    cell: ({ row }) => {
      const postSlug = row.original.slug;

      return h('div', {
        class: 'flex justify-center items-center gap-3'
      }, [
        h('button', {
          class: 'px-4 py-2 bg-gradient-to-r from-blue-500 to-indigo-600 text-white border border-blue-400 rounded-xl text-sm font-semibold cursor-pointer transition-all duration-300 ease-in-out hover:from-blue-600 hover:to-indigo-700 hover:-translate-y-0.5 hover:shadow-lg focus:outline-none focus:ring-4 focus:ring-blue-300 shadow-md',
          onClick: async (e: Event) => {
            e.stopPropagation();
            try {
              await navigateTo(`/admin/blog/posts/${postSlug}/edit`);
            } catch (error) {
              console.error('Edit navigation error:', error);
              window.location.href = `/admin/blog/posts/${postSlug}/edit`;
            }
          }
        }, '✏️ Редагувати'),
        h('button', {
          class: 'px-4 py-2 bg-gradient-to-r from-red-500 to-pink-600 text-white border border-red-400 rounded-xl text-sm font-semibold cursor-pointer transition-all duration-300 ease-in-out hover:from-red-600 hover:to-pink-700 hover:-translate-y-0.5 hover:shadow-lg focus:outline-none focus:ring-4 focus:ring-red-300 shadow-md',
          onClick: (e: Event) => {
            e.stopPropagation();
            handleDeleteClick(row.original);
          }
        }, '🗑️ Видалити')
      ]);
    }
  }
];

const pagination = ref({
  pageIndex: 0,
  pageSize: 10
});
</script>

<style scoped>
.table-fixed :deep(th),
.table-fixed :deep(td) {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: normal;
}

.table-fixed :deep(td > .break-words) {
  white-space: normal;
  word-break: break-word;
}

/* Custom scrollbar */
:deep(.overflow-x-auto)::-webkit-scrollbar {
  height: 8px;
}

:deep(.overflow-x-auto)::-webkit-scrollbar-track {
  background: #f1f5f9;
  border-radius: 4px;
}

:deep(.overflow-x-auto)::-webkit-scrollbar-thumb {
  background: linear-gradient(90deg, #6366f1, #8b5cf6);
  border-radius: 4px;
}

:deep(.overflow-x-auto)::-webkit-scrollbar-thumb:hover {
  background: linear-gradient(90deg, #4f46e5, #7c3aed);
}
</style>
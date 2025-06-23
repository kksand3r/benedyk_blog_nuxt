<template>
  <div class="min-h-screen bg-gradient-to-br from-purple-50 via-white to-indigo-50">
    <div class="bg-gradient-to-r from-purple-600 via-indigo-600 to-blue-600 shadow-2xl">
      <div class="container max-w-8xl mx-auto p-8">
        <div class="text-center">
          <h1 class="text-4xl font-bold text-white mb-3 tracking-tight">Категорії блогу</h1>
          <NuxtLink
              to="/admin/blog/categories/create"
              class="inline-flex items-center gap-3 px-8 py-4 bg-white text-purple-600 font-bold rounded-2xl shadow-xl transition-all duration-300 ease-in-out hover:bg-gray-50 hover:-translate-y-1 hover:shadow-2xl focus:outline-none focus:ring-4 focus:ring-white/30 transform"
          >
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"></path>
            </svg>
            Додати категорію
          </NuxtLink>
        </div>
      </div>
    </div>

    <div class="container max-w-8xl mx-auto p-8">
      <UCard class="w-full bg-white rounded-3xl shadow-2xl border-0 overflow-hidden backdrop-blur-sm">
        <template #header>
          <div class="px-8 py-6 bg-gradient-to-r from-gray-50 to-gray-100 border-b border-gray-200">
            <div class="flex items-center justify-between">
              <div>
                <h2 class="text-2xl font-bold text-gray-900 flex items-center gap-3">
                  <div class="p-2 bg-purple-100 rounded-xl">
                    <svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"></path>
                    </svg>
                  </div>
                  Управління категоріями
                </h2>
                <p class="text-sm text-gray-600 mt-2">Перегляд та керування категоріями блогу в елегантному інтерфейсі</p>
              </div>
              <div class="flex items-center gap-3">
                <div class="px-4 py-2 bg-purple-100 text-purple-800 rounded-xl text-sm font-medium">
                  {{ totalCategories }} категорій
                </div>
              </div>
            </div>
          </div>
        </template>

        <div class="min-h-[400px] flex items-center justify-center">
          <template v-if="loading">
            <div class="text-center py-16">
              <div class="inline-flex items-center gap-3 text-xl text-purple-600 font-semibold">
                <div class="w-8 h-8 border-4 border-purple-200 border-t-purple-600 rounded-full animate-spin"></div>
                Завантаження категорій...
              </div>
            </div>
          </template>
          <template v-else-if="categories.length === 0">
            <div class="text-center py-16">
              <div class="mb-4">
                <svg class="w-24 h-24 text-gray-300 mx-auto" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1" d="M9 5H7a2 2 0 00-2 2v10a2 2 0 002 2h8a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2"></path>
                </svg>
              </div>
              <div class="text-xl text-gray-500 font-medium">Категорій поки що немає</div>
              <p class="text-gray-400 mt-2">Створіть першу категорію для організації контенту</p>
            </div>
          </template>
          <template v-else>
            <div>
              <UTable
                  :data="categories"
                  :columns="columns"
                  sort-by="id"
                  sort-asc="false"
                  class="max-w-full mx-auto"
                  :thead-class="'bg-gradient-to-r from-purple-50 to-indigo-50'"
                  :tbody-tr-class="'border-b border-gray-50 hover:bg-gradient-to-r hover:from-purple-50/30 hover:to-indigo-50/30 hover:shadow-sm transform transition-all duration-300 ease-in-out cursor-pointer'"
                  :tbody-td-class="'px-8 py-6 text-sm'"
                  :th-class="'px-8 py-4 text-left text-xs font-bold text-gray-700 uppercase tracking-wider border-b-2 border-purple-100'"
              />
            </div>
          </template>
        </div>

        <div v-if="!loading && categories.length > 0" class="px-8 py-6 bg-gradient-to-r from-gray-50 to-gray-100 border-t border-gray-200 flex items-center justify-between min-h-[80px] flex-wrap">
          <div class="flex items-center gap-4">
            <div class="text-sm text-gray-600 font-medium bg-white px-4 py-2 rounded-xl shadow-sm">
              Показано {{ ((page - 1) * perPage) + 1 }} - {{ Math.min(page * perPage, totalCategories) }} з {{ totalCategories }} категорій
            </div>
            <div class="flex items-center gap-2">
              <span class="text-sm text-gray-500 whitespace-nowrap">На сторінці:</span>
              <USelect
                  v-model.number="perPage"
                  :options="[5, 10, 20, 50]"
                  class="w-20"
                  :ui="{
                  base: 'rounded-xl border-0 shadow-sm bg-white',
                  select: 'text-center font-medium'
                }"
              />
            </div>
          </div>
          <UPagination
              v-model="page"
              :page-count="perPage"
              :total="totalCategories"
              :max="5"
              :active-button="{ variant: 'solid', color: 'purple' }"
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
import { ref, watch, computed, onMounted, h } from 'vue';
import { useRuntimeConfig, navigateTo } from '#app';
import { useToast } from '#imports';
import type { TableColumn } from '@nuxt/ui';

interface Category {
  id: number;
  title: string;
  slug: string;
  description: string | null;
  parent_id: number | null;
  created_at: string;
  updated_at: string;
}

const columns: TableColumn<Category>[] = [
  {
    accessorKey: 'id',
    header: '🆔 ID',
    cell: ({ row }) => h('div', {
      class: 'font-mono text-purple-600 font-semibold'
    }, `#${row.getValue('id')}`)
  },
  {
    accessorKey: 'title',
    header: '📝 Назва',
    sortable: true,
    cell: ({ row }) => {
      const id = row.original.id;
      const title = row.getValue('title');
      return h('a', {
        href: `/admin/blog/categories/edit/${id}`,
        class: 'text-gray-800 font-semibold hover:text-purple-600 transition-colors duration-300 cursor-pointer',
        onClick: (e: Event) => {
          e.preventDefault();
          navigateTo(`/admin/blog/categories/edit/${id}`);
        }
      }, title);
    }
  },
  {
    accessorKey: 'slug',
    header: '🔗 Slug',
    cell: ({ row }) => h('code', {
      class: 'px-2 py-1 bg-gray-100 text-gray-700 rounded text-xs font-mono'
    }, row.getValue('slug'))
  },
  {
    accessorKey: 'description',
    header: '📄 Опис',
    cell: ({ row }) => {
      const description = row.getValue('description');
      const text = description ? String(description).substring(0, 50) + (String(description).length > 50 ? '...' : '') : '-';
      return h('div', {
        class: 'text-gray-600 text-sm'
      }, text);
    }
  },
  {
    accessorKey: 'parent_id',
    header: '🏗️ Батьківська',
    cell: ({ row }) => {
      const parentId = row.getValue('parent_id');
      return h('div', {
        class: 'text-center'
      }, parentId ? h('span', {
        class: 'px-2 py-1 bg-indigo-100 text-indigo-800 rounded-full text-xs font-medium'
      }, `#${parentId}`) : h('span', {
        class: 'text-gray-400 text-sm'
      }, '—'));
    }
  },
  {
    accessorKey: 'actions',
    header: '⚡ Дії',
    cell: ({ row }) => {
      return h('div', {
        class: 'flex justify-center items-center gap-2'
      }, [
        h('button', {
          class: 'px-3 py-1.5 bg-gradient-to-r from-blue-500 to-purple-600 text-white rounded-lg text-xs font-semibold cursor-pointer transition-all duration-300 ease-in-out hover:from-blue-600 hover:to-purple-700 hover:-translate-y-0.5 hover:shadow-lg focus:outline-none focus:ring-2 focus:ring-blue-300 shadow-sm',
          onClick: () => navigateTo(`/admin/blog/categories/edit/${row.original.id}`)
        }, '✏️ Редагувати'),
        h('button', {
          class: 'px-3 py-1.5 bg-gradient-to-r from-red-500 to-pink-600 text-white rounded-lg text-xs font-semibold cursor-pointer transition-all duration-300 ease-in-out hover:from-red-600 hover:to-pink-700 hover:-translate-y-0.5 hover:shadow-lg focus:outline-none focus:ring-2 focus:ring-red-300 shadow-sm ml-1',
          onClick: () => deleteCategory(row.original.id)
        }, '🗑️ Видалити')
      ]);
    }
  }
];

const categories = ref<Category[]>([]);
const loading = ref(true);
const page = ref(1);
const perPage = ref(20);
const totalCategories = ref(0);
const q = ref('');

const config = useRuntimeConfig();
const toast = useToast();

const fetchCategories = async () => {
  loading.value = true;
  try {
    const apiUrl = `${config.public.apiBase}/blog/categories?page=${page.value}&per_page=${perPage.value}&search=${q.value}`;

    const { data, meta } = await $fetch<{ data: Category[], meta: any }>(apiUrl);

    categories.value = data;
    totalCategories.value = meta.total;

  } catch (error) {
    console.error("Error fetching categories:", error);
    toast.add({
      title: 'Помилка завантаження категорій',
      description: 'Не вдалося завантажити список категорій.',
      icon: 'i-heroicons-x-circle',
      color: 'red'
    });
    categories.value = [];
    totalCategories.value = 0;
  } finally {
    loading.value = false;
  }
};

const deleteCategory = async (id: number) => {
  if (!confirm('Ви впевнені, що хочете видалити цю категорію?')) {
    return;
  }

  try {
    await $fetch(`${config.public.apiBase}/blog/categories/${id}`, {
      method: 'DELETE'
    });
    toast.add({
      title: 'Категорію видалено',
      description: 'Категорія була успішно видалена.',
      icon: 'i-heroicons-check-circle',
      color: 'green'
    });
    fetchCategories();
  } catch (error) {
    console.error('Помилка видалення категорії:', error);
    toast.add({
      title: 'Помилка видалення',
      description: 'Не вдалося видалити категорію.',
      icon: 'i-heroicons-x-circle',
      color: 'red'
    });
  }
};

const items = (row: Category) => [
  [{
    label: 'Редагувати',
    icon: 'i-heroicons-pencil-square-20-solid',
    click: () => navigateTo(`/admin/blog/categories/edit/${row.id}`)
  }, {
    label: 'Видалити',
    icon: 'i-heroicons-trash-20-solid',
    click: () => deleteCategory(row.id)
  }]
];

watch([page, perPage], () => {
  fetchCategories();
});

watch(q, (newQ) => {
  const debounce = setTimeout(() => {
    page.value = 1;
    fetchCategories();
  }, 300);
  return () => clearTimeout(debounce);
});

onMounted(() => {
  fetchCategories();
});
</script>

<style scoped>
:deep(.overflow-x-auto)::-webkit-scrollbar {
  height: 8px;
}

:deep(.overflow-x-auto)::-webkit-scrollbar-track {
  background: #f1f5f9;
  border-radius: 4px;
}

:deep(.overflow-x-auto)::-webkit-scrollbar-thumb {
  background: linear-gradient(90deg, #8b5cf6, #6366f1);
  border-radius: 4px;
}

:deep(.overflow-x-auto)::-webkit-scrollbar-thumb:hover {
  background: linear-gradient(90deg, #7c3aed, #4f46e5);
}

:deep(.hover\:bg-gradient-to-r) {
  transition: all 0.3s ease-in-out;
}
</style>
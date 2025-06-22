<template>
  <div class="container">
    <div class="table-card">

      <div class="card-header">
        <h2>NuxtUI таблиця</h2>
      </div>

      <div class="table-wrapper">
        <UTable
            ref="table"
            v-model:pagination="pagination"
            :data="posts"
            :columns="columns"
            :pagination-options="{ getPaginationRowModel: getPaginationRowModel() }"
            class="main-table"
            :thead-class="'table-thead'"
            :tbody-tr-class="'table-row'"
            :tbody-td-class="'table-cell'"
            :th-class="'table-th'"
        >
          <template #status-data="{ row }">
            <span :class="{'status-badge': true, 'status-published': row.status === 'Опубліковано', 'status-draft': row.status === 'Чернетка'}">
              {{ row.status }}
            </span>
          </template>
        </UTable>
      </div>

      <div class="footer-pagination">
        <div class="pagination-info-and-control">
          <div class="pagination-label-centered"> Показано {{ ((pagination.pageIndex) * pagination.pageSize) + 1 }} по
            {{ Math.min((pagination.pageIndex + 1) * pagination.pageSize, posts.length) }}
            з {{ posts.length }} результатів
          </div>
          <UPagination
              :default-page="(table?.tableApi?.getState().pagination.pageIndex || 0) + 1"
              :items-per-page="table?.tableApi?.getState().pagination.pageSize"
              :total="table?.tableApi?.getFilteredRowModel().rows.length"
              @update:page="p => table?.tableApi?.setPageIndex(p - 1)"
              class="page-select"
          />
        </div>
      </div>

    </div>
  </div>
</template>


<script setup lang="ts">
import { ref } from 'vue'
import { getPaginationRowModel } from '@tanstack/vue-table'
import type { TableColumn } from '@nuxt/ui'

const table = ref()

type Post = {
  id: string
  name: string
  category: string
  title: string
  date: string
  status: 'Опубліковано' | 'Чернетка'
}

const posts = ref<Post[]>([])

const getPosts = async () => {
  try {
    let allPosts: Post[] = []
    let currentPage = 1
    let lastPage = 1

    do {
      const response = await $fetch(`http://localhost/api/blog/posts?page=${currentPage}`)
      const pagePosts = response.data.map((post: any) => ({
        id: String(post.id),
        name: post.user?.name || 'Невідомий автор',
        category: post.category?.title || 'Без категорії',
        title: post.title,
        date: post.published_at,
        status: Math.random() > 0.5 ? 'Опубліковано' : 'Чернетка'
      })) as Post[]

      allPosts = allPosts.concat(pagePosts)
      lastPage = response.meta.last_page
      currentPage++
    } while (currentPage <= lastPage)

    posts.value = allPosts
  } catch (error) {
    console.error('Помилка отримання дописів:', error)
  }
}

getPosts()

const columns: TableColumn<Post>[] = [
  {
    accessorKey: 'id',
    header: '#',
    cell: ({ row }) => `#${row.getValue('id')}`
  },
  {
    accessorKey: 'date',
    header: 'Дата',
    cell: ({ row }) => {
      return new Date(row.getValue('date')).toLocaleString('uk-UA', {
        day: 'numeric',
        month: 'short',
        year: 'numeric', // Додано відображення року
        hour: '2-digit',
        minute: '2-digit',
        hour12: false
      })
    }
  },
  {
    accessorKey: 'name',
    header: 'Автор'
  },
  {
    accessorKey: 'category',
    header: 'Категорія'
  },
  {
    accessorKey: 'title',
    header: 'Заголовок'
  },
  {
    accessorKey: 'status',
    header: 'Статус'
  }
]

const pagination = ref({
  pageIndex: 0,
  pageSize: 10
})
</script>

<style scoped>
.container {
  max-width: 1300px;
  margin: 0 auto;
  padding: 3rem 2rem;
  background-color: #f5f7fa;
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
}

.table-card {
  max-width: 100%;
  margin: 0 auto;
  border-radius: 1rem;
  background-color: #ffffff;
  box-shadow: 0 12px 35px rgba(0, 0, 0, 0.12), 0 4px 12px rgba(0, 0, 0, 0.06);
  overflow: hidden;
}

.card-header {
  font-size: 2rem;
  font-weight: 700;
  color: #1a202c;
  padding: 1.75rem 2.5rem;
  position: relative;
  margin-bottom: 0;
  background-color: #fcfdfe;
}

.header-subtitle {
  font-size: 1.05rem;
  color: #6b7280;
  margin-top: 0.6rem;
}

.card-header::after {
  content: '';
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  height: 1px;
  background: linear-gradient(to right, rgba(237, 242, 247, 0) 5%, #e2e8f0 50%, rgba(237, 242, 247, 0) 95%);
}

.table-wrapper {
  overflow-x: auto;
}

.main-table {
  width: 100%;
  border-collapse: collapse;
}

.main-table :deep(thead) {
  background-color: #f8fafd;
}

.main-table :deep(th) {
  padding: 1.25rem 2.5rem;
  text-align: left;
  font-size: 0.85rem;
  font-weight: 600;
  color: #4a5568;
  text-transform: uppercase;
  letter-spacing: 0.06em;
}

.main-table :deep(tbody tr) {
  border-bottom: 1px solid #f0f4f8;
  transition: background-color 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
}

.main-table :deep(tbody tr:hover) {
  background-color: #fbfbfc;
  box-shadow: inset 4px 0 0 0 #3b82f6;
}

.main-table :deep(td) {
  padding: 1.5rem 2.5rem;
  font-size: 0.95rem;
  color: #374151;
}

.main-table :deep(td:nth-child(2)) {
  font-weight: 500;
  color: #4a5568;
}

.status-badge {
  padding: 0.25rem 0.75rem;
  border-radius: 9999px;
  font-size: 0.75rem;
  font-weight: 600;
  display: inline-block;
  white-space: nowrap;
}

.status-published {
  background-color: #d1fae5;
  color: #065f46;
}

.status-draft {
  background-color: #fff1e0;
  color: #9a6700;
}

.footer-pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1.5rem 2.5rem;
  background-color: #fcfdfe;
  min-height: 60px;
  flex-wrap: nowrap;
  box-shadow: 0 -5px 20px rgba(0, 0, 0, 0.04), 0 -2px 8px rgba(0, 0, 0, 0.02);
  border-bottom-left-radius: 0.9rem;
  border-bottom-right-radius: 0.9rem;
}

.pagination-info-and-control {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  width: 100%;
}

.pagination-label-centered {
  font-size: 0.95rem;
  color: #6b7280;
  white-space: nowrap;
  text-align: center;
  margin-bottom: 0.5rem;
}

.footer-pagination :deep(.upagination) {
  display: flex;
  gap: 0.25rem;
  align-items: center;
  flex-wrap: nowrap;
}

.footer-pagination :deep(.upagination button) {
  background-color: white;
  border: 1px solid #d8dce2;
  padding: 0.6rem 0.9rem;
  border-radius: 0.35rem;
  font-size: 0.95rem;
  color: #374151;
  cursor: pointer;
  transition: all 0.2s ease-in-out;
  min-width: 2.5rem;
  height: 2.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 500;
  white-space: nowrap;
  flex-shrink: 0;
  box-shadow: none;
}

.footer-pagination :deep(.upagination button:first-child),
.footer-pagination :deep(.upagination button:last-child) {
  font-weight: 600;
  color: #4a5568;
}

.footer-pagination :deep(.upagination button:hover:not(:disabled)) {
  background-color: #f0f4f8;
  border-color: #c9d6e4;
  transform: none;
  box-shadow: none;
}

.footer-pagination :deep(.upagination button[aria-current='true']) {
  background-color: #3b82f6;
  color: white;
  border: none;
  font-weight: 600;
  box-shadow: none;
  transform: none;
}

.footer-pagination :deep(.upagination button:disabled) {
  opacity: 0.6;
  cursor: not-allowed;
  background-color: #fcfdfe;
  color: #a0a8b2;
  box-shadow: none;
  transform: none;
  border: 1px solid #e0e0e0;
}
</style>
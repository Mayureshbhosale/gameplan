<template>
  <div>
    <header
      class="sticky top-0 z-10 flex items-center justify-between border-b bg-white px-5 py-2.5"
    >
      <PageBreadcrumbs
        class="h-7"
        :items="[{ label: 'My Tasks', route: { name: 'MyTasks' } }]"
      />
      <Button variant="solid" @click="showNewTaskDialog">
        <template #prefix>
          <LucidePlus class="h-4 w-4" />
        </template>
        Add new
      </Button>
    </header>

    <div class="mx-auto w-full max-w-4xl px-5">
      <div class="py-6">
        <TaskList :listOptions="listOptions" :groupByStatus="true" />
        <NewTaskDialog ref="newTaskDialog" />
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref, computed } from 'vue'
import { getCachedListResource, usePageMeta } from 'frappe-ui'
import { getUser } from '@/data/users'

let newTaskDialog = ref(null)

let listOptions = computed(() => ({
  filters: { assigned_or_owner: getUser('sessionUser').name },
}))

function showNewTaskDialog() {
  newTaskDialog.value.show({
    defaults: {
      assigned_to: getUser('sessionUser').name,
    },
    onSuccess: () => {
      let tasks = getCachedListResource(['Tasks', listOptions.value])
      if (tasks) {
        tasks.reload()
      }
    },
  })
}

usePageMeta(() => {
  return {
    title: 'My Tasks',
  }
})
</script>

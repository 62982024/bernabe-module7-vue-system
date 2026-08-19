<script setup>
defineProps({
  items: Array
})

const emit = defineEmits(['edit-item', 'delete-item'])
</script>

<template>
  <div class="bg-white p-6 rounded-xl shadow-md border border-gray-100">
    <div class="flex justify-between items-center mb-4">
      <h2 class="text-xl font-bold text-gray-800">📋 Menu List</h2>
      <span class="bg-gray-100 text-gray-600 text-xs font-semibold px-2.5 py-1 rounded-full">
        Total Items: {{ items.length }}
      </span>
    </div>

    <div v-if="items.length === 0" class="text-center py-8 text-gray-500">
      No menu items found. Add some items or clear your search filter.
    </div>

    <div v-else class="overflow-x-auto">
      <table class="w-full text-left border-collapse">
        <thead>
          <tr class="border-b border-gray-200 bg-gray-50 text-xs font-semibold text-gray-600 uppercase">
            <th class="p-3">Item Name</th>
            <th class="p-3">Category</th>
            <th class="p-3">Price</th>
            <th class="p-3">Status</th>
            <th class="p-3 text-center">Actions</th>
          </tr>
        </thead>
        <tbody class="divide-y divide-gray-100 text-sm">
          <tr v-for="item in items" :key="item.id" class="hover:bg-gray-50">
            <td class="p-3 font-medium text-gray-900">{{ item.name }}</td>
            <td class="p-3 text-gray-600">{{ item.category }}</td>
            <td class="p-3 font-semibold text-gray-800">₱{{ Number(item.price).toFixed(2) }}</td>
            <td class="p-3">
              <span 
                :class="item.status === 'Available' ? 'bg-green-100 text-green-700' : 'bg-red-100 text-red-700'"
                class="px-2.5 py-0.5 rounded-full text-xs font-semibold"
              >
                {{ item.status }}
              </span>
            </td>
            <td class="p-3 text-center space-x-2">
              <button 
                @click="emit('edit-item', item)" 
                class="bg-blue-500 hover:bg-blue-600 text-white px-3 py-1 rounded text-xs transition-colors cursor-pointer"
              >
                Edit
              </button>
              <button 
                @click="emit('delete-item', item.id)" 
                class="bg-red-500 hover:bg-red-600 text-white px-3 py-1 rounded text-xs transition-colors cursor-pointer"
              >
                Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
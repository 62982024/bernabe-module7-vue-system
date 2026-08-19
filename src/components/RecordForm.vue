<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  itemToEdit: Object
})

const emit = defineEmits(['save-item', 'cancel-edit'])

const formData = ref({
  name: '',
  category: 'Main',
  price: '',
  status: 'Available'
})

const errorMessage = ref('')

watch(() => props.itemToEdit, (newVal) => {
  if (newVal) {
    formData.value = { ...newVal }
  } else {
    resetForm()
  }
}, { immediate: true })

function resetForm() {
  formData.value = { name: '', category: 'Main', price: '', status: 'Available' }
  errorMessage.value = ''
}

function handleSubmit() {
  if (!formData.value.name.trim() || !formData.value.price) {
    errorMessage.value = 'Please complete all required fields.'
    return
  }
  if (formData.value.price <= 0) {
    errorMessage.value = 'Price must be greater than 0.'
    return
  }

  emit('save-item', { ...formData.value, price: Number(formData.value.price) })
  resetForm()
}

function handleCancel() {
  resetForm()
  emit('cancel-edit')
}
</script>

<template>
  <div class="bg-white p-6 rounded-xl shadow-md border border-gray-100 mb-8">
    <h2 class="text-xl font-bold text-gray-800 mb-4">
      {{ itemToEdit ? '✏️ Edit Menu Item' : '➕ Add New Menu Item' }}
    </h2>

    <div v-if="errorMessage" class="bg-red-50 text-red-600 p-3 rounded-md mb-4 text-sm font-medium border border-red-200">
      ⚠️ {{ errorMessage }}
    </div>

    <form @submit.prevent="handleSubmit" class="space-y-4">
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Item Name *</label>
          <input 
            v-model="formData.name" 
            type="text" 
            placeholder="e.g. Cheeseburger" 
            class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-red-500 focus:outline-none"
          />
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Category *</label>
          <select 
            v-model="formData.category" 
            class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-red-500 focus:outline-none"
          >
            <option value="Main">Main Course</option>
            <option value="Sides">Sides</option>
            <option value="Drinks">Drinks</option>
            <option value="Desserts">Desserts</option>
          </select>
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Price (₱) *</label>
          <input 
            v-model="formData.price" 
            type="number" 
            step="0.01" 
            placeholder="0.00" 
            class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-red-500 focus:outline-none"
          />
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Status *</label>
          <select 
            v-model="formData.status" 
            class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-red-500 focus:outline-none"
          >
            <option value="Available">Available</option>
            <option value="Out of Stock">Out of Stock</option>
          </select>
        </div>
      </div>

      <div class="flex gap-2 pt-2">
        <button 
          type="submit" 
          class="bg-red-600 hover:bg-red-700 text-white font-medium px-5 py-2 rounded-lg transition-colors cursor-pointer"
        >
          {{ itemToEdit ? 'Update Item' : 'Add Item' }}
        </button>
        <button 
          v-if="itemToEdit" 
          type="button" 
          @click="handleCancel" 
          class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-medium px-5 py-2 rounded-lg transition-colors cursor-pointer"
        >
          Cancel
        </button>
      </div>
    </form>
  </div>
</template>
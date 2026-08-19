<script setup>
import { ref, computed, onMounted } from 'vue'
import AppHeader from './components/AppHeader.vue'
import RecordForm from './components/RecordForm.vue'
import RecordList from './components/RecordList.vue'
import AppFooter from './components/AppFooter.vue'

const items = ref([])
const searchTerm = ref('')
const selectedItemToEdit = ref(null)
const alertMessage = ref('')

onMounted(() => {
  const saved = localStorage.getItem('module7-records')
  if (saved) {
    items.value = JSON.parse(saved)
  } else {
    items.value = [
      { id: 1, name: 'Classic Burger', category: 'Main', price: 120, status: 'Available' },
      { id: 2, name: 'Iced Milk Tea', category: 'Drinks', price: 80, status: 'Available' }
    ]
    saveToStorage()
  }
})

function saveToStorage() {
  localStorage.setItem('module7-records', JSON.stringify(items.value))
}

function handleSaveItem(itemData) {
  if (selectedItemToEdit.value) {
    const index = items.value.findIndex(i => i.id === itemData.id)
    if (index !== -1) {
      items.value[index] = { ...itemData }
      showAlert('Item updated successfully!')
    }
    selectedItemToEdit.value = null
  } else {
    const newItem = {
      ...itemData,
      id: Date.now()
    }
    items.value.push(newItem)
    showAlert('Item added successfully!')
  }
  saveToStorage()
}

function handleEditItem(item) {
  selectedItemToEdit.value = item
}

function handleDeleteItem(id) {
  const confirmDelete = window.confirm('Are you sure you want to delete this menu item?')
  if (!confirmDelete) return

  items.value = items.value.filter(item => item.id !== id)
  saveToStorage()
  showAlert('Item deleted successfully!')
}

function showAlert(msg) {
  alertMessage.value = msg
  setTimeout(() => alertMessage.value = '', 3000)
}

const filteredItems = computed(() => {
  const query = searchTerm.value.toLowerCase().trim()
  return items.value.filter(item => 
    item.name.toLowerCase().includes(query) || 
    item.category.toLowerCase().includes(query)
  )
})
</script>

<template>
  <div class="min-h-screen bg-gray-50 flex flex-col justify-between">
    <div>
      <AppHeader />

      <main class="max-w-6xl mx-auto px-4">
        <div v-if="alertMessage" class="bg-green-600 text-white px-4 py-3 rounded-lg shadow-lg mb-6 text-center font-semibold">
          ✅ {{ alertMessage }}
        </div>

        <div class="bg-white p-4 rounded-xl shadow-md border border-gray-100 mb-6">
          <input 
            v-model="searchTerm" 
            type="text" 
            placeholder="🔍 Search menu items by name or category..." 
            class="w-full border border-gray-300 rounded-lg px-4 py-2 focus:ring-2 focus:ring-red-500 focus:outline-none"
          />
        </div>

        <RecordForm 
          :itemToEdit="selectedItemToEdit" 
          @save-item="handleSaveItem" 
          @cancel-edit="selectedItemToEdit = null" 
        />

        <RecordList 
          :items="filteredItems" 
          @edit-item="handleEditItem" 
          @delete-item="handleDeleteItem" 
        />
      </main>
    </div>

    <AppFooter />
  </div>
</template>
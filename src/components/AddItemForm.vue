<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  message: { type: String, default: 'Anything else?', required: true }
})

const showForm = ref(false)

const displayForm = () => {
  console.log('siu')
  showForm.value = !showForm.value
}

// Define individual reactive properties for the form fields
const emits = defineEmits(['addItemToList'])

const newItem = ref({
  id: 0,
  name: '',
  amount: 0,
  highPriority: false,
  purchased: false
})

const addItem = () => {
  emits('addItemToList', { ...newItem.value })
  console.log('Item added:', newItem.value)
  // Reset form fields
  newItem.value = {
    id: 0,
    name: '',
    amount: 0,
    highPriority: false,
    purchased: false
  }
  showForm.value = false
}

// Characters handling
const characterCount = computed(() => newItem.value.name.length)
const characterLimit = ref(50)

const isButtonDisabled = computed(() => {
  return !newItem.value.name || newItem.value.amount <= 0 || isNaN(parseInt(newItem.value.amount))
})

const activeButton = computed(() => ({
  'under w-full py-1 px-0 rounded-md border-solid border border-black cursor-pointer bg-yellow-200 transition hover:bg-emerald-300 hover:scale-105': true,
  'disabled:opacity-50 disabled:pointer-events-none cursor-not-allowed': isButtonDisabled.value
}))
</script>

<template>
  <div
    class="new-item-container p-3 rounded-xl flex flex-col justify-between items-center flex-wrap bg-green-200 border border-black gap-3 text-center shadow-lg"
  >
    <h2>{{ message }}</h2>
    <form
      v-if="showForm"
      class="form flex h-full items-center flex-col justify-center w-full gap-5 transition"
      @submit.prevent="addItem"
    >
      <div class="inputs-container flex flex-col gap-2 w-full">
        <input
          class="border border-black rounded-md w-full p-1"
          v-model="newItem.name"
          type="text"
          placeholder="Add an item"
        />
        <p>{{ characterCount }} / {{ characterLimit }}</p>
        <input
          class="border border-black rounded-md w-full p-1"
          v-model="newItem.amount"
          type="number"
          placeholder="How many?"
        />
        <div class="p-1 flex items-center w-full justify-between">
          <p>Priority:</p>
          <label
            for="priority"
            class="cursor-pointer rounded-md border border-solid border-black bg-yellow-100 p-1 hover:scale-105 transition"
          >
            <input id="priority" type="checkbox" v-model="newItem.highPriority" /> High Priority
          </label>
        </div>
      </div>
      <button :class="activeButton" :disabled="isButtonDisabled">Save</button>
    </form>
    <div class="w-full hover:scale-105 transition">
      <button
        class="w-full p-1 rounded-md border border-black border-solid bg-yellow-200 hover:bg-yellow-300"
        v-if="!showForm"
        @click="displayForm"
      >
        Add Item
      </button>
      <button
        class="w-full p-1 rounded-md border border-black border-solid bg-red-200 hover:bg-red-300"
        v-else
        @click="displayForm"
      >
        Cancel
      </button>
    </div>
  </div>
</template>

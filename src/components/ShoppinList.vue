<template>
  <section
    class="main-container border p-5 rounded-lg shadow-md flex flex-col justify-center items-center w-full max-w-screen-lg gap-5"
  >
    <h1 class="title font-bold text-center text-xl">{{ header }}</h1>

    <div :class="secondaryContainer">
      <div
        class="new-item-container p-3 rounded-xl flex flex-col justify-between items-center flex-wrap bg-green-200 border border-black gap-3 text-center shadow-lg"
      >
        <h2>{{ msg }}</h2>
        <form
          v-if="showForm"
          class="form flex h-full items-center flex-col justify-center w-full gap-5 transition"
          @submit.prevent="addItem"
        >
          <div class="inputs-container flex flex-col gap-2 w-full">
            <input
              class="border border-black rounded-md w-full p-1"
              v-model="newItem"
              type="text"
              placeholder="Add an item"
            />
            <input
              class="border border-black rounded-md w-full p-1"
              v-model="newAmount"
              type="number"
              placeholder="How many?"
            />
            <div class="p-1 flex items-center w-full justify-between">
              <p>Priority:</p>
              <label for="priority">
                <input id="priority" type="checkbox" v-model="newHighPriority" /> High Priority
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

      <ul
        v-if="itemsArr && itemsArr.length > 0"
        class="shopping-list flex flex-col bg-blue-300 rounded-2xl p-3 gap-3 w-full border border-black max-w-screen-sm min-w-200 shadow-lg"
      >
        <li
          class="item p-1 border-black border border-solid rounded-md flex flex-row justify-between bg-white items-center transition"
          v-for="({ id, name, amount, highPriority }, index) in itemsArr"
          :key="id"
        >
          <p class="font-semibold">
            {{ id }}. {{ name }}: {{ amount }} {{ highPriority ? ' - High Priority🚩' : '' }}
          </p>
          <button class="bg-white cursor-pointer rounded-md transition" @click="removeItem(index)">
            <Trash2
              class="trash-icon transition hover:scale-125 hover:fill-red-300"
              :size="20"
              stroke-width="1"
            />
          </button>
        </li>
      </ul>
      <p
        v-else
        class="flex justify-center items-center font-medium text-center w-full max-w-screen-sm min-w-200"
      >
        Your list is empty 😅
      </p>
    </div>
  </section>
</template>

<script setup>
import { Trash2 } from 'lucide-vue-next'
import { ref, computed } from 'vue'

const header = ref('Shopping List App')
const itemsArr = ref([
  { id: 1, name: 'Party hats', amount: 10, highPriority: false },
  { id: 2, name: 'Board games', amount: 2, highPriority: false },
  { id: 3, name: 'Cups', amount: 20, highPriority: false }
])

const msg = ref('Anything else?')
const showForm = ref(false)
const newItem = ref(null)
const newAmount = ref(null)
const newHighPriority = ref(false)

const displayForm = () => {
  showForm.value = !showForm.value
}

const addItem = () => {
  itemsArr.value.push({
    id: itemsArr.value.length + 1,
    name: newItem.value,
    amount: parseInt(newAmount.value),
    highPriority: newHighPriority.value
  })
  // Reset form fields
  newItem.value = null
  newAmount.value = null
  newHighPriority.value = false
  msg.value = 'Item added. Anything else?'
}

const removeItem = (index) => {
  itemsArr.value.splice(index, 1)
  msg.value = 'Item removed'
}

const secondaryContainer = computed(() => ({
  'w-full flex flex-col justify-center items-center lg:flex-row lg:justify-center lg:items-start gap-5': true,
  'lg:items-center': itemsArr.value.length === 0
}))

const isButtonDisabled = computed(() => {
  return !newItem.value || !newAmount.value || newAmount.value <= 0 || !parseInt(newAmount.value)
})

const activeButton = computed(() => ({
  'under w-full py-1 px-0 rounded-md border-solid border border-black cursor-pointer bg-yellow-200 transition hover:bg-emerald-300 hover:scale-105': true,
  'disabled:opacity-50 disabled:pointer-events-none cursor-not-allowed': isButtonDisabled.value
}))
</script>

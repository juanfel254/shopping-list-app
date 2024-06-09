<script setup>
import { ref, computed } from 'vue'
import ShoppItem from './ShoppItem.vue'
import AddItemForm from './AddItemForm.vue'
import { ArrowUpDown } from 'lucide-vue-next'

const headerMessage = ref('Shopping List App')
const itemsArr = ref([
  { id: 1, name: 'Party hats', amount: 10, highPriority: false, purchased: true },
  { id: 2, name: 'Board games', amount: 4, highPriority: false, purchased: true },
  { id: 3, name: 'Cups', amount: 20, highPriority: false, purchased: false },
  { id: 4, name: 'Cake', amount: 1, highPriority: true, purchased: false }
])

// Error message and toggle form
const message = ref('Anything else?')

// Inverse list of items
const filterReverse = ref(false)
const applyFilter = () => {
  filterReverse.value = !filterReverse.value
}
const reversedItems = computed(() => {
  return [...itemsArr.value].reverse()
})

const addItem = (item) => {
  itemsArr.value.push({
    id: itemsArr.value.length + 1,
    name: item.name,
    amount: parseInt(item.amount),
    highPriority: item.highPriority
  })
  message.value = 'Item added. Anything else?'
}

const findItem = (i, item) => i.name === item.name && i.amount === item.amount

const deleteItem = (item) => {
  itemsArr.value = itemsArr.value.filter((i) => !findItem(i, item))
  if (itemsArr.value.length === 0) message.value = 'What are you buying today?'
  else message.value = 'Item deleted. Are you adding something else?'
}

const togglePurchase = (item) => {
  const itemList = itemsArr.value.find((i) => findItem(i, item))
  if (itemList) itemList.purchased = !itemList.purchased
}

const secondaryContainer = computed(() => ({
  'w-full flex flex-col justify-center items-center lg:flex-row lg:justify-center lg:items-start gap-5': true,
  'lg:items-center': itemsArr.value.length === 0
}))
</script>

<template>
  <section
    class="main-container border p-5 rounded-lg shadow-md flex flex-col justify-center items-center w-full max-w-screen-lg gap-5"
  >
    <h1 class="title font-bold text-center text-xl">{{ headerMessage }}</h1>

    <div :class="secondaryContainer">
      <AddItemForm :message="message" @addItemToList="addItem" />

      <div v-if="itemsArr && itemsArr.length > 0" class="flex flex-col w-28">
        <button
          v-bind="filterReverse"
          class="flex items-center justify-center gap-2 bg-cyan-100 p-1 text-sm rounded-md border border-solid border-black font-normal transition hover:bg-cyan-200"
          @click="applyFilter"
        >
          {{ !filterReverse ? 'Reverse' : 'Unreverse' }}
          <ArrowUpDown :size="20" :stroke-width="1" />
        </button>
      </div>

      <div
        v-if="itemsArr && itemsArr.length > 0"
        class="shopping-list flex flex-col bg-blue-300 rounded-2xl p-3 gap-3 w-full border border-black max-w-screen-sm min-w-200 shadow-lg"
      >
        <ShoppItem
          v-for="item in !filterReverse ? itemsArr : reversedItems"
          :key="item.id"
          :id="item.id"
          :name="item.name"
          :price="item.price"
          :amount="item.amount"
          :highPriority="item.highPriority"
          :purchased="item.purchased"
          @purchased="togglePurchase($event)"
          @removeItem="deleteItem($event)"
        />
      </div>
      <p
        v-else
        class="flex justify-center items-center font-medium text-center w-full max-w-screen-sm min-w-200"
      >
        Your list is empty 😅
      </p>
    </div>
  </section>
</template>

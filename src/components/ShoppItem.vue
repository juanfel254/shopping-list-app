<script setup>
import { ref } from 'vue'
import { Trash2 } from 'lucide-vue-next'

const props = defineProps({
  id: { type: Number, required: true },
  name: { type: String, required: true },
  amount: { type: Number, default: 1, required: true },
  highPriority: { type: Boolean, default: false },
  purchased: { type: Boolean, default: false }
})

const isDeleted = ref(false)

const togglePurchase = () => emits('purchased', props)

const deleteItem = () => {
  isDeleted.value = !isDeleted.value
  emits('removeItem', props)
}

const emits = defineEmits(['purchased', 'removeItem'])
</script>

<template>
  <div
    class="item p-1 border border-black rounded-md flex flex-row justify-between bg-white items-center transition hover:scale-105 cursor-pointer"
    @click="togglePurchase"
  >
    <p
      :class="{
        'line-through text-gray-400': props.purchased,
        'text-red-500': props.highPriority,
        'text-red-300': props.highPriority && props.purchased
      }"
      class="font-semibold pl-1"
    >
      {{ props.name }}: {{ props.amount }}
    </p>
    <button class="bg-white cursor-pointer rounded-md transition" @click.stop="deleteItem">
      <Trash2
        class="trash-icon transition hover:scale-125 hover:fill-red-300"
        :size="20"
        stroke-width="1"
      />
    </button>
  </div>
</template>

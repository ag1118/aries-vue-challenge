<template>
  <div class="flex items-center space-x-4 mb-4">
    <input v-model.number="localOption.strike_price" type="number" placeholder="Strike Price" class="border p-2 rounded-md w-20" />
    <select v-model="localOption.type" class="border p-2 rounded-md">
      <option value="Call">Call</option>
      <option value="Put">Put</option>
    </select>
    <input v-model.number="localOption.bid" type="number" placeholder="Bid" class="border p-2 rounded-md w-20" />
    <input v-model.number="localOption.ask" type="number" placeholder="Ask" class="border p-2 rounded-md w-20" />
    <select v-model="localOption.long_short" class="border p-2 rounded-md">
      <option value="long">Long</option>
      <option value="short">Short</option>
    </select>
    <button @click="removeOption" class="bg-red-500 text-white px-4 py-2 rounded-md">Remove</button>
  </div>
</template>

<script>
import { defineComponent, ref, watch } from 'vue'

export default defineComponent({
  props: {
    option: {
      type: Object,
      required: true
    }
  },
  emits: ['update', 'remove'],
  setup(props, { emit }) {
    const localOption = ref(props.option)

    // Watch for changes in localOption and emit update event
    watch(localOption, (newValue) => {
      emit('update', newValue)
    }, { deep: true })

    const removeOption = () => {
      emit('remove')
    }

    return {
      localOption,
      removeOption
    }
  }
})
</script>

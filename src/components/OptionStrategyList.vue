<template>
    <div>
        <h2 class="text-xl font-semibold mb-4">Options</h2>
        <div v-for="(option, index) in options" :key="index">
            <OptionStrategyItem :option="option" @update="onUpdateOption(index, $event)" @remove="onRemoveOption(index)" />
        </div>
        <div v-if="options.length < 4">
            <button @click="onAddOption" class="bg-blue-500 text-white px-4 py-2 rounded-md">Add</button>
        </div>
    </div>
</template>

<script>
import { defineComponent } from 'vue'
import OptionStrategyItem from './OptionStrategyItem.vue'

export default defineComponent({
    components: {
        OptionStrategyItem
    },
    props: {
        options: {
            type: Array,
            required: true,
        },
        onAddOption: {
            type: Function,
            required: true
        },
        onRemoveOption: {
            type: Function,
            required: true
        },
        onUpdateOption: {
            type: Function,
            required: true
        }
    }
})
</script>

<template>
  <div>
    <h1 class="text-3xl font-semibold mb-4">Options Profit Calculator</h1>
    <OptionsProfitCalculator :initialOptions="optionsData" />
  </div>
</template>

<script>
import OptionsProfitCalculator from './OptionsProfitCalculator'

export default {
  name: 'CodingChallenge',
  props: {
    optionsData: Array
  },
  components: {
    OptionsProfitCalculator
  },
}
</script>
<template>
    <div class="flex h-screen">
        <!-- Left side: OptionStrategyList -->
        <div class="w-1/2 p-4 border-r">
            <OptionStrategyList :options="options" :onAddOption="handleAddOption" :onUpdateOption="handleUpdateOption"
                :onRemoveOption="handleRemoveOption" />
            <div v-if="options.length > 0" class="mt-4 border-t p-4">
                <button @click="analyzeOptions" class="bg-blue-500 text-white px-4 py-2 rounded-md">Analyze</button>
            </div>
        </div>

        <!-- Right side: Analytics Result and Graph -->
        <div class="w-1/2 p-4">
            <div v-if="options.length > 0">
                <h2 class="text-xl font-semibold mb-4">Analytics Result</h2>
                <div>
                    <h3>Max Profit: {{ maxProfit.value }}</h3>
                    <h3>Max Loss: {{ maxLoss.value }}</h3>
                    <h3>Break Even Points: {{ breakEvenPoints.value.join(', ') }}</h3>
                </div>
                <LineChart :data="chartData" :chart-options="chartOptions" />
            </div>
            <div v-else>
                <p>No options selected.</p>
            </div>
        </div>
    </div>
</template>

<script>
import { defineComponent, ref, reactive } from 'vue'
import OptionStrategyList from './OptionStrategyList.vue'

import { Line } from 'vue-chartjs'
import { Chart as ChartJS, CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend } from 'chart.js'

ChartJS.register(CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend)

// Generate an array of prices from 0 to 200 with a step of 10, used for profit calculation
const PRICES = Array.from({ length: 21 }, (_, index) => index * 10)

export default defineComponent({
    components: {
        OptionStrategyList,
        LineChart: Line
    },
    props: {
        initialOptions: {
            type: Array,
            default: () => []
        }
    },
    computed: {
        chartData() {
            return {
                labels: PRICES,
                datasets: [
                    {
                        label: 'Profit/Loss',
                        data: this.profits.value,
                        borderColor: 'rgba(75, 192, 192, 1)',
                        borderWidth: 2,
                        fill: false
                    }
                ]
            }
        },
    },
    setup(props) {
        const options = ref([...props.initialOptions])

        const maxProfit = reactive({ value: 0 })
        const maxLoss = reactive({ value: 0 })
        const breakEvenPoints = reactive({ value: [] })

        const chartOptions = {
            responsive: true,
            scales: {
                x: {
                    title: {
                        display: true,
                        text: 'Price of Underlying at Expiry'
                    }
                },
                y: {
                    title: {
                        display: true,
                        text: 'Profit/Loss'
                    }
                }
            }
        }

        const profits = reactive({ value: PRICES.map(() => 0) })

        const handleAddOption = () => {
            options.value.push({
                strike_price: null,
                type: 'Call',
                bid: null,
                ask: null,
                long_short: 'long'
            })
        }

        const handleRemoveOption = (index) => {
            options.value.splice(index, 1)
        }

        const handleUpdateOption = (index, updatedOption) => {
            options.value[index] = updatedOption
        }

        const analyzeOptions = () => {
            profits.value = PRICES.map((price) => calculateProfit(price))

            // Calculate max profit, max loss, and break-even points
            maxProfit.value = Math.max(...profits.value)
            maxLoss.value = Math.min(...profits.value)
            breakEvenPoints.value = calculateBreakEvenPoints(profits.value, PRICES)
        }

        const calculateProfit = (price) => {
            return options.value.reduce((totalProfit, option) => {
                const { type, strike_price, bid, ask, long_short } = option
                const premium = (bid + ask) / 2
                const quantity = long_short.toLowerCase() === 'long' ? 1 : -1

                if (type.toLowerCase() === 'call') {
                    const intrinsicValue = Math.max(price - strike_price, 0)
                    totalProfit += (intrinsicValue - premium) * quantity
                } else if (type.toLowerCase() === 'put') {
                    const intrinsicValue = Math.max(strike_price - price, 0)
                    totalProfit += (intrinsicValue - premium) * quantity
                }

                return totalProfit
            }, 0)
        }

        const calculateBreakEvenPoints = (profits, prices) => {
            const breakEvens = []


            for (let i = 1; i < profits.length; i++) {
                if ((profits[i - 1] < 0 && profits[i] > 0) || (profits[i - 1] > 0 && profits[i] < 0)) {
                    breakEvens.push(prices[i])
                }
            }


            return breakEvens
        }

        return {
            options,
            profits,
            maxProfit,
            maxLoss,
            breakEvenPoints,
            chartOptions,
            handleAddOption,
            handleRemoveOption,
            handleUpdateOption,
            analyzeOptions
        }
    }
})
</script>

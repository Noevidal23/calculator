<template>
  <div class="flex justify-center items-center min-h-screen bg-gray-100">
    <div class="bg-white shadow-md rounded-lg p-6 w-64">
      <div class="mb-4 text-right bg-gray-200 p-2 rounded">
        <div class="text-2xl font-mono" aria-live="polite">{{ display }}</div>
      </div>
      <div class="grid grid-cols-4 gap-2">
        <button v-for="btn in buttons" :key="btn" @click="handleInput(btn)"
                class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded">
          {{ btn }}
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const display = ref('0')
const currentValue = ref(null)
const operator = ref(null)
const waitingForOperand = ref(true)

const buttons = [
  '7', '8', '9', '/',
  '4', '5', '6', '*',
  '1', '2', '3', '-',
  '0', '.', '=', '+'
]

const handleInput = (value) => {
  if (value >= '0' && value <= '9') {
    inputDigit(value)
  } else if (value === '.') {
    inputDecimal()
  } else if (['+', '-', '*', '/'].includes(value)) {
    handleOperator(value)
  } else if (value === '=') {
    handleEqual()
  }
}

const inputDigit = (digit) => {
  if (waitingForOperand.value) {
    display.value = digit
    waitingForOperand.value = false
  } else {
    display.value = display.value === '0' ? digit : display.value + digit
  }
}

const inputDecimal = () => {
  if (waitingForOperand.value) {
    display.value = '0.'
  } else if (display.value.indexOf('.') === -1) {
    display.value += '.'
  }
  waitingForOperand.value = false
}

const handleOperator = (nextOperator) => {
  const inputValue = parseFloat(display.value)

  if (currentValue.value == null) {
    currentValue.value = inputValue
  } else if (operator.value) {
    const result = calculate(currentValue.value, inputValue, operator.value)
    display.value = String(result)
    currentValue.value = result
  }

  waitingForOperand.value = true
  operator.value = nextOperator
}

const handleEqual = () => {
  const inputValue = parseFloat(display.value)

  if (operator.value) {
    const result = calculate(currentValue.value, inputValue, operator.value)
    display.value = String(result)
    currentValue.value = result
    operator.value = null
    waitingForOperand.value = true
  }
}

const calculate = (a, b, op) => {
  switch (op) {
    case '+':
      return a + b
    case '-':
      return a - b
    case '*':
      return a * b
    case '/':
      return b !== 0 ? a / b : 'Error'
    default:
      return b
  }
}
</script>
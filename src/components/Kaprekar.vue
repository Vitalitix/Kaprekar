<script setup lang="ts">
import { ref } from 'vue';

const props = defineProps<{ msg: string }>();
const inputNumber = ref<number | null>(null);
const errorMessage = ref<string | null>(null);
const stepsList = ref<{ value: number, formula: string }[]>([]);

const kaprekarSteps = (num: number): { value: number, formula: string }[] => {
  const steps: { value: number, formula: string }[] = [];
  let currentNum = num;
  while (currentNum !== 6174 && currentNum !== 0) {
    const digits = currentNum.toString().padStart(4, '0').split('');
    const asc = parseInt(digits.sort().join(''), 10);
    const desc = parseInt(digits.sort().reverse().join(''), 10);
    const diff = desc - asc;
    
    const formula = `${desc} - ${asc}`;
    steps.push({ value: diff, formula: formula });

    currentNum = diff;
  }
  return steps;
};

const calculateKaprekar = () => {
  errorMessage.value = null;
  stepsList.value = [];  
  const inputValue = inputNumber.value?.toString() || ''; 
  if (inputValue) {
    const numStrPadded = inputValue.padStart(4, '0');
    if (numStrPadded.length > 4) {
      errorMessage.value = "Please enter a number with up to 4 digits.";
    } else if (!/^\d+$/.test(numStrPadded)) {
      errorMessage.value = "Please enter only digits.";
    } else if (new Set(numStrPadded).size < 2 && numStrPadded.length === 4) {
      errorMessage.value = "The 4-digit number should have at least two different digits.";
    } else {
      stepsList.value = kaprekarSteps(parseInt(numStrPadded, 10));
    }
  } else {
    errorMessage.value = "Please enter a number.";
  }
};

</script>

<template>
  <h1>{{ msg }}</h1>
  <div class="card">
    <input 
      type="text" 
      v-model="inputNumber"
      placeholder="Enter a 4-digit number" 
      @keyup.enter="calculateKaprekar">
    <button type="button" @click="calculateKaprekar">
      Calculate Kaprekar's Constant
    </button>
    <table v-if="stepsList.length > 0" class="kaprekar-table">
      <thead>
        <tr><th>Step</th><th>Formula</th><th></th><th>Result</th></tr>
      </thead>
      <tbody>
        <tr v-for="(step, index) in stepsList" :key="index" class="kaprekar-row">
          <td>{{ index + 1 }}</td><td>{{ step.formula }}</td><td>=</td><td>{{ step.value }}</td>
        </tr>
      </tbody>
    </table>
    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
  </div>
 </template>

<style scoped>
.error {
  color: red;
  margin-top: 10px; 
}

.kaprekar-table {
  width: 100%;
  border-collapse: separate;
  border-spacing: 0;
  margin-top: 20px; 
}

.kaprekar-table th,
.kaprekar-table td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: right; 
}

.kaprekar-table th {
  background-color: #333; /* Darker background */
  color: #fff; /* White text */
}

.kaprekar-table td:nth-child(1), /* Step column */
.kaprekar-table td:nth-child(3) { /* = column */
  text-align: center; 
}
</style>

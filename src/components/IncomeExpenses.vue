<template>
    <div class="inc-exp-container">
      <div>
        <h4>Income</h4>
        <p id="money-plus" class="money plus">+${{ income }}</p>
      </div>
      <div>
        <h4>Expense</h4>
        <p id="money-minus" class="money minus">-${{ Math.abs(expenses) }}</p>
      </div>
    </div>

    <div class="balance-container">
      <div>
        <h4>Balance</h4>
        <p id="money-plus" class="money plus">+${{ balances }}</p>
      </div>
    </div>
  </template>
  
  <script setup>
  import { defineProps } from 'vue';
  
  const props = defineProps({
    income: {
      type: Number,
      required: true,
    },
    expenses: {
      type: Number,
      required: true,
    },
    balances: {
      type: Number,
      required: true,
    }
  });
  </script>
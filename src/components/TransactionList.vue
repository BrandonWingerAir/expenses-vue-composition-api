<template>
  <h3>Items</h3>

  <ul id="list" class="list">
    <li 
      v-for="transaction in transactions" 
      :key="transaction.id"
      :class="transaction.amount < 0 ? 'minus' : 'plus'"
    >
      {{transaction.text}} <span>${{transaction.amount}}</span>
      <button @click="editTransaction(transaction.id)" class="edit-btn">✏️</button>
      <button @click="deleteTransaction(transaction.id)" class="delete-btn">x</button>
    </li>

    <!-- Placeholder Items -->
    <!-- <li class="minus">
      Cash <span>-$400</span><button class="delete-btn">x</button>
    </li>
    <li class="plus">
      Paycheck <span>$800</span><button class="delete-btn">x</button>
    </li> -->
  </ul>
</template>

<script setup>
import { defineProps } from 'vue';

const emit = defineEmits(['transactionDeleted', 'transactionEdit']);

const props = defineProps({
  transactions: {
    type: Array,
    required: true
  }
});

const editTransaction = (id) => {
  emit('transactionEdit', id);
};

const deleteTransaction = (id) => {
  emit('transactionDeleted', id);
};
</script>
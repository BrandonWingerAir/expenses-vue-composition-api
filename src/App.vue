<template>
  <Header/>
  <div class="container">
    <Balance :total="+total"/>
    <IncomeExpenses :income="+income" :expenses="+expenses"/>
    <TransactionList :transactions="transactions" @transactionEdit="handleTransactionEdit" @transactionDeleted="handleTransactionDeleted"/>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
  </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

import {useToast} from 'vue-toastification';

import { ref, computed, onMounted } from 'vue';

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('expense_list_transactions'));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
}) 

// Total Amount
const total = computed(() => {
  return transactions.value.reduce((totalAmount, transaction) => {
    return totalAmount + transaction.amount
  }, 0);
});

// Total Income
const income = computed(() => {
  return transactions.value
  .filter((transaction) => transaction.amount > 0)
  .reduce((totalAmount, transaction) => {
    return totalAmount + transaction.amount
  }, 0)
  .toFixed(2);
});

// Total Expenses
const expenses = computed(() => {
  return transactions.value
  .filter((transaction) => transaction.amount < 0)
  .reduce((totalAmount, transaction) => {
    return totalAmount + transaction.amount
  }, 0)
  .toFixed(2);
});

// Add Item
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  });

  saveListToLocalStorage();

  toast.success('Transaction added!');
};

// Generate ID
const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
}

// Edit Item (Incomplete)
const handleTransactionEdit = (id) => {
  const transactionToEdit = transactions.value.filter((transaction) => transaction.id == id);
  console.log(transactionToEdit[0].text + ': $' + transactionToEdit[0].amount);
}

// Delete Item
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id);
  saveListToLocalStorage();
  toast.success('Transaction deleted.');
}

// Save to local storage
const saveListToLocalStorage = () => {
  localStorage.setItem('expense_list_transactions', JSON.stringify(transactions.value));
}
</script>
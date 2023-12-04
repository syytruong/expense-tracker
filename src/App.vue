<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpenses :income="+income" :expenses="+expenses" :balances="+balances" />
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import { addDoc, collection, getDocs, deleteDoc, doc } from "firebase/firestore";
import { computed, ref } from 'vue';
import { useToast } from 'vue-toastification';
import AddTransaction from './components/AddTransaction.vue';
import Balance from './components/Balance.vue';
import Header from './components/Header.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import { db } from './libs/firebase';

const toast = useToast();

const transactions = ref([]);

// Get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

// Get income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2);
});

// Get expenses
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2);
});

// Get balances
const balances = computed(() => {
  return Math.abs(income.value) - Math.abs(expenses.value);
});

// Submit transaction
const handleTransactionSubmitted = async (transactionData) => {
  try {
    await addDoc(collection(db, "transactions"), {
      id: generateUniqueId(),
      text: transactionData.text,
      amount: transactionData.amount,
    });
    toast.success('Transaction added.');
  } catch (e) {
    console.log(e);
    toast.error('Transaction add failed.');
  }
};

// Generate unique ID
const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};

// Delete transaction
const handleTransactionDeleted = async (id) => {
  try {
    await deleteDoc(doc(db, "transactions", id));
    getAllTransactions();
    toast.success('Transaction deleted.');
  } catch (e) {
    console.log(e);
    toast.error('Transaction delete failed.');
  }
};

// Get all transactions from db
const getAllTransactions = async () => {
  transactions.value = [];
  const querySnapshot = await getDocs(collection(db, "transactions"));
  querySnapshot.forEach((doc) => {
    transactions.value.push({
      ...doc.data(),
      id: doc.id,
    })
  });
}

getAllTransactions();
</script>
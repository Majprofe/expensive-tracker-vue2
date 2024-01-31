<template>
  <Header />
  <div class="container">
    <Balance :total="+total"/>
    <IncomeExpenses :income="+income" :expenses="+expenses"/>
    <TransactionList 
    :transactions="transactions"
    @deleteTransaction="handleTransactionDeleted" />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from './components/Header.vue'
import Balance from './components/Balance.vue'
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

import {useToast} from 'vue-toastification'

const toast = useToast()

import { ref, computed, onMounted } from 'vue'

/* const transactions = ref([
  { id: 1, text: 'Flower', amount: -19.99 },
  { id: 2, text: 'Salary', amount: 299.97 },
  { id: 3, text: 'Book', amount: -10 },
  { id: 4, text: 'Camera', amount: 150 }
]) */

const transactions = ref([])

onMounted(()=>{
  const savedTransaction = JSON.parse(localStorage.getItem('transactions'))
  if (savedTransaction) {
    transactions.value = savedTransaction
  }
})

//Get total
const total = computed(()=>{
  return transactions.value.reduce((acc, transaction)=>{
    return acc + transaction.amount
  }, 0).toFixed(2)
})

//Get income
const income = computed(()=>{
  return transactions.value
  .filter((transaction) => transaction.amount > 0)
  .reduce((acc, transaction)=>{
    return acc + transaction.amount
  }, 0).toFixed(2)
})

//Get expenses
const expenses = computed(()=>{
  return transactions.value
  .filter((transaction) => transaction.amount < 0)
  .reduce((acc, transaction)=>{
    return acc + transaction.amount
  }, 0).toFixed(2)
})

//Get a unique ID

const generateUniqueID = () => {
  return Math.floor(Math.random() * 1000000)
}

//Add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueID(),
    text: transactionData.text,
    amount: transactionData.amount
  })
  saveTransactionsToLocalStorage()
  toast.success('Transaction added')
}

//Delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id)
  saveTransactionsToLocalStorage()
  toast.success('Transaction deleted')
}

//Save to localstore
const saveTransactionsToLocalStorage = () =>{
  localStorage.setItem('transactions', JSON.stringify(transactions.value))
}

</script>

<style scoped></style>

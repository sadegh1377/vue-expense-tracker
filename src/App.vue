<template>
    <Header/>
    <div class="container">
        <Balance :total="+total"/>
        <IncomeExpenses :income="+income" :expenses="+expenses"/>
        <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/>
        <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
    </div>
</template>

<script setup>
import Header from "@/components/Header.vue";
import Balance from "@/components/Balance.vue";
import IncomeExpenses from "@/components/IncomeExpenses.vue";
import TransactionList from "@/components/TransactionList.vue";
import AddTransaction from "@/components/AddTransaction.vue";
import {useToast} from "vue-toastification";
import {ref, computed, onMounted} from "vue";

const transactions = ref([])
const toast = useToast()

onMounted(() => {
    const savedTransaction = JSON.parse(localStorage.getItem('transaction'))

    if (savedTransaction) {
        transactions.value = savedTransaction
    }
})

// Get total
const total = computed(() => {
    return transactions.value.reduce((acc, transactions) => {
        return acc + transactions.amount
    }, 0).toFixed(2)
})

// Get income
const income = computed(() => {
    return transactions.value.filter((transaction) => transaction.amount > 0).reduce((acc, transactions) => {
        return acc + transactions.amount
    }, 0).toFixed(2)
})

// Get expense
const expenses = computed(() => {
    return transactions.value.filter((transaction) => transaction.amount < 0).reduce((acc, transactions) => {
        return acc + transactions.amount
    }, 0).toFixed(2)
})

// Add transaction
const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
        id: generateUniqueId(),
        text: transactionData.text,
        amount: transactionData.amount
    })
    saveTransactionToLocalStorage()
    toast.success("Transaction added")
}

// Generate unique ID
const generateUniqueId = () => {
    return Math.floor(Math.random() * 1000000)
}

// Delete transaction

const handleTransactionDeleted = (id) => {
    transactions.value = transactions.value.filter((transaction) => {
        return transaction.id !== id
    })
    saveTransactionToLocalStorage()
    toast.success('Transaction deleted')
}

// Save to localStorage

const saveTransactionToLocalStorage = () => {
    localStorage.setItem('transaction', JSON.stringify(transactions.value))
}
</script>
<style scoped>
</style>

<template>
<Header />
<div class="container">
    <Balance :total="total" />
    <IncomeExpense :income="+income" :expense="+expense" />
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
</div>
</template>

<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpense from "./components/IncomeExpense.vue";
import TransactionList from "./components/TransactionList.vue"
import AddTransaction from "./components/AddTransaction.vue"
import {
    useToast
} from "vue-toastification";

import {
    ref,
    computed,
    onMounted
} from 'vue'

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

    if (savedTransactions) {
        transactions.value = savedTransctions;
    }
});

//get the total
const total = computed(() => {
    return transactions.value.reduce((accumulator, currval) => {
        return accumulator + currval.amount
    }, 0);
});

//get the income
const income = computed(() => {
    return transactions.value
        .filter((transaction) => transaction.amount > 0)
        .reduce((accumulator, transaction) =>
            accumulator + transaction.amount, 0
        ).toFixed(2);
});

//get the expense
const expense = computed(() => {
    return transactions.value
        .filter((transaction) => transaction.amount < 0)
        .reduce((accumulator, transaction) =>
            accumulator + transaction.amount, 0
        ).toFixed(2);
});

//Add transaction
const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
        id: generateUniqueId(),
        text: transactionData.text,
        amount: transactionData.amount,
    });

    saveTransactionsToLocalStorage();

    toast.success('Transaction added');
}

//generate Unique id
const generateUniqueId = () => {
    return Math.floor(Math.random() * 1000000);
}

//Delete transaction
const handleTransactionDeleted = (id) => {
    transactions.value = transactions.value.filter(
    (transaction) =>  transaction.id !== id);
    saveTransactionsToLocalStorage();

    toast.success('Transaction deleted');
}

//save to local storage
const saveTransactionsToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value));
};
</script>

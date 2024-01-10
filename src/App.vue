<template>
<Header />
<div class="container">
    <Balance :total="+total" />
</div>
<IncomeExpense :income="+income" :expense="+expense" />
<TransactionList :transactions="transactions" />
<AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
</template>

<script>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpense from "./components/IncomeExpense.vue";
import TransactionList from "./components/TransactionList.vue"
import AddTransaction from "./components/AddTransaction.vue"

import {
    ref,
    computed
} from 'vue'

const transactions = ref([{
        id: 1,
        text: 'flower',
        amount: -19.9
    },
    {
        id: 2,
        text: 'books',
        amount: 9
    },
    {
        id: 3,
        text: 'Pen',
        amount: -40
    },
    {
        id: 4,
        text: 'human',
        amount: 150
    },
]);

//get the total
const total = computed(() => {
    return transactions.value.reduce((accumulator, currval) => {
        return accumulator + currval.amount
    }, 0);
});

//get the income
const income = computed(() => {
    return transactions.value
        .filter((transactions) => transactions.amount > 0)
        .reduce((accumulator, currval) =>{
          return accumulator + currval.amount, 0}).toFixed(2);
});

//get the expense
const expense = computed(() => {
    return transactions.value
        .filter((transactions) => transactions.amount < 0)
        .reduce((accumulator, currval) =>{
           return accumulator + currval.amount, 0}).toFixed(2);
});

//handle submit action
const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
        id: generateUniqueId(),
        text: transactionData.text,
        amount: transactionData.amount
    });

}

const generateUniqueId = () => {
    return Math.floor(Math.random() * 1000000);
}
</script>

<style>

</style>

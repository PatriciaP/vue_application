<template>
  <div class="container">
    <h1>Finance Manager</h1>

    <h2>Transactions</h2>
    <table class="table" style="width: 100%;">
      <thead>
        <tr>
          <th>Date</th>
          <th>Description</th>
          <th>Amount</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(transaction, index) in transactions" :key="index">
          <td>{{ new Date(transaction.date).toLocaleDateString('pt-BR') }}</td>
          <td>{{ transaction.description }}</td>
          <td v-bind:class="{ 'negative': transaction.amount < 0, 'positive': transaction.amount > 0 }">
            {{
              transaction.amount.toLocaleString('pt-BR', {
                style: 'currency',
                currency: 'BRL'
              })
            }}</td>
          <td>
            <button class="btn btn-primary" @click="editTransaction(transaction)">Edit</button>
            <button class="btn btn-delete" @click="deleteTransaction(transaction)">Delete</button>
          </td>
        </tr>
      </tbody>

    </table>
     <tfoot>
            <tr>
              <td>Balance:</td>
              <td></td>
              <td v-bind:class="{ 'negative': calculateBalance() < 0, 'positive': calculateBalance() > 0 }">
                {{
                  calculateBalance().toLocaleString('pt-BR', {
                    style: 'currency',
                    currency: 'BRL'
                  })
                }}</td>
              <td></td>
            </tr>
          </tfoot>

    <div v-if="!editingTransaction" class="form-container">
      <h2>Add Transaction</h2>
      <form @submit.prevent="addTransaction">
        <label>
          Date:
          <input type="date" v-model="newTransaction.date" required>
        </label>
        <label>
          Description:
          <input type="text" v-model="newTransaction.description" required>
        </label>
        <label>
          Amount:
          <input type="number" v-model="newTransaction.amount" required>
        </label>
        <button class="btn btn-primary" type="submit">Add</button>
      </form>
    </div>

    <div v-if="editingTransaction" class="form-container">
      <h2>Edit Transaction</h2>
      <form @submit.prevent="updateTransaction">
        <label>
          Date:
          <input type="date" v-model="editingTransaction.date" required>
        </label>
        <label>
          Description:
          <input type="text" v-model="editingTransaction.description" required>
        </label>
        <label>
          Amount:
          <input type="number" v-model="editingTransaction.amount" required>
        </label>
        <button class="btn btn-primary" type="submit">Update</button>
        <button class="btn btn-delete" @click="cancelEdit">Cancel</button>
      </form>
    </div>


  </div>
</template>
<script>
import { ref } from 'vue';
import transactionsData from "./assets/data.json";


export default {
  name: 'TransactionList',
  setup() {
    
    const transactions = ref(transactionsData);
    const newTransaction = ref({ id: 0, date: new Date(), description: '', amount: 0 });
    const editingTransaction = ref(null);

    const addTransaction = () => {
      transactions.value.push({ ...newTransaction.value });
      newTransaction.value = {id: 0, date: new Date(), description: '', amount: 0 };
    };

    const editTransaction = (transaction) => {
      editingTransaction.value = { ...transaction };
    };

    const calculateBalance = () => {
      return transactions.value.reduce((total, transaction) => {
        return total + transaction.amount;
      }, 0);
    };


    const updateTransaction = () => {
      const index = transactions.value.findIndex(
        (transaction) => transaction.id === editingTransaction.value.id
      );
      transactions.value[index].date = editingTransaction.value.date;
      transactions.value[index].description = editingTransaction.value.description;
      transactions.value[index].amount = editingTransaction.value.amount;
      editingTransaction.value = null;
    };


    const deleteTransaction = (transaction) => {
      const index = transactions.value.findIndex(
        (t) => t === transaction
      );
      transactions.value.splice(index, 1);
    };

    return {
      transactions: transactionsData,
      newTransaction,
      editingTransaction,
      addTransaction,
      editTransaction,
      updateTransaction,
      deleteTransaction,
      calculateBalance,
    };
  },
};
</script>


<style>
body {
  font-family: sans-serif;
  margin: 0;
}

h1 {
  text-align: center;
  margin: 1rem 0;
}

.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.form-container {
  margin-bottom: 20px;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.form-container h2 {
  margin-bottom: 10px;
}

label {
  display: block;
  margin-bottom: 5px;
  flex-direction: column;
  margin-top: 1rem;
}

input[type="date"],
input[type="text"],
input[type="number"] {
  display: block;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
  font-size: 16px;
}

button {
  margin-top: 1rem;
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 4px;
  color: white;
  font-weight: bold;
  cursor: pointer;
}

.btn-primary {
  background-color: #0574eb;
  color: #fff;
  border: none;
  margin-right: 10px;
}

button.btn-primary:hover {
  background-color: #046bdaa1;
}

.btn-delete {
  background-color: #dc143c;
  margin-left: 0.5rem;
}

button.btn-delete:hover {
  background-color: #e60e39b6;
}

form {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin-bottom: 2rem;
}

input {
  padding: 0.5rem;
  border: none;
  border-bottom: 1px solid #ccc;
  width: 100%;
  margin-top: 0.5rem;
}

table {
  display: block;
  max-width: 100%;
  max-height: 600px;
  overflow-y: scroll;
  scroll-behavior: smooth;
  table-layout: fixed;
}

th,
td {
  padding: 0.5rem;
  text-align: left;
  border-bottom: 1px solid #ccc;
  min-width: 180px;
  font-size: 14px;
}

th {
  background-color: #f2f2f2;
  font-weight: normal;
  resize: horizontal;
  min-width: 100px;
}

.negative {
  color: #dc143c;
}

.positive {
  color: #3cb371;
}

tfoot td:first-child {
  font-weight: bold;
}

.error {
  color: red;
  font-style: italic;
}

.form-row {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.form-group {
  margin-right: 15px;
  margin-bottom: 10px;
}


label {
  display: inline-block;
  width: 100px;
}

input[type='text'],
input[type='number'],
input[type='date'] {
  width: 400px;
}

.form-group label {
  display: inline-block;
  width: 100px;
}

.form-group input[type="date"] {
  width: 150px;
}

.form-group input[name="description"] {
  width: 400px;
}

.form-group input[name="amount"] {
  width: 150px;
}


</style>

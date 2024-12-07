<script setup lang="ts">
import { computed, ref } from 'vue'

interface Expenses {
  id: number;
  title: string;
  price: number;
  date: string;
}

const expenses = ref<Expenses[]>([])
const des = ref("")
const dates = ref("")
const cost = ref("")
const editingId = ref<number | null>(null)

const addExpense = () => {
  const isTaskAlreadyUsed = expenses.value.some(task => task.title === des.value)
  if (isTaskAlreadyUsed) {
    alert("Expense already exists")
    return
  }
  expenses.value.push({
    id: expenses.value.length + 1,
    title: des.value,
    price: parseFloat(cost.value),
    date: dates.value
  })
  console.log(expenses)
}

const startEdit = (expense: Expenses) => {
  editingId.value = expense.id
  des.value = expense.title
  dates.value = expense.date
  cost.value = expense.price.toString()
}

const saveEdit = () => {
  if (editingId.value === null) return

  const expenseIndex = expenses.value.findIndex(exp => exp.id === editingId.value)
  if (expenseIndex !== -1) {
    expenses.value[expenseIndex].title = des.value
    expenses.value[expenseIndex].date = dates.value
    expenses.value[expenseIndex].price = parseFloat(cost.value)
  }
  editingId.value = null  // Reset editing state
}

const remove = (id: number) => {
  expenses.value = expenses.value.filter(expense => expense.id !== id)
}

const totalExpenses = computed(() =>
    expenses.value.reduce((sum, expense) => sum + expense.price, 0)
)
</script>

<template>
  <h1>Expense Tracker</h1>
  <input v-model="dates" type="date">
  <input v-model="des" type="text" placeholder="Add title">
  <input v-model="cost" type="number" placeholder="Cost in €">
  <button @click="addExpense">Save</button>

  <ol>
    <li v-for="expense in expenses" :key="expense.id">
      <div v-if="editingId !== expense.id">
        Date: {{ expense.date }}. Description: {{ expense.title }}. Price: {{ expense.price }}€
        <button @click="startEdit(expense)">Edit</button>
      </div>
      <div v-else>
        <input v-model="des" placeholder="Edit title">
        <input v-model="dates" type="date" placeholder="Edit date">
        <input v-model="cost" type="number" placeholder="Edit price">
        <button @click="saveEdit">Save Edit</button>
      </div>
      <button @click="remove(expense.id)">Delete</button>
    </li>
  </ol>

  <h2>Total expenses: {{ totalExpenses }}€</h2>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>

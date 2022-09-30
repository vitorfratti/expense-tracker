<template>

<div class="transactions-container">

    <div v-if="item.amount >= 0 && item.completed" class="transaction favorite green">
        <div class="name-transaction-container">
            <p class="name-transaction">{{ item.title }}</p>
            <p class="incomeorexpense-transaction">{{ item.incomeorexpense }}</p>
        </div>
        <div class="amount-transaction-container">
            <p class="amount-transaction">${{ item.amount }}</p>
        </div>
        <div class="category-transaction-container">
            <p class="category-transaction">{{ item.category }}</p>
        </div>
        <div class="date-transaction-container">
            <p class="date-transaction">{{ item.date }}</p>
        </div>
        <div class="favoriteandedit-buttons-container">
            <button v-if="!item.completed" @click="$emit('favorite')"><img id="favorite-icon" src="../assets/star-unchecked.png" alt="star-unchecked"></button>
            <button v-else @click="$emit('favorite')"><img id="favorite-icon" src="../assets/star-checked.png" alt="star-unchecked"></button>
            <button @click="$emit('delete')"><img src="../assets/delete.png" alt="dots"></button>
        </div>
    </div>

    <div v-else-if="item.amount >= 0" class="transaction green">
        <div class="name-transaction-container">
            <p class="name-transaction">{{ item.title }}</p>
            <p class="incomeorexpense-transaction">{{ item.incomeorexpense }}</p>
        </div>
        <div class="amount-transaction-container">
            <p class="amount-transaction">${{ item.amount }}</p>
        </div>
        <div class="category-transaction-container">
            <p class="category-transaction">{{ item.category }}</p>
        </div>
        <div class="date-transaction-container">
            <p class="date-transaction">{{ item.date }}</p>
        </div>
        <div class="favoriteandedit-buttons-container">
            <button v-if="!item.completed" @click="$emit('favorite')"><img id="favorite-icon" src="../assets/star-unchecked.png" alt="star-unchecked"></button>
            <button v-else @click="$emit('favorite')"><img id="favorite-icon" src="../assets/star-checked.png" alt="star-unchecked"></button>
            <button @click="$emit('delete')"><img src="../assets/delete.png" alt="dots"></button>
        </div>
    </div>

    <div v-else-if="item.amount < 0 && item.completed" class="transaction favorite red">
        <div class="name-transaction-container">
            <p class="name-transaction">{{ item.title }}</p>
            <p class="incomeorexpense-transaction">{{ item.incomeorexpense }}</p>
        </div>
        <div class="amount-transaction-container">
            <p class="amount-transaction">${{ item.amount }}</p>
        </div>
        <div class="category-transaction-container">
            <p class="category-transaction">{{ item.category }}</p>
        </div>
        <div class="date-transaction-container">
            <p class="date-transaction">{{ item.date }}</p>
        </div>
        <div class="favoriteandedit-buttons-container">
            <button v-if="!item.completed" @click="$emit('favorite')"><img id="favorite-icon" src="../assets/star-unchecked.png" alt="star-unchecked"></button>
            <button v-else @click="$emit('favorite')"><img id="favorite-icon" src="../assets/star-checked.png" alt="star-unchecked"></button>
            <button @click="$emit('delete')"><img src="../assets/delete.png" alt="dots"></button>
        </div>
    </div>

    <div v-else-if="item.amount < 0" class="transaction red">
        <div class="name-transaction-container">
            <p class="name-transaction">{{ item.title }}</p>
            <p class="incomeorexpense-transaction">{{ item.incomeorexpense }}</p>
        </div>
        <div class="amount-transaction-container">
            <p class="amount-transaction">${{ item.amount }}</p>
        </div>
        <div class="category-transaction-container">
            <p class="category-transaction">{{ item.category }}</p>
        </div>
        <div class="date-transaction-container">
            <p class="date-transaction">{{ item.date }}</p>
        </div>
        <div class="favoriteandedit-buttons-container">
            <button v-if="!item.completed" @click="$emit('favorite')"><img id="favorite-icon" src="../assets/star-unchecked.png" alt="star-unchecked"></button>
            <button v-else @click="$emit('favorite')"><img id="favorite-icon" src="../assets/star-checked.png" alt="star-unchecked"></button>
            <button @click="$emit('delete')"><img src="../assets/delete.png" alt="dots"></button>
        </div>
    </div>

</div>

</template>

<script>

export default {
    name: 'Transactions',
    emits: ['favorite', 'delete'],
    props: ['item', 'title', 'amount', 'category', 'favoriteMode', 'incomeorexpense']
}

</script>

<style lang="scss" scoped>

:root {
  --primary-background: #ffffff;
  --secondary-background: #f8f8fb;
  --border-and-shadows: #dadada;
  --primary-color: #081231;
  --secondary-color: #646464;
  --percentbar-outside: #eceef1;
}

.dark-mode:root {
  --primary-background: #242424;
  --secondary-background: #1a1a1a;
  --border-and-shadows: #1d1d1d;
  --primary-color: #e1e1e1;
  --secondary-color: #e1e1e1;
  --percentbar-outside: #c0c0c0;
}
.transactions-container {
    width: 60%;
    margin: 0.5rem 0;
}

.transaction {
    background: var(--primary-background);
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem;
    border: 1px solid var(--border-and-shadows);
    border-radius: 0 10px 10px 0;
}

.transaction div {
    text-align: left;
    width: 10%;
}

.favoriteandedit-buttons-container {
    display: flex;
    justify-content: flex-end;
    align-items: center;
}

.favoriteandedit-buttons-container button {
    background: none;
    border: none;
    cursor: pointer;
    width: 1.5rem;
    height: 1.5rem;
}

.favoriteandedit-buttons-container button img {
    width: 1.2rem;
}

p {
    color: var(--primary-color);
}

p.incomeorexpense-transaction {
    font-size: 0.8rem;
}

.green {
    border-left: 3px solid #5cc372;
}

.red {
    border-left: 3px solid #f06464;
}

</style>
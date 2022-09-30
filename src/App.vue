<template>

<div id="app">

  <div id="modal-container" class="none">
    <div class="header-modal-container">
        <h2>Add transaction</h2>
        <button @click="closeModal" class="closemodal-btn"><img v-if="this.nightModeActive === true" src="./assets/close-white.png" alt="close"><img v-else src="./assets/close.png" alt="close"></button>
    </div>
    <div class="main-modal-container">
        <p class="label title">Title*</p>
        <input v-model="title" placeholder="Title" type="text">
        <p class="label amount">Amount*</p>
        <input v-model="amount" id="input-amount" placeholder="Amount" type="text" @input="inputAnimation" oninput="this.value = this.value.replace(/[^0-9.-]/g, '').replace(/(\..*?)\..*/g, '$1');">
        <p class="infoaboutamount">Note: - for expense and + for income</p>
        <p class="label category">Category*</p>
        <select id="input-select-category" v-model="category">
            <option disabled value=""></option>
            <option value="Personal">Personal</option>
            <option value="Bills">Bills</option>
            <option value="Leisure">Leisure</option>
            <option value="Health">Health</option>
            <option value="Food">Food</option>
        </select>
    </div>
    <div class="footer-modal-container">
        <button @click="closeModal" class="cancel-btn">Cancel</button>
        <button @click="createItem" class="addtransaction-btn">Add</button>
    </div>
  </div>

  <header>
    <h1 class="header-title">Expense Tracker</h1>
    <div class="header-right">
      <button v-if="this.nightModeActive === true" @click="nightMode" class="toggle-nightmode-btn right">
        <div id="circle-btn" class="circle-toggle"><img id="toggle-icon" class="toggle-icon" src="./assets/moon.png" alt="moon"></div>
      </button>
      <button v-else @click="nightMode" class="toggle-nightmode-btn">
        <div id="circle-btn" class="circle-toggle"><img id="toggle-icon" class="toggle-icon" src="./assets/sun.png" alt="sun"></div>
      </button>
      <button @click="openModal" class="openmodal-btn">Add</button>
    </div>
  </header>

  <main>
    <div class="main-container">
      <div class="boxes-container">
          <div class="box-container" id="balance">
              <p class="box-title">Balance</p>
              <p class="box-subtitle">Total Balance</p>
              <p class="box-value">${{ balance }}</p>
              <div class="percentbar-texts">
                  <p class="percentbar-income-text">{{ saved }}% saved</p>
                  <p class="percentbar-expense-text">{{ spent }}% spent</p>
              </div>
              <div id="percentbar-outside" class="percentbar-outside">
                  <div id="percentbar-inside-income" class="percentbar-inside-income"></div>
                  <div id="percentbar-inside-expense" class="percentbar-inside-expense"></div>
              </div>
          </div>
          <div class="box-container" id="income">
              <p class="box-title">Income</p>
              <p class="box-subtitle">Total Income</p>
              <p class="box-value">${{ income }}</p>
              <p class="percent-total-transaction-text"><span class="green-color">{{ saved }}%</span> of the total amount</p>
          </div>
          <div class="box-container" id="expense">
              <p class="box-title">Expense</p>
              <p class="box-subtitle">Total Expense</p>
              <p class="box-value">${{ expense }}</p>
              <p class="percent-total-transaction-text"><span class="red-color">{{ spent }}%</span> of the total amount</p>
          </div>
      </div>
      <h3 class="empty-text" id="empty-text">No transactions yet.</h3>
      <div id="recenttransactions-container" class="recenttransactions-container none">
          <h1 class="recenttransactions-title">Recent Transactions</h1>
          <div class="select-filters-container">
              <select v-on:change="filterItems" v-model="this.class">
                <option value="All">All</option>
                <option value="Income">Income</option>
                <option value="Expense">Expense</option>
                <option value="Favorites">Favorites</option>
              </select>
          </div>
          <div class="name-infos-container">
              <p class="info name">NAME</p>
              <p class="info amount">AMOUNT</p>
              <p class="info category">CATEGORY</p>
              <p class="info date">DATE</p>
              <p></p>
          </div>
          <div class="transactions-area">
            <Transactions v-for="(item, index) in items"
            :item="item" :amount="amount" :category="category" :incomeorexpense="incomeorexpense" :key="index"
            @favorite="checkFavorite(index)" @delete="deleteTransaction(index)"/>
          </div>
      </div>
    </div>
  </main>

</div>

</template>

<script>

import Transactions from './components/Transactions.vue'

export default {
    name: "App",
    components: { Transactions },
    data() {
      return {
        nightModeActive: false,
        item: '',
        title: '',
        incomeorexpense: '',
        amount: '',
        category: '',
        date: '',
        items: [],
        balance: 0,
        income: 0,
        expense: 0,
        saved: 0,
        spent: 0,
        class: 'All'
      }
    },
    methods: {
      nightMode() {
        this.nightModeActive = !this.nightModeActive
        document.querySelector('html').classList.toggle('dark-mode')
      },
      openModal() {
        document.getElementById('modal-container').classList.remove('none')
      },
      closeModal() {
        document.getElementById('modal-container').classList.add('none')
        document.getElementById('input-amount').classList.remove('border-red')
        document.getElementById('input-amount').classList.remove('border-green')
      },
      inputAnimation(){
        if(this.amount >= 0) {
          document.getElementById('input-amount').classList.remove('border-red')
          document.getElementById('input-amount').classList.add('border-green')
        } else {
          document.getElementById('input-amount').classList.remove('border-green')
          document.getElementById('input-amount').classList.add('border-red')
        }
      },
      createItem() {
        document.getElementById('input-amount').classList.remove('border-red')
        document.getElementById('input-amount').classList.remove('border-green')
        if(this.title !== '' && this.amount !== '' && this.category !== '')  {
          this.class = 'All'
          this.filterItems()
          if(this.items.length >= 0) {
            document.getElementById('recenttransactions-container').classList.remove('none')
            document.getElementById('empty-text').classList.add('none')
          } else {
            document.getElementById('recenttransactions-container').classList.add('none')
            document.getElementById('empty-text').classList.remove('none')
          }
          var data = new Date();
          var dia = String(data. getDate()). padStart(2, '0');
          var mes = String(data. getMonth() + 1). padStart(2, '0');
          var ano = data. getFullYear();
          var dataAtual = dia + '/' + mes + '/' + ano;
          this.date = dataAtual
          if(this.amount >= 0) {
            this.incomeorexpense = 'Income'
          } else {
            this.incomeorexpense = 'Expense'
          }
          this.balance = parseInt(this.balance) + parseInt(this.amount)
          if(this.amount >= 0) {
            this.income = parseInt(this.income) + parseInt(this.amount)
          } else {
            this.expense = parseInt(this.expense) + parseInt(this.amount)
          }
          if(this.income > 0) {
            this.saved = (parseInt(this.income) / (parseInt(this.income - parseInt(this.expense))) * 100).toFixed(0)
            document.getElementById('percentbar-inside-income').style.width = this.saved + '%'
          } if(this.expense < 0) {
            this.spent = (parseInt(this.expense) / (parseInt(this.income - parseInt(this.expense))) * 100).toFixed(0).substring(1)
            document.getElementById('percentbar-inside-expense').style.width = this.spent + '%'
          } if(this.saved === '100') {
            document.getElementById('percentbar-inside-income').classList.add('border-radius-full')
          } else {
            document.getElementById('percentbar-inside-income').classList.remove('border-radius-full')
          } if(this.spent === '100') {
            document.getElementById('percentbar-inside-expense').classList.add('border-radius-full')
          } else {
            document.getElementById('percentbar-inside-expense').classList.remove('border-radius-full')
          }
          this.items.push({ title: this.title, amount: this.amount, incomeorexpense: this.incomeorexpense, category: this.category, date: this.date, completed: false })
          this.closeModal()
          this.title = ''
          this.amount = ''
          this.category = ''
        }
      },
      deleteTransaction(index) {
        this.class = 'All'
        this.filterItems()
        if(this.items[index].amount >= 0) {
          this.balance = parseInt(this.balance) - parseInt(this.items[index].amount)
          this.income = parseInt(this.income) - parseInt(this.items[index].amount)
          this.saved = (parseInt(this.income) / (parseInt(this.income) - parseInt(this.expense)) * 100).toFixed(0)
          this.spent = parseInt(this.expense) / (parseInt(this.income) - parseInt(this.expense)) * 100
          if(this.spent !== 0) {
            this.spent = (parseInt(this.expense) / (parseInt(this.income) - parseInt(this.expense)) * 100).toFixed(0).substring(1)
          }
          document.getElementById('percentbar-inside-income').style.width = this.saved + '%'
          document.getElementById('percentbar-inside-expense').style.width = this.spent + '%'
        } else {
          this.balance = parseInt(this.balance) - parseInt(this.items[index].amount)
          this.expense = parseInt(this.expense) - parseInt(this.items[index].amount)
          this.saved = (parseInt(this.income) / (parseInt(this.income) - parseInt(this.expense)) * 100).toFixed(0)
          this.spent = parseInt(this.expense) / (parseInt(this.income) - parseInt(this.expense)) * 100
          if(this.spent !== 0) {
            this.spent = (parseInt(this.expense) / (parseInt(this.income) - parseInt(this.expense)) * 100).toFixed(0).substring(1)
          }
          document.getElementById('percentbar-inside-income').style.width = this.saved + '%'
          document.getElementById('percentbar-inside-expense').style.width = this.spent + '%'
        }
        if(this.income === 0 && this.expense === 0) {
          this.saved = 0
          this.spent = 0
          document.getElementById('percentbar-inside-income').style.width = this.saved + '%'
          document.getElementById('percentbar-inside-expense').style.width = this.spent + '%'
        }
        if(this.saved === '100') {
          document.getElementById('percentbar-inside-income').classList.add('border-radius-full')
        } else {
          document.getElementById('percentbar-inside-income').classList.remove('border-radius-full')
        } if(this.spent === '100') {
          document.getElementById('percentbar-inside-expense').classList.add('border-radius-full')
        } else {
          document.getElementById('percentbar-inside-expense').classList.remove('border-radius-full')
        }
        this.items.splice(index, 1)
        if(this.items.length === 0) {
          document.getElementById('recenttransactions-container').classList.add('none')
          document.getElementById('empty-text').classList.remove('none')
        }
      },
      checkFavorite(index) {
        this.items[index].completed = !this.items[index].completed
      },
      filterItems() {
        if(this.class === 'All') {
          let expenses = document.querySelectorAll('.red')
          expenses.forEach(divExpenses => { divExpenses.classList.remove('none') })
          let incomes = document.querySelectorAll('.green')
          incomes.forEach(divIncomes => { divIncomes.classList.remove('none') })
          let favorites = document.querySelectorAll('.favorite')
          favorites.forEach(divFavorites => { divFavorites.classList.remove('none') })
        } if(this.class === 'Income') {
          let favorites = document.querySelectorAll('.favorite')
          favorites.forEach(divFavorites => { divFavorites.classList.add('none') })
          let expenses = document.querySelectorAll('.red')
          expenses.forEach(divExpenses => { divExpenses.classList.add('none') })
          let incomes = document.querySelectorAll('.green')
          incomes.forEach(divIncomes => { divIncomes.classList.remove('none') })
        } if(this.class === 'Expense') {
          let favorites = document.querySelectorAll('.favorite')
          favorites.forEach(divFavorites => { divFavorites.classList.add('none') })
          let incomes = document.querySelectorAll('.green')
          incomes.forEach(divIncomes => { divIncomes.classList.add('none') })
          let expenses = document.querySelectorAll('.red')
          expenses.forEach(divExpenses => { divExpenses.classList.remove('none') })
        } if(this.class === 'Favorites') {
          let expenses = document.querySelectorAll('.red')
          expenses.forEach(divExpenses => { divExpenses.classList.add('none') })
          let incomes = document.querySelectorAll('.green')
          incomes.forEach(divIncomes => { divIncomes.classList.add('none') })
          let favorites = document.querySelectorAll('.favorite')
          favorites.forEach(divFavorites => { divFavorites.classList.remove('none') })
        }
      }
    }
}

</script>

<style lang="scss">

@import url('https://fonts.googleapis.com/css2?family=Inter:wght@100;300;500;700;900&display=swap');

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
  --percentbar-outside: #e1e1e1;
}

* {
  margin: 0%;
  padding: 0%;
  box-sizing: border-box;
  font-family: 'Inter', sans-serif;
}

body {
  height: 100vh;
  background: var(--secondary-background);
}

header {
  background: var(--primary-background);
  padding: 1.5rem 2.5rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 0 3px var(--border-and-shadows);

  h1 {
    font-size: 2rem;
    color: var(--primary-color);
  }

  .header-right {
    width: 12rem;
    display: flex;
    justify-content: space-between;
    align-items: center;

    button.toggle-nightmode-btn {
      background: var(--secondary-background);
      width: 4rem;
      height: 2rem;
      border-radius: 15px;
      border: 1px solid var(--border-and-shadows);
      padding: 0.2rem;
      cursor: pointer;
      display: flex;
      justify-content: flex-start;

      .circle-toggle {
        background: var(--primary-background);
        width: 45%;
        height: 100%;
        border-radius: 50%;
        border: 1px solid var(--border-and-shadows);
        display: flex;
        justify-content: center;
        align-items: center;

        img.toggle-icon {
          width: 0.8rem;
        }
      }
    }

    button.openmodal-btn {
      background: var(--secondary-background);
      width: 6rem;
      height: 2rem;
      border-radius: 15px;
      cursor: pointer;
      border: 1px solid var(--border-and-shadows);
      color: var(--primary-color);
    }
  }
}

main {
  background: var(--secondary-background);
  padding: 2rem 2rem 0 2rem;
  max-height: 100vh;

  .boxes-container {
    display: flex;
    justify-content: flex-start;
    overflow-x: auto;

    .box-container {
      background: var(--primary-background);
      width: 33%;
      height: 17rem;
      box-shadow: 0 0 3px var(--border-and-shadows);
      margin: 0 1rem;
      padding: 1.5rem 1.8rem;
      border-radius: 10px;

      p.box-title {
        font-size: 1.5rem;
        font-weight: 400;
        color: var(--primary-color);
      }

      p.box-subtitle {
        font-size: 1rem;
        font-weight: 300;
        color: var(--secondary-color);
        margin-top: 1.5rem;
      }

      p.box-value {
        font-size: 2.5rem;
        color: var(--primary-color);
      }

      .percentbar-texts {
        margin-top: 2rem;
        display: flex;
        justify-content: space-between;

        p.percentbar-income-text {
          color: var(--primary-color);
        }

        p.percentbar-expense-text {
          color: var(--primary-color);
        }
      }

      .percentbar-outside {
        background: var(--percentbar-outside);
        margin-top: 1rem;
        width: 100%;
        height: 0.75rem;
        background: var(--percentbar-outside);
        border-radius: 20px;
        display: flex;

        .percentbar-inside-income {
          width: 0;
          background: #5cc372;
          border-radius: 20px 0 0 20px;
          transition: 1s;
        }

        .percentbar-inside-expense {
          width: 0;
          background: #f06464;
          border-radius: 0 20px 20px 0;
          transition: 1s;
        }
      }
    }
  }
}

.percent-total-transaction-text {
  margin-top: 2rem;
  color: var(--secondary-color);
  font-weight: 300;
}

span.green-color {
  font-weight: 400;
  color: #5cc372;
}

span.red-color {
  font-weight: 400;
  color: #f06464;
}

h3.empty-text {
  margin: 2.5rem 1rem;
  color: var(--primary-color);
  font-weight: 300;
}

.recenttransactions-container {
  margin: 2rem 1rem 2rem 1rem;

  h1.recenttransactions-title {
    font-size: 1.5rem;
    color: var(--primary-color);
  }

  .select-filters-container {
    border-radius: 10px;
    margin-top: 1rem;

    select {
      background: var(--primary-background);
      border: 1px solid var(--border-and-shadows);
      width: 10rem;
      height: 2rem;
      border-radius: 4px;
      padding: 0 0.4rem;
      color: var(--primary-color);
    }
  }

  .name-infos-container {
    display: flex;
    justify-content: space-between;
    width: 60%;
    padding: 0 1rem;
    margin-top: 2rem;

    p {
      font-size: 0.9rem;
      color: var(--secondary-color);
      text-align: left;
      width: 10%;
    }
  }
}

.transactions-area {
  margin: 1rem 0;
  height: 22.5rem;
  overflow-y: scroll;
}

.transactions-area::-webkit-scrollbar {
  width: 1rem;
}

#modal-container {
  background: var(--primary-background);
  border-radius: 15px;
  padding: 2rem;
  box-shadow: 0 0 100rem #000;
  width: 36rem;
  position: absolute;
  top: 14rem;
  left: calc(50% - 18rem);

  .header-modal-container {
    display: flex;
    justify-content: space-between;

    h2 {
      color: var(--primary-color);
    }

    button.closemodal-btn {
      cursor: pointer;
      border: none;
      background: none;

      img {
        width: 0.9rem;
      }
    }
  }

  .main-modal-container {
    margin-top: 1rem;

    p.label {
      font-size: 0.9rem;
      color: var(--secondary-color);
      margin: 1rem 0 0.5rem 0;
    }

    p.infoaboutamount {
      font-size: 0.9rem;
      color: var(--secondary-color);
      margin-top: 0.75rem;
    }

    input, select {
      background: var(--secondary-background);
      color: var(--primary-color);
      width: 100%;
      height: 2.5rem;
      border: 1px solid var(--border-and-shadows);
      border-radius: 6px;
      padding-left: 0.5rem;
      font-size: 1rem;
    }
  }

  .footer-modal-container {
    display: flex;
    justify-content: flex-end;
    margin-top: 1.5rem;

    button.cancel-btn {
      background: var(--secondary-background);
      color: var(--primary-color);
      width: 8rem;
      height: 2.5rem;
      cursor: pointer;
      border: 1px solid var(--border-and-shadows);
      border-radius: 8px;
    }

    button.addtransaction-btn {
      background: var(--secondary-background);
      color: var(--primary-color);
      width: 8rem;
      height: 2.5rem;
      cursor: pointer;
      border: 1px solid var(--border-and-shadows);
      border-radius: 8px;
      margin-left: 1rem;
    }
  }
}

button.responsive-boxes-btn {
  display: none;
  background: none;
  border: none;
  cursor: pointer;

  img {
    width: 2rem;
  }
}

img#left-arrow {
  transform: rotate(180deg);
}

button:hover {
  opacity: 0.75;
}

.border-radius-full {
  border-radius: 20px !important;
}

.right {
  justify-content: flex-end !important;
}

.border-green {
  outline: 1px solid #5cc372;
  border: none;
}

.border-red {
  outline: 1px solid #f06464;
  border: none;
}

.none {
  display: none !important;
}

@media screen and (max-width: 1100px) {

  .name-infos-container {
    width: 100% !important;
  }

  .transactions-container {
    width: 100% !important;
  }

}

@media screen and (max-width: 790px) {

  p.box-value {
    font-size: 2rem !important;
  }

  button.responsive-boxes-btn {
    display: block;
  }

}

@media screen and (max-width: 710px) {

  p.box-value {
    font-size: 1.5rem !important;
  }

}

@media screen and (max-width: 650px) {

  .boxes-container {
    flex-wrap: wrap !important;
    width: 100%;
  }

  .box-container {
    width: 100% !important;
    margin-bottom: 0.5rem !important;
  }

  #modal-container {
    width: 20rem;
    left: calc(50% - 10rem);
  }

  .category-transaction-container {
    display: none;
  }

  .category {
    display: none;
  }

}

@media screen and (max-width: 550px) {

  .name-infos-container {
    display: none !important;
  }

  .transaction div {
    width: 33% !important;
  }

  .date-transaction-container {
    display: none !important;
  }

}

</style>

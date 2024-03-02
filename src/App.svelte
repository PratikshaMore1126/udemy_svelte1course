<script>
  import { setContext, onMount, afterUpdate } from "svelte";

  //component
  import Navbar from "./Components/navbar.svelte";
  import ExpenseList from "./Components/expenseList.svelte";
  import Totals from "./Components/total.svelte";
  import ExpenseForm from "./Components/expenseForm.svelte";
  // import expensesData from "./Components/expenses";

  import Modal from "./Components/modal.svelte";
  //variable
  // let expenses = [...expensesData];
  let expenses = [];
  //set editing variables
  let setName = "";
  let setAmount = null;
  let setId = null;

  // toggle form variables
  let isFormOpen = false;

  //reactive
  $: isEditing = setId ? true : false;
  $: total = expenses.reduce((acc, curr) => {
    return (acc += curr.amount);
  }, 0);

  //functions

  function showForm() {
    isFormOpen = true;
  }
  function hideForm() {
    isFormOpen = false;
    setAmount = null;
    setName = "";
    setId = null;
  }

  function removeExpense(id) {
    expenses = expenses.filter((item) => item.id !== id);
    // setLocalStorage();
  }
  function clearExpenses() {
    expenses = [];
    // setLocalStorage();
  }
  function addExpense({ name, amount }) {
    let expense = { id: Math.random() * Date.now(), name, amount };
    expenses = [expense, ...expenses];
    // setLocalStorage();
  }
  function setModifiedExpense(id) {
    let expense = expenses.find((item) => item.id === id);

    setId = expense.id;
    setName = expense.name;
    setAmount = expense.amount;
    showForm();
  }
  function editExpense({ name, amount }) {
    expenses = expenses.map((item) => {
      return item.id === setId ? { ...item, name, amount } : { ...item };
    });
    setId = null;
    setAmount = null;
    setName = "";
    // setLocalStorage();
  }

  //context
  setContext("remove", removeExpense);
  setContext("modify", setModifiedExpense);

  //local Storage
  function setLocalStorage() {
    localStorage.setItem("expenses", JSON.stringify(expenses));
  }

  onMount(() => {
    expenses = localStorage.getItem("expenses")
      ? JSON.parse(localStorage.getItem("expenses"))
      : [];
  });

  afterUpdate(() => {
    setLocalStorage();
  });
</script>

<Navbar {showForm} />

<main class="content">
  {#if isFormOpen}
    <Modal>
      <ExpenseForm
        {addExpense}
        name={setName}
        amount={setAmount}
        {isEditing}
        {editExpense}
        {hideForm}
      />
    </Modal>
  {/if}
  <Totals title="total expenses" {total} />

  <ExpenseList {expenses} />
  <button
    type="button"
    class="btn btn-primary btn-block"
    on:click={clearExpenses}
  >
    clear expenses
  </button>
</main>

<!-- <Modal
  >
  <h1 slot="header">hello world</h1>
  <p slot="footer">
    Lorem ipsum, dolor sit amet consectetur adipisicing elit. Ipsam, quos
    laboriosam. Obcaecati velit quam cupiditate veniam, facere eos hic nemo
    veritatis odio, incidunt sequi architecto sapiente reprehenderit! Sint, in
    nobis!
  </p></Modal
> -->

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }
  /* 
  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 2em;
    font-weight: 100;
  } */

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>

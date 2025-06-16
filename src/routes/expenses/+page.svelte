<script>
  import { supabase } from '$lib/supabaseClient';
  import ExpenseForm from '$lib/components/ExpenseForm.svelte'; // <-- Import our new component

  let expenses = [];
  let loading = true;
  let error;

  async function getExpenses() {
    loading = true; // Set loading to true when we re-fetch
    const { data, error: fetchError } = await supabase
      .from('expenses')
      .select('*')
      .order('date', { ascending: false });

    if (fetchError) {
      error = fetchError.message;
      console.error("Error fetching expenses:", fetchError);
    } else {
      expenses = data;
    }
    loading = false;
  }

  async function deleteExpense(id) {
    // Optimistic UI: Remove from the list immediately
    expenses = expenses.filter(expense => expense.id !== id);

    // Call Supabase to delete the row from the database
    const { error: deleteError } = await supabase
      .from('expenses')
      .delete()
      .eq('id', id);
    
    if (deleteError) {
        // If the delete fails, alert the user and refresh the list to get the real state
        alert('Error deleting expense: ' + deleteError.message);
        getExpenses();
    }
  }

  // Run the function when the component first loads
  getExpenses();
</script>

<main>
  <div class="form-section">
    <h2>Add New Expense</h2>
    <ExpenseForm on:expenseAdded={getExpenses} />
  </div>

  <div class="list-section">
    <h2>My Expenses</h2>
    {#if loading}
      <p>Loading your expenses...</p>
    {:else if error}
      <p class="error-message">Error: {error}</p>
    {:else if expenses.length === 0}
      <p>You have no expenses logged yet.</p>
    {:else}
      <ul class="expense-list">
        {#each expenses as expense}
          <li>
            <span class="date">{new Date(expense.date).toLocaleDateString()}</span>
            <span class="description">{expense.description}</span>
            <span class="category">{expense.category}</span>
            <span class="amount">${expense.amount.toFixed(2)}</span>
            <button class="delete-btn" on:click={() => deleteExpense(expense.id)}>
              &times; </button>
          </li>
        {/each}
      </ul>
    {/if}
  </div>
</main>

<style>
  
  main {
    display: grid;
    grid-template-columns: 1fr 1.5fr; /* Create two columns */
    gap: 3rem;
    padding: 2rem;
    max-width: 1200px;
    margin: auto;
  }
  .error-message {
    color: #FF4500; /* OrangeRed */
  }
  .expense-list {
    list-style: none;
    padding: 0;
  }
  .expense-list li {
    display: grid;
    grid-template-columns: 100px 1fr auto auto;
    gap: 1rem;
    padding: 0.75rem 0;
    border-bottom: 1px solid #333;
    align-items: center;
  }
  .amount {
    font-weight: bold;
    text-align: right;
  }

  /* Make it a single column on smaller screens */
  @media (max-width: 900px) {
    main {
      grid-template-columns: 1fr;
    }
  }

    /* ADD STYLES FOR THE DELETE BUTTON */
  .delete-btn {
      background: none;
      border: none;
      color: #777;
      cursor: pointer;
      font-size: 1.5rem;
      line-height: 1;
      padding: 0 0.5rem;
  }
  .delete-btn:hover {
      color: #FF4500; /* OrangeRed */
  }

  /* Adjust the grid to make space for the button */
  .expense-list li {
    display: grid;
    /* Date | Description | Category | Amount | Delete Button */
    grid-template-columns: 100px 1fr auto auto 30px; 
    gap: 1rem;
    padding: 0.75rem 0;
    border-bottom: 1px solid #333;
    align-items: center;
  }
</style>
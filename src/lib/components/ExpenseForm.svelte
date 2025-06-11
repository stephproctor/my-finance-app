<script>
    import { supabase } from '$lib/supabaseClient';
    import { createEventDispatcher } from 'svelte';

    // This allows the component to notify the parent page when an expense is added
    const dispatch = createEventDispatcher();

    // Variables to hold the form data
    let amount = null;
    let description = '';
    let date = new Date().toISOString().slice(0, 10); // Default to today
    let selectedCategory = ''; // Single select for category now for simplicity
    let selectedChargedTo = '';
    
    let loading = false;
    let errorMessage = '';

    // Define our pill options right here in the component
    const categoryOptions = ["Groceries", "Eating Out", "Subscriptions", "Utilities", "Hobbies", "Shopping"];
    const chargedToOptions = ["PC Mastercard", "World Elite Mastercard", "Cashback Mastercard", "Tangerine Debit"];

    async function handleSubmit() {
        if (!amount || !selectedCategory || !selectedChargedTo) {
            alert('Please fill out all fields.');
            return;
        }
        
        loading = true;
        errorMessage = '';

        const { error } = await supabase
            .from('expenses')
            .insert({ 
                amount, 
                description, 
                category: selectedCategory, // Use the single selected category
                date, 
                charged_to: selectedChargedTo 
            });

        if (error) {
            errorMessage = error.message;
            console.error(error);
        } else {
            // Success! Reset the form and notify the parent page
            amount = null;
            description = '';
            selectedCategory = '';
            selectedChargedTo = '';
            dispatch('expenseAdded'); // Send the "expenseAdded" event
        }
        loading = false;
    }
</script>

<form on:submit|preventDefault={handleSubmit}>
    <div class="form-container">
        <div class="form-group">
            <label for="amount">Amount</label>
            <input type="number" id="amount" step="0.01" bind:value={amount} placeholder="0.00" required>
        </div>
        
        <div class="form-group">
            <label for="description">Description</label>
            <input type="text" id="description" bind:value={description}>
        </div>

        <div class="form-group">
            <label>Category</label>
            <div class="pill-container">
                {#each categoryOptions as category}
                    <button 
                        type="button" 
                        class="pill"
                        class:selected={selectedCategory === category}
                        on:click={() => selectedCategory = category}
                    >
                        {category}
                    </button>
                {/each}
            </div>
        </div>

        <div class="form-group">
            <label>Charged To</label>
            <div class="pill-container">
                {#each chargedToOptions as account}
                    <button 
                        type="button" 
                        class="pill"
                        class:selected={selectedChargedTo === account}
                        on:click={() => selectedChargedTo = account}
                    >
                        {account}
                    </button>
                {/each}
            </div>
        </div>
        
        <div class="form-group">
            <label for="date">Date</label>
            <input type="date" id="date" bind:value={date} required>
        </div>
    </div>

    {#if errorMessage}
        <p class="error-message">{errorMessage}</p>
    {/if}

    <button type="submit" class="submit-button" disabled={loading}>
        {#if loading}
            Adding...
        {:else}
            Add Expense
        {/if}
    </button>
</form>

<style>
    /* You have full control to style your form here! */
    .form-container {
        display: grid;
        gap: 1.5rem;
        margin-bottom: 1.5rem;
    }
    .form-group {
        display: flex;
        flex-direction: column;
    }
    label {
        font-size: 0.9rem;
        margin-bottom: 0.5rem;
        color: #aaa;
    }
    input {
        background: #222;
        border: 1px solid #444;
        color: white;
        padding: 0.75rem;
        border-radius: 6px;
        font-size: 1rem;
    }
    .pill-container {
        display: flex;
        flex-wrap: wrap;
        gap: 0.5rem;
    }
    .pill {
        background: #333;
        color: #eee;
        border: 1px solid #555;
        border-radius: 1rem;
        padding: 0.5rem 1rem;
        cursor: pointer;
        transition: all 0.2s;
    }
    .pill:hover {
        background: #444;
    }
    .pill.selected {
        background: #007BFF;
        color: white;
        border-color: #007BFF;
        font-weight: bold;
    }
    .submit-button {
        width: 100%;
        background: #007BFF;
        color: white;
        border: none;
        padding: 1rem;
        border-radius: 6px;
        font-size: 1.1rem;
        font-weight: bold;
        cursor: pointer;
    }
    .submit-button:disabled {
        background: #555;
    }
    .error-message {
        color: #FF4500;
        margin-bottom: 1rem;
    }
</style>
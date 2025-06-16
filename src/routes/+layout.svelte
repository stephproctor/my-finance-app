<script>
    import './styles.css';
</script>

<div class="app-container">
    <nav class="top-nav">
        <a href="/" class="nav-logo">MyFinance</a>
        <div class="nav-links">
            <a href="/">Overview</a>
            <a href="/expenses">Expenses</a>
            <a href="/income">Income</a>
            <a href="/savings">Savings</a>
            <a href="/left-to-spend">Left to Spend</a>
        </div>
    </nav>

    <main class="content">
        <slot />
    </main>
</div>

<style>
    /* All your existing styles for the layout remain the same */
    .app-container {
        display: flex;
        flex-direction: column;
        min-height: 100vh;
        background-color: #000;
        color: #fff;
    }
    .top-nav {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 1rem 2rem;
        border-bottom: 1px solid #333;
    }
    .nav-logo {
        font-weight: 800;
        font-size: 1.5rem;
        color: #b3dc39;
        text-decoration: none;
    }
    .nav-links a {
        color: #ccc;
        text-decoration: none;
        margin-left: 1.5rem;
        transition: color 0.2s;
    }
    .nav-links a:hover {
        color: #fff;
    }
    .content {
        flex-grow: 1;
        padding: 2rem;
    }
</style>
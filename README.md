# Personal Finance Tracker

## Project Overview

The Personal Finance Tracker is a web-based application that helps users manage their finances by tracking income and expenses, calculating balances, and displaying recent transactions. It also fetches and displays live currency exchange rates. The project is implemented as a single HTML file containing HTML, CSS, and JavaScript.

---

## How to Use

1. **Open the File**: Double-click `finance_tracker_project.html` or open it in any modern web browser (such as Chrome, Firefox, or Edge).
2. **Add Transactions**: Fill in the form with a description, amount, type (income or expense), category, and optional notes, then click "Add Transaction".
3. **View Stats**: The app calculates and displays your total income, expenses, and balance.
4. **Delete Transactions**: Click the 🗑️ button next to a transaction to remove it.
5. **Currency Exchange**: Live (or demo) currency exchange rates are shown for reference.

---

## File Structure

- `finance_tracker_project.html` — Main file containing all code (HTML, CSS, JS).
- `README.md` — This documentation file.

---

## Code Explanation (Line by Line)

Below is a detailed explanation of the code, section by section and line by line.

### 1. HTML Structure

```
<!DOCTYPE html> <!-- Declares the document type as HTML5 -->
<html lang="en"> <!-- Start of the HTML document, language is English -->
<head> <!-- Head section contains metadata and styles -->
    <meta charset="UTF-8"> <!-- Sets character encoding to UTF-8 -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0"> <!-- Responsive design for mobile -->
    <title>Personal Finance Tracker</title> <!-- Title shown in browser tab -->
    <style> ... </style> <!-- Embedded CSS for page styling -->
</head>
<body> <!-- Body contains all visible content -->
    <div class="container"> <!-- Main container for the app -->
        <header class="header"> <!-- App header -->
            <h1>💰 Personal Finance Tracker</h1> <!-- App title -->
            <p>Master your money with smart tracking</p> <!-- Subtitle -->
        </header>
        <!-- Stats, Currency, Main Content, and Form sections follow -->
        ...
    </div>
    <script> ... </script> <!-- Embedded JavaScript for app logic -->
</body>
</html>
```

**Explanation:**
- The HTML is structured with a main container, header, statistics section, currency exchange section, and the main content area (form and transaction list).
- All CSS is included in a `<style>` tag in the `<head>`, and all JavaScript is in a `<script>` tag at the end of the `<body>`.

---

### 2. CSS Styling (in `<style>`)

- The CSS resets default browser styles, sets up a modern, responsive layout, and styles all UI elements (container, header, stats, forms, buttons, transaction list, etc).
- Classes like `.container`, `.header`, `.stats`, `.form-section`, `.btn`, `.transaction-item`, etc., are used to organize and style the page.
- Media queries ensure the app looks good on mobile devices.

---

### 3. JavaScript Logic (in `<script>`)

The JavaScript is fully commented in your HTML. Here is a summary and a detailed, line-by-line explanation:

#### **Class: FinanceTracker**

```
class FinanceTracker {
    // The constructor initializes the app
    constructor() {
        this.transactions = this.loadTransactions(); // Load saved or sample transactions
        this.exchangeRates = {}; // Prepare for currency rates
        this.init(); // Set up the app
    }

    // Initializes event listeners and UI
    init() {
        console.log('🔧 Initializing FinanceTracker...');
        this.bindEvents(); // Set up form and button events
        console.log('✅ Events bound successfully');
        this.updateStats(); // Calculate and display income/expense/balance
        console.log('✅ Stats updated');
        this.renderTransactions(); // Show recent transactions
        console.log('✅ Transactions rendered');
        setTimeout(() => {
            this.fetchExchangeRates(); // Fetch live or demo currency rates
        }, 100);
        console.log('✅ FinanceTracker initialization complete');
    }

    // Sets up form submission and delete button events
    bindEvents() {
        console.log('🔗 Binding events...');
        const form = document.getElementById('transactionForm');
        if (form) {
            form.addEventListener('submit', (e) => {
                console.log('📝 Form submitted');
                e.preventDefault(); // Prevent reload
                this.handleFormSubmit(e);
            });
            console.log('✅ Form event listener added');
        } else {
            console.error('❌ Transaction form not found!');
        }
        document.addEventListener('click', (e) => {
            if (e.target.matches('.btn-delete')) {
                console.log('🗑️ Delete button clicked');
                const id = parseInt(e.target.dataset.id);
                this.deleteTransaction(id);
            }
        });
        console.log('✅ Event delegation set up');
    }

    // Handles form submission: validation, adding, and UI update
    async handleFormSubmit(e) {
        try {
            const formData = this.getFormData();
            this.validateTransaction(formData);
            await this.addTransaction(formData);
            this.showSuccess('Transaction added successfully! 🎉');
            this.clearForm();
        } catch (error) {
            this.showError(error.message);
            console.error('Transaction error:', error);
        }
    }

    // Gets form data as an object
    getFormData() {
        const form = document.getElementById('transactionForm');
        return {
            id: Date.now(),
            description: form.description.value.trim(),
            amount: parseFloat(form.amount.value),
            type: form.type.value,
            category: form.category.value || 'other',
            notes: form.notes.value.trim(),
            date: new Date().toISOString()
        };
    }

    // Validates transaction data
    validateTransaction(transaction) {
        const { description, amount, type } = transaction;
        if (!description) throw new Error('Description is required');
        if (!amount || amount <= 0) throw new Error('Amount must be greater than 0');
        if (!type) throw new Error('Transaction type is required');
        if (description.length < 3) throw new Error('Description must be at least 3 characters long');
        if (amount > 1000000) throw new Error('Amount seems too large. Please check.');
    }

    // Adds a transaction and updates the UI
    async addTransaction(transaction) {
        await this.delay(300); // Simulate delay
        this.transactions.unshift(transaction);
        this.saveTransactions();
        this.updateStats();
        this.renderTransactions();
    }

    // Deletes a transaction by ID
    deleteTransaction(id) {
        if (confirm('Are you sure you want to delete this transaction?')) {
            this.transactions = this.transactions.filter(t => t.id !== id);
            this.saveTransactions();
            this.updateStats();
            this.renderTransactions();
            this.showSuccess('Transaction deleted! 🗑️');
        }
    }

    // Updates income, expense, and balance in the UI
    updateStats() {
        const stats = this.transactions.reduce((acc, transaction) => {
            if (transaction.type === 'income') acc.income += transaction.amount;
            else if (transaction.type === 'expense') acc.expense += transaction.amount;
            return acc;
        }, { income: 0, expense: 0 });
        const balance = stats.income - stats.expense;
        document.getElementById('totalIncome').textContent = this.formatCurrency(stats.income);
        document.getElementById('totalExpense').textContent = this.formatCurrency(stats.expense);
        document.getElementById('totalBalance').textContent = this.formatCurrency(balance);
        const balanceElement = document.getElementById('totalBalance');
        balanceElement.className = `stat-value ${balance >= 0 ? 'income' : 'expense'}`;
    }

    // Renders the transaction list
    renderTransactions() {
        const container = document.getElementById('transactionsList');
        if (this.transactions.length === 0) {
            container.innerHTML = `<p style="text-align: center; color: #666; padding: 40px;">No transactions yet. Add your first transaction! 🚀</p>`;
            return;
        }
        const transactionHTML = this.transactions.slice(0, 10).map(transaction => this.createTransactionHTML(transaction)).join('');
        container.innerHTML = transactionHTML;
    }

    // Returns HTML for a single transaction
    createTransactionHTML(transaction) {
        const { id, description, amount, type, category, notes, date } = transaction;
        const formattedDate = new Date(date).toLocaleDateString();
        const categoryEmoji = this.getCategoryEmoji(category);
        return `
            <div class="transaction-item">
                <div class="transaction-details">
                    <h4>${categoryEmoji} ${description}</h4>
                    <p>${formattedDate} • ${category} ${notes ? `• ${notes}` : ''}</p>
                </div>
                <div style="display: flex; align-items: center; gap: 15px;">
                    <div class="transaction-amount ${type}">
                        ${type === 'income' ? '+' : '-'}${this.formatCurrency(amount)}
                    </div>
                    <button class="btn btn-danger btn-delete" data-id="${id}">🗑️</button>
                </div>
            </div>
        `;
    }

    // Returns an emoji for a given category
    getCategoryEmoji(category) {
        const emojiMap = { food: '🍕', transport: '🚗', entertainment: '🎬', bills: '📄', salary: '💼', other: '📦' };
        return emojiMap[category] || '📦';
    }
    // ... (other methods for exchange rates, storage, etc.)
}
```

---

### 4. Additional Features
- **Currency Exchange**: The app fetches live exchange rates from public APIs. If APIs fail, it shows demo data.
- **Local Storage**: Transactions are saved in the browser so your data persists between sessions.
- **Responsive Design**: The layout adjusts for mobile and desktop.
- **Error Handling**: Form validation and error messages help prevent invalid data entry.

---

## Full Line-by-Line Explanation

Your HTML file is now fully commented, especially the JavaScript section, with an explanation for every line and logic block. Please refer to the code for inline comments for every line.

If you need a separate file with only the line-by-line explanation, or want a PDF/Word version, let me know!

---

## Credits

- Developed by [Your Name].
- No external dependencies required; works in any modern browser.

---

## License

This project is open-source and free to use for learning and personal finance management.

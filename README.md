# household_ledger
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mom's Dynamic Current Account Diary</title>
    <style>
        :root {
            --primary: #2563eb;
            --success: #16a34a;
            --danger: #dc2626;
            --bg: #f8fafc;
            --card: #ffffff;
            --text: #1e293b;
            --note-bg: #fef9c3;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 20px;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
        }
        h1 { text-align: center; color: var(--primary); margin-bottom: 5px; }
        p.subtitle { text-align: center; color: #64748b; margin-top: 0; margin-bottom: 25px; }
        
        /* Timeframe Filter Buttons */
        .filter-bar {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 8px;
            margin-bottom: 20px;
        }
        .filter-btn {
            padding: 10px;
            background: #e2e8f0;
            border: none;
            border-radius: 6px;
            font-size: 14px;
            font-weight: 600;
            cursor: pointer;
            color: #475569;
            transition: all 0.2s;
        }
        .filter-btn.active {
            background: var(--primary);
            color: white;
        }

        .grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-bottom: 20px;
        }
        .card {
            background: var(--card);
            padding: 15px;
            border-radius: 12px;
            box-shadow: 0 4px 6px -1px rgba(0,0,0,0.05);
            text-align: center;
            margin-bottom: 20px;
        }
        .card h3 { margin: 0 0 10px 0; font-size: 14px; text-transform: uppercase; color: #64748b; }
        .card p { margin: 0; font-size: 24px; font-weight: bold; }
        .surplus { color: var(--success); }
        .deficit { color: var(--danger); }
        
        form {
            background: var(--card);
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0 4px 6px -1px rgba(0,0,0,0.05);
            margin-bottom: 20px;
        }
        .form-group {
            margin-bottom: 15px;
        }
        label { display: block; margin-bottom: 5px; font-weight: 500; }
        input, select, textarea {
            width: 100%;
            padding: 10px;
            border: 1px solid #cbd5e1;
            border-radius: 6px;
            box-sizing: border-box;
            font-size: 16px;
            font-family: inherit;
        }
        .btn {
            width: 100%;
            padding: 12px;
            color: white;
            border: none;
            border-radius: 6px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
        }
        .btn-primary { background: var(--primary); }
        .btn-primary:hover { background: #1d4ed8; }
        
        .log-list {
            background: var(--card);
            border-radius: 12px;
            padding: 15px;
            box-shadow: 0 4px 6px -1px rgba(0,0,0,0.05);
            margin-bottom: 20px;
        }
        .log-item, .note-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 12px 0;
            border-bottom: 1px solid #e2e8f0;
        }
        .log-item:last-child, .note-item:last-child { border-bottom: none; }
        .item-details { display: flex; flex-direction: column; }
        .item-date { font-size: 12px; color: #94a3b8; margin-top: 2px; }
        .item-name { font-weight: 500; }
        
        .item-actions {
            display: flex;
            align-items: center;
            gap: 8px;
            flex-wrap: wrap;
            justify-content: flex-end;
        }
        .delete-btn {
            background: #fee2e2;
            color: var(--danger);
            border: none;
            border-radius: 4px;
            padding: 6px 10px;
            font-size: 12px;
            font-weight: bold;
            cursor: pointer;
        }
        .edit-btn {
            background: #e0f2fe;
            color: #0284c7;
            border: none;
            border-radius: 4px;
            padding: 6px 10px;
            font-size: 12px;
            font-weight: bold;
            cursor: pointer;
        }
        .buy-btn {
            background: var(--success);
            color: white;
            border: none;
            border-radius: 4px;
            padding: 6px 10px;
            font-size: 12px;
            font-weight: bold;
            cursor: pointer;
        }

        .note-item {
            background: var(--note-bg);
            padding: 12px;
            border-radius: 8px;
            margin-bottom: 10px;
            border-left: 4px solid #eab308;
            border-bottom: none;
        }
        .note-text { white-space: pre-wrap; word-break: break-word; font-size: 14px; font-weight: 500; }
    </style>
</head>
<body>

<div class="container">
    <h1>Mom's Household Ledger 📑</h1>
    <p class="subtitle">Context-Aware Current Account Tracker & Notebook</p>

    <!-- Main Section Tabs -->
    <div class="filter-bar" style="margin-bottom: 25px; grid-template-columns: 1fr 1fr;">
        <button class="filter-btn active" onclick="switchView('ledger', this)">💰 Transactions</button>
        <button class="filter-btn" onclick="switchView('notebook', this)">📝 Notebook & Shopping</button>
    </div>

    <!-- LEDGER VIEW -->
    <div id="ledger-view">
        <!-- Timeframe Filter Bar -->
        <div class="filter-bar">
            <button class="filter-btn active" onclick="setFilter('all', this)">All-Time</button>
            <button class="filter-btn" onclick="setFilter('daily', this)">Daily</button>
            <button class="filter-btn" onclick="setFilter('weekly', this)">Weekly</button>
            <button class="filter-btn" onclick="setFilter('monthly', this)">Monthly</button>
        </div>
        
        <!-- Scorecard Cards -->
        <div class="grid">
            <div class="card" style="margin-bottom: 0;">
                <h3 id="earnings-label">Total Earnings</h3>
                <p id="total-earnings" class="surplus">E0.00</p>
            </div>
            <div class="card" style="margin-bottom: 0;">
                <h3 id="spending-label">Total Spending</h3>
                <p id="total-spending" class="deficit">E0.00</p>
            </div>
        </div>
        
        <div class="card">
            <h3>Net Balance Pool</h3>
            <p id="net-balance">E0.00</p>
        </div>
<!-- Monthly Analytics Insights Card -->
<div class="card" style="background: #eff6ff; border: 1px solid #bfdbfe; text-align: left; padding: 20px;">
    <h3 style="color: var(--primary); margin-bottom: 12px;">📊 This Month's Overview</h3>
    <div style="display: flex; justify-content: space-between; margin-bottom: 8px; font-size: 14px;">
        <span>Income Spent:</span>
        <strong id="analytics-pct-spent">0%</strong>
    </div>
    <div style="display: flex; justify-content: space-between; margin-bottom: 8px; font-size: 14px;">
        <span>Average Daily Spending:</span>
        <strong id="analytics-avg-daily">E0.00</strong>
    </div>
    <div style="display: flex; justify-content: space-between; font-size: 14px;">
        <span>Remaining Income:</span>
        <strong id="analytics-remaining" class="surplus">E0.00</strong>
    </div>
</div>

        <!-- Transaction Form -->
        <form id="ledger-form">
            <h3 style="margin-top:0; color:var(--primary);">Add Financial Entry</h3>
            <div class="form-group">
                <label for="description">Description (e.g., Bread, Gas, Salary)</label>
                <input type="text" id="description" required placeholder="What was bought or earned?">
            </div>
            <div class="form-group">
                <label for="type">Transaction Type</label>
                <select id="type">
                    <option value="expense">Expense (Money Outflow)</option>
                    <option value="income">Income (Money Inflow)</option>
                </select>
            </div>
            <div class="form-group">
                <label for="amount">Amount (SZL / Emalangeni)</label>
                <input type="number" id="amount" step="0.01" required placeholder="0.00">
            </div>
            <div class="form-group">
                <label for="custom-date">Date</label>
                <input type="date" id="custom-date" required>
            </div>
            <div class="form-group">
                <label for="frequency">Budget Scope / Frequency</label>
                <select id="frequency">
                    <option value="daily">Daily / Regular (Counts in Daily, Weekly & Monthly)</option>
                    <option value="monthly">Monthly Only (Salary, Rent — hides from Daily/Weekly)</option>
                </select>
            </div>
            <button type="submit" class="btn btn-primary">Log Transaction</button>
        </form>

        <!-- Historic Ledger Log -->
        <div class="log-list">
            <h3 style="margin-top:0; color: #475569;" id="log-heading">Showing All Records</h3>
            <div id="log-container"></div>
        </div>
    </div>

    <!-- NOTEBOOK VIEW (Hidden by default) -->
    <div id="notebook-view" style="display: none;">
        <!-- Notebook Form -->
        <form id="note-form">
            <h3 style="margin-top:0; color: #ca8a04;">Add Note or Shopping List 📝</h3>
            <div class="form-group">
                <label for="note-content">Note or Shopping Items</label>
                <textarea id="note-content" rows="2" required placeholder="e.g., 2kg Mealie Meal, Bread, Electricity..."></textarea>
            </div>
            <div class="form-group">
                <label for="note-amount">Estimated Price in SZL (Optional — enables Auto-Deduct)</label>
                <input type="number" id="note-amount" step="0.01" placeholder="0.00">
            </div>
            <div class="form-group">
                <label for="note-date">Date for Note</label>
                <input type="date" id="note-date" required>
            </div>
            <button type="submit" class="btn" style="background-color: #ca8a04;">Save Note to Diary</button>
        </form>

        <!-- Historic Notes Log -->
        <div class="log-list">
            <h3 style="margin-top:0; color: #ca8a04;">Shopping Lists & Notes</h3>
            <div id="notes-container"></div>
        </div>
    </div>

    <!-- System Backup Action Deck -->
    <div class="grid" style="margin-top: 20px; margin-bottom: 20px;">
        <button onclick="exportData()" class="btn" style="background-color: #475569; padding: 10px; font-size: 14px;">💾 Download Backup</button>
        <button onclick="document.getElementById('import-file').click()" class="btn" style="background-color: #0f172a; padding: 10px; font-size: 14px;">📂 Restore Backup</button>
        <input type="file" id="import-file" style="display:none;" onchange="importData(this)">
    </div>

</div>

<            
        
<script>
    let transactions = JSON.parse(localStorage.getItem('household_ledger_v2')) || [];
    let notes = JSON.parse(localStorage.getItem('household_notes_v2')) || [];
    let currentFilter = 'all';

    // Grab elements
    const form = document.getElementById('ledger-form');
    const noteForm = document.getElementById('note-form');
    const logContainer = document.getElementById('log-container');
    const notesContainer = document.getElementById('notes-container');
    
    const today = new Date();
    const localISODate = new Date(today.getTime() - (today.getTimezoneOffset() * 60000)).toISOString().split('T')[0];
    document.getElementById('custom-date').value = localISODate;
    document.getElementById('note-date').value = localISODate;

    function switchView(viewName, element) {
        const switchButtons = document.querySelectorAll('.container > .filter-bar .filter-btn');
        switchButtons.forEach(btn => btn.classList.remove('active'));
        element.classList.add('active');

        if (viewName === 'ledger') {
            document.getElementById('ledger-view').style.display = 'block';
            document.getElementById('notebook-view').style.display = 'none';
        } else {
            document.getElementById('ledger-view').style.display = 'none';
            document.getElementById('notebook-view').style.display = 'block';
        }
    }

    // Ledger Form Submit
    form.addEventListener('submit', (e) => {
        e.preventDefault();
        const newTx = {
            id: Date.now(),
            description: document.getElementById('description').value.trim(),
            type: document.getElementById('type').value,
            frequency: document.getElementById('frequency').value,
            amount: parseFloat(document.getElementById('amount').value),
            date: document.getElementById('custom-date').value
        };
        transactions.push(newTx);
        saveAndRender();
        form.reset();
        document.getElementById('custom-date').value = localISODate;
    });

    // Note Form Submit
    noteForm.addEventListener('submit', (e) => {
        e.preventDefault();
        const rawAmount = document.getElementById('note-amount').value;
        const newNote = {
            id: Date.now(),
            content: document.getElementById('note-content').value.trim(),
            date: document.getElementById('note-date').value,
            amount: rawAmount ? parseFloat(rawAmount) : null,
            isLogged: false
        };
        notes.push(newNote);
        saveAndRender();
        noteForm.reset();
        document.getElementById('note-date').value = localISODate;
    });

    // Convert Note to Expense (Auto-Deduct)
    function convertNoteToExpense(id) {
        const note = notes.find(n => n.id === id);
        if (!note || !note.amount || note.isLogged) return;

        const newTx = {
            id: Date.now(),
            description: `Bought: ${note.content}`,
            type: 'expense',
            frequency: 'daily',
            amount: note.amount,
            date: localISODate
        };
        transactions.push(newTx);
        note.isLogged = true;
        saveAndRender();
    }

    function deleteTransaction(id) {
        if (confirm('Delete this transaction?')) {
            transactions = transactions.filter(tx => tx.id !== id);
            saveAndRender();
        }
    }

    function deleteNote(id) {
        if (confirm('Delete this note?')) {
            notes = notes.filter(note => note.id !== id);
            saveAndRender();
        }
    }

    function editNote(id) {
        const note = notes.find(n => n.id === id);
        if (!note) return;

        const updatedContent = prompt("Edit your note or shopping item:", note.content);
        if (updatedContent !== null && updatedContent.trim() !== "") {
            note.content = updatedContent.trim();
            
            const currentPrice = note.amount ? note.amount.toString() : "";
            const updatedPrice = prompt("Edit estimated price in SZL (leave blank for none):", currentPrice);
            if (updatedPrice !== null) {
                note.amount = updatedPrice.trim() !== "" ? parseFloat(updatedPrice) : null;
            }
            saveAndRender();
        }
    }

    function saveAndRender() {
        localStorage.setItem('household_ledger_v2', JSON.stringify(transactions));
        localStorage.setItem('household_notes_v2', JSON.stringify(notes));
        updateApp();
    }

    function setFilter(filterType, element) {
        const filterButtons = document.querySelectorAll('#ledger-view > .filter-bar .filter-btn');
        filterButtons.forEach(btn => btn.classList.remove('active'));
        element.classList.add('active');
        currentFilter = filterType;
        updateApp();
    }

    function updateApp() {
        let totalEarnings = 0;
        let totalSpending = 0;
        logContainer.innerHTML = '';
        notesContainer.innerHTML = '';

        const now = new Date();
              // Calculate current month's analytics
        let monthIncome = 0;
        let monthSpending = 0;
        let daysInMonthCount = new Date(now.getFullYear(), now.getMonth() + 1, 0).getDate();
        
        transactions.forEach((tx) => {
            const txDate = new Date(tx.date + 'T00:00:00');
            if (txDate.getMonth() === now.getMonth() && txDate.getFullYear() === now.getFullYear()) {
                if (tx.type === 'income') monthIncome += tx.amount;
                else monthSpending += tx.amount;
            }
        });

        const pctSpent = monthIncome > 0 ? Math.min(100, Math.round((monthSpending / monthIncome) * 100)) : 0;
        const avgDaily = monthSpending / daysInMonthCount;
        const remaining = monthIncome - monthSpending;

        document.getElementById('analytics-pct-spent').textContent = `${pctSpent}%`;
        document.getElementById('analytics-avg-daily').textContent = `E${avgDaily.toFixed(2)}`;
        const remEl = document.getElementById('analytics-remaining');
        remEl.textContent = `${remaining < 0 ? '-' : ''}E${Math.abs(remaining).toFixed(2)}`;
        remEl.className = remaining >= 0 ? 'surplus' : 'deficit';

        const contextMap = { 'all': 'Total', 'daily': 'Daily', 'weekly': 'Weekly', 'monthly': 'Monthly' };
        document.getElementById('earnings-label').innerText = `${contextMap[currentFilter]} Earnings`;
        document.getElementById('spending-label').innerText = `${contextMap[currentFilter]} Spending`;
        document.getElementById('log-heading').innerText = `Showing ${contextMap[currentFilter]} Records`;

        // Render Notes
        notes.sort((a, b) => new Date(b.date + 'T00:00:00') - new Date(a.date + 'T00:00:00'));
        notes.forEach((note) => {
            const noteDate = new Date(note.date + 'T00:00:00');
            const noteItem = document.createElement('div');
            noteItem.className = 'note-item';

            const detailsDiv = document.createElement('div');
            detailsDiv.className = 'item-details';

            const textSpan = document.createElement('div');
            textSpan.className = 'note-text';
            textSpan.textContent = note.content;
            if (note.isLogged) {
                textSpan.style.textDecoration = 'line-through';
                textSpan.style.color = '#64748b';
            }

            const dateSpan = document.createElement('span');
            dateSpan.className = 'item-date';
            dateSpan.textContent = `📅 ${noteDate.toLocaleDateString(undefined, { month: 'short', day: 'numeric', year: 'numeric' })}`;

            detailsDiv.appendChild(textSpan);
            detailsDiv.appendChild(dateSpan);

            const actionsDiv = document.createElement('div');
            actionsDiv.className = 'item-actions';

            if (note.amount && !note.isLogged) {
                const logBtn = document.createElement('button');
                logBtn.className = 'buy-btn';
                logBtn.textContent = `✅ Buy & Log E${note.amount.toFixed(2)}`;
                logBtn.onclick = () => convertNoteToExpense(note.id);
                actionsDiv.appendChild(logBtn);
            } else if (note.isLogged) {
                const statusSpan = document.createElement('span');
                statusSpan.style.fontSize = '12px';
                statusSpan.style.fontWeight = 'bold';
                statusSpan.style.color = '#16a34a';
                statusSpan.textContent = '✔️ Deducted';
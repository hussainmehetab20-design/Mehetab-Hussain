<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>ExpenseRun - Quick Expense Logger</title>
  
  <meta name="theme-color" content="#059669">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="default">
  <meta name="apple-mobile-web-app-title" content="ExpenseRun">
  <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>🏃</text></svg>">
  <link rel="apple-touch-icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><rect width='100' height='100' rx='20' fill='%23059669'/><text x='50' y='70' font-size='60' text-anchor='middle'>🏃</text></svg>">
  <link rel="manifest" href="data:application/manifest+json,%7B%22name%22%3A%22ExpenseRun%20Logger%22%2C%22short_name%22%3A%22ExpenseRun%22%2C%22start_url%22%3A%22.%22%2C%22display%22%3A%22standalone%22%2C%22background_color%22%3A%22%23f8fafc%22%2C%22theme_color%22%3A%22%23059669%22%7D">

  <script src="https://cdn.tailwindcss.com/3.4.17"></script>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
  <style>
    body { font-family: 'Inter', sans-serif; -webkit-tap-highlight-color: transparent; }
    .no-scrollbar::-webkit-scrollbar { display: none; }
    .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }

    @keyframes roadMove {
      from { background-position: 0 0; }
      to { background-position: -40px 0; }
    }
    .road-stripes {
      background: repeating-linear-gradient(
        90deg,
        #f8fafc 0px,
        #f8fafc 16px,
        transparent 16px,
        transparent 32px
      );
    }
    .road-moving {
      animation: roadMove 0.8s linear infinite;
    }

    @keyframes runnerBounce {
      0%, 100% { transform: translateY(0) rotate(0deg); }
      50% { transform: translateY(-4px) rotate(4deg); }
    }
    .animate-runner {
      animation: runnerBounce 0.35s infinite ease-in-out;
    }

    @keyframes floatUp {
      0% { opacity: 0; transform: translateY(6px) scale(0.8); }
      30% { opacity: 1; transform: translateY(-6px) scale(1.1); }
      100% { opacity: 0; transform: translateY(-24px) scale(0.9); }
    }
    .float-coin {
      animation: floatUp 1.2s ease-out forwards;
    }
  </style>
</head>
<body class="bg-slate-50 min-h-screen text-slate-800 p-4 sm:p-8 flex justify-center items-start">
  <main class="w-full max-w-md bg-white rounded-2xl shadow-sm border border-slate-200 p-6 space-y-6">
    <header class="flex justify-between items-start">
      <div class="space-y-1">
        <div class="flex items-center space-x-2">
          <h1 class="text-2xl font-bold tracking-tight text-slate-900">ExpenseRun</h1>
          <span class="text-[10px] uppercase font-bold tracking-wider px-2 py-0.5 rounded bg-emerald-100 text-emerald-800 border border-emerald-200">App</span>
        </div>
        <p class="text-sm text-slate-500">Track your spending & beat the budget</p>
      </div>
      <div class="flex items-center space-x-1.5">
        <button 
          type="button" 
          id="sound-toggle-btn"
          class="p-1.5 bg-slate-100 hover:bg-slate-200 text-slate-600 rounded-lg border border-slate-200 text-xs font-medium transition-colors"
          title="Toggle Sound Effects"
          aria-label="Toggle sound"
        >
          <span id="sound-icon">🔊</span>
        </button>
        <div class="flex items-center space-x-1 bg-slate-100 p-1 rounded-lg border border-slate-200 text-xs font-semibold">
          <button type="button" data-currency="₹" class="currency-btn px-2 py-0.5 rounded bg-white text-emerald-700 shadow-sm transition-colors">₹</button>
          <button type="button" data-currency="$" class="currency-btn px-2 py-0.5 rounded text-slate-600 hover:text-slate-900 transition-colors">$</button>
          <button type="button" data-currency="€" class="currency-btn px-2 py-0.5 rounded text-slate-600 hover:text-slate-900 transition-colors">€</button>
        </div>
      </div>
    </header>

    <section class="bg-gradient-to-b from-sky-50 to-slate-100 border border-slate-200 rounded-xl p-4 space-y-3 relative overflow-hidden">
      <div class="flex justify-between items-center text-sm z-10 relative">
        <div>
          <span class="text-xs font-semibold uppercase tracking-wider text-slate-500">Spent / Budget</span>
          <div class="flex items-baseline space-x-1.5 mt-0.5">
            <span id="total-amount" class="text-2xl font-bold text-slate-900">₹0.00</span>
            <span class="text-slate-400 text-xs">of</span>
            <span id="budget-display" class="text-xs font-semibold text-slate-600">₹2000.00</span>
          </div>
        </div>

        <div class="flex items-center space-x-1.5">
          <label for="budget-input" class="text-xs text-slate-500 font-medium">Limit:</label>
          <input 
            type="number" 
            id="budget-input" 
            value="2000" 
            min="10" 
            step="100" 
            class="w-20 px-2 py-1 bg-white border border-slate-300 rounded text-xs text-right font-medium focus:outline-none focus:ring-2 focus:ring-emerald-500"
          />
        </div>
      </div>

      <div class="relative w-full h-20 bg-slate-700 rounded-lg overflow-hidden border border-slate-600 shadow-inner flex flex-col justify-end" id="street-track">
        <div class="absolute top-0 left-0 right-0 h-4 bg-emerald-800/40 border-b border-emerald-600/30 flex justify-between px-3 text-[10px] text-emerald-200 font-mono">
          <span>🏁 START (0%)</span>
          <span id="finish-label">🛑 BUDGET LIMIT</span>
        </div>

        <div id="road-lines" class="absolute top-10 left-0 right-0 h-1 road-stripes opacity-75"></div>
        <div class="absolute right-3 top-4 bottom-0 w-1 bg-rose-400 border-r border-dashed border-white opacity-80 z-0"></div>

        <div 
          id="runner" 
          class="absolute bottom-2 z-10 transition-all duration-700 ease-out flex flex-col items-center select-none"
          style="left: 8px;"
        >
          <div id="coin-popup" class="text-xs font-bold text-amber-300 drop-shadow hidden"></div>
          <div id="runner-sprite" class="text-2xl animate-runner transition-transform">
            🧍
          </div>
          <span id="runner-bubble" class="text-[9px] font-bold text-white bg-slate-900/80 px-1.5 py-0.5 rounded shadow">
            0%
          </span>
        </div>
      </div>

      <div class="w-full bg-slate-200 rounded-full h-2 overflow-hidden">
        <div 
          id="budget-progress-bar" 
          class="h-2 rounded-full transition-all duration-300 bg-emerald-500" 
          style="width: 0%"
        ></div>
      </div>

      <div class="flex justify-between items-center text-xs font-medium" id="budget-status-row">
        <span id="budget-status-text" class="text-emerald-700">₹2000.00 remaining</span>
        <span id="budget-percent-text" class="text-slate-500">0% used</span>
      </div>

      <div id="breakdown-container" class="pt-2 border-t border-slate-200/80 space-y-1.5 hidden">
        <div class="flex justify-between items-center text-[11px] text-slate-500 font-medium">
          <span>Spending Breakdown</span>
          <span id="breakdown-subtitle" class="text-slate-400">by category</span>
        </div>

        <div id="breakdown-bar" class="w-full h-2 rounded-full overflow-hidden flex bg-slate-200"></div>
        <div id="breakdown-legend" class="flex flex-wrap gap-1.5 pt-1 text-xs"></div>
      </div>
    </section>

    <section class="space-y-1.5">
      <div class="flex justify-between items-center text-xs">
        <span class="font-semibold uppercase tracking-wider text-slate-500">⚡ 1-Tap Quick Log</span>
        <span class="text-slate-400 text-[11px]">instant run</span>
      </div>
      <div class="grid grid-cols-4 gap-2" id="presets-container">
        <button 
          type="button" 
          class="preset-btn p-2 bg-amber-50 hover:bg-amber-100 border border-amber-200 rounded-lg text-center transition-transform active:scale-95"
          data-desc="Chai" 
          data-amount="20" 
          data-cat="Food"
        >
          <div class="text-base">☕</div>
          <div class="text-xs font-semibold text-amber-900 mt-0.5">Chai</div>
          <div class="text-[10px] text-amber-700 font-medium preset-price"><span class="currency-label">₹</span>20</div>
        </button>

        <button 
          type="button" 
          class="preset-btn p-2 bg-blue-50 hover:bg-blue-100 border border-blue-200 rounded-lg text-center transition-transform active:scale-95"
          data-desc="Auto / Metro" 
          data-amount="50" 
          data-cat="Transport"
        >
          <div class="text-base">🛺</div>
          <div class="text-xs font-semibold text-blue-900 mt-0.5">Ride</div>
          <div class="text-[10px] text-blue-700 font-medium preset-price"><span class="currency-label">₹</span>50</div>
        </button>

        <button 
          type="button" 
          class="preset-btn p-2 bg-emerald-50 hover:bg-emerald-100 border border-emerald-200 rounded-lg text-center transition-transform active:scale-95"
          data-desc="Lunch" 
          data-amount="150" 
          data-cat="Food"
        >
          <div class="text-base">🍛</div>
          <div class="text-xs font-semibold text-emerald-900 mt-0.5">Lunch</div>
          <div class="text-[10px] text-emerald-700 font-medium preset-price"><span class="currency-label">₹</span>150</div>
        </button>

        <button 
          type="button" 
          class="preset-btn p-2 bg-pink-50 hover:bg-pink-100 border border-pink-200 rounded-lg text-center transition-transform active:scale-95"
          data-desc="Groceries" 
          data-amount="300" 
          data-cat="Shopping"
        >
          <div class="text-base">🥦</div>
          <div class="text-xs font-semibold text-pink-900 mt-0.5">Market</div>
          <div class="text-[10px] text-pink-700 font-medium preset-price"><span class="currency-label">₹</span>300</div>
        </button>
      </div>
    </section>

    <form id="expense-form" class="space-y-4 pt-1">
      <div>
        <label for="desc-input" class="block text-xs font-semibold uppercase tracking-wider text-slate-600 mb-1">Custom Expense Name</label>
        <input 
          type="text" 
          id="desc-input" 
          placeholder="e.g. Dinner with friends, Movie ticket" 
          required 
          class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500"
        />
      </div>

      <div class="grid grid-cols-2 gap-3">
        <div>
          <label for="amount-input" class="block text-xs font-semibold uppercase tracking-wider text-slate-600 mb-1">
            Amount (<span class="currency-label">₹</span>)
          </label>
          <input 
            type="number" 
            id="amount-input" 
            step="0.01" 
            min="0.01" 
            placeholder="0.00" 
            required 
            class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500"
          />
        </div>

        <div>
          <label for="category-select" class="block text-xs font-semibold uppercase tracking-wider text-slate-600 mb-1">Category</label>
          <select 
            id="category-select" 
            class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm bg-white focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500"
          >
            <option value="Food">🍔 Food</option>
            <option value="Transport">🚗 Transport</option>
            <option value="Bills">⚡ Bills</option>
            <option value="Shopping">🛍️ Shopping</option>
            <option value="Other">📦 Other</option>
          </select>
        </div>
      </div>

      <button 
        type="submit" 
        id="add-btn"
        class="w-full py-2.5 px-4 bg-emerald-600 hover:bg-emerald-700 active:bg-emerald-800 text-white font-medium text-sm rounded-lg transition-colors shadow-sm"
      >
        Add Expense
      </button>
    </form>

    <section class="space-y-3 pt-2">
      <div class="flex justify-between items-center">
        <h2 class="text-sm font-semibold uppercase tracking-wider text-slate-600">Expenses</h2>
        <div class="flex items-center space-x-2">
          <span id="expense-count" class="text-xs text-slate-400">0 items</span>
          <button 
            type="button" 
            id="export-btn"
            class="inline-flex items-center gap-1 text-xs font-medium text-slate-600 hover:text-emerald-700 px-2 py-1 rounded bg-slate-100 hover:bg-emerald-50 transition-colors"
            title="Export expenses to CSV"
          >
            <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
            </svg>
            Export CSV
          </button>
        </div>
      </div>

      <nav class="flex space-x-1.5 overflow-x-auto no-scrollbar pb-1 text-xs" aria-label="Category filter" id="filter-container">
        <button type="button" data-filter="All" class="filter-pill px-2.5 py-1 rounded-full font-medium transition-colors bg-emerald-600 text-white">All</button>
        <button type="button" data-filter="Food" class="filter-pill px-2.5 py-1 rounded-full font-medium transition-colors bg-slate-100 text-slate-600 hover:bg-slate-200">Food</button>
        <button type="button" data-filter="Transport" class="filter-pill px-2.5 py-1 rounded-full font-medium transition-colors bg-slate-100 text-slate-600 hover:bg-slate-200">Transport</button>
        <button type="button" data-filter="Bills" class="filter-pill px-2.5 py-1 rounded-full font-medium transition-colors bg-slate-100 text-slate-600 hover:bg-slate-200">Bills</button>
        <button type="button" data-filter="Shopping" class="filter-pill px-2.5 py-1 rounded-full font-medium transition-colors bg-slate-100 text-slate-600 hover:bg-slate-200">Shopping</button>
        <button type="button" data-filter="Other" class="filter-pill px-2.5 py-1 rounded-full font-medium transition-colors bg-slate-100 text-slate-600 hover:bg-slate-200">Other</button>
      </nav>

      <div id="empty-state" class="text-center py-6 border border-dashed border-slate-200 rounded-xl text-slate-400 text-sm">
        No expenses found in this category.
      </div>

      <ul id="expense-list" class="space-y-2 hidden"></ul>
    </section>
  </main>

  <script>
    const EXPENSES_STORAGE_KEY = 'quick_expenses_v2';
    const BUDGET_STORAGE_KEY = 'quick_budget_limit_v2';
    const CURRENCY_STORAGE_KEY = 'quick_currency_v2';
    const SOUND_STORAGE_KEY = 'quick_sound_v2';

    function loadExpenses() {
      try {
        const raw = localStorage.getItem(EXPENSES_STORAGE_KEY);
        return raw ? JSON.parse(raw) : [];
      } catch (err) {
        return [];
      }
    }

    function saveExpenses() {
      try {
        localStorage.setItem(EXPENSES_STORAGE_KEY, JSON.stringify(expenses));
      } catch (err) {
        console.warn('Storage save failed:', err);
      }
    }

    function loadBudget() {
      try {
        const raw = localStorage.getItem(BUDGET_STORAGE_KEY);
        return raw ? parseFloat(raw) : 2000;
      } catch (err) {
        return 2000;
      }
    }

    function saveBudget(value) {
      try {
        localStorage.setItem(BUDGET_STORAGE_KEY, value.toString());
      } catch (err) {
        console.warn('Budget save failed:', err);
      }
    }

    function loadCurrency() {
      try {
        return localStorage.getItem(CURRENCY_STORAGE_KEY) || '₹';
      } catch (err) {
        return '₹';
      }
    }

    function saveCurrency(sym) {
      try {
        localStorage.setItem(CURRENCY_STORAGE_KEY, sym);
      } catch (err) {
        console.warn('Currency save failed:', err);
      }
    }

    function loadSoundSetting() {
      try {
        return localStorage.getItem(SOUND_STORAGE_KEY) !== 'false';
      } catch (err) {
        return true;
      }
    }

    function saveSoundSetting(enabled) {
      try {
        localStorage.setItem(SOUND_STORAGE_KEY, enabled.toString());
      } catch (err) {
        console.warn('Sound save failed:', err);
      }
    }

    let expenses = loadExpenses();
    let budgetLimit = loadBudget();
    let currencySymbol = loadCurrency();
    let soundEnabled = loadSoundSetting();
    let currentFilter = 'All';

    let audioCtx = null;

    function getAudioContext() {
      if (!audioCtx) {
        const AudioContextClass = window.AudioContext || window.webkitAudioContext;
        if (AudioContextClass) {
          audioCtx = new AudioContextClass();
        }
      }
      if (audioCtx && audioCtx.state === 'suspended') {
        audioCtx.resume();
      }
      return audioCtx;
    }

    function playCoinSound() {
      if (!soundEnabled) return;
      const ctx = getAudioContext();
      if (!ctx) return;

      const now = ctx.currentTime;
      const osc = ctx.createOscillator();
      const gain = ctx.createGain();

      osc.type = 'sine';
      osc.frequency.setValueAtTime(987.77, now);
      osc.frequency.setValueAtTime(1318.51, now + 0.08);

      gain.gain.setValueAtTime(0.15, now);
      gain.gain.exponentialRampToValueAtTime(0.001, now + 0.35);

      osc.connect(gain);
      gain.connect(ctx.destination);

      osc.start(now);
      osc.stop(now + 0.35);
    }

    function playWarningBuzzer() {
      if (!soundEnabled) return;
      const ctx = getAudioContext();
      if (!ctx) return;

      const now = ctx.currentTime;
      const osc = ctx.createOscillator();
      const gain = ctx.createGain();

      osc.type = 'sawtooth';
      osc.frequency.setValueAtTime(180, now);
      osc.frequency.linearRampToValueAtTime(110, now + 0.3);

      gain.gain.setValueAtTime(0.18, now);
      gain.gain.exponentialRampToValueAtTime(0.001, now + 0.32);

      osc.connect(gain);
      gain.connect(ctx.destination);

      osc.start(now);
      osc.stop(now + 0.32);
    }

    function playDeleteSound() {
      if (!soundEnabled) return;
      const ctx = getAudioContext();
      if (!ctx) return;

      const now = ctx.currentTime;
      const osc = ctx.createOscillator();
      const gain = ctx.createGain();

      osc.type = 'triangle';
      osc.frequency.setValueAtTime(260, now);
      osc.frequency.exponentialRampToValueAtTime(80, now + 0.15);

      gain.gain.setValueAtTime(0.12, now);
      gain.gain.exponentialRampToValueAtTime(0.001, now + 0.15);

      osc.connect(gain);
      gain.connect(ctx.destination);

      osc.start(now);
      osc.stop(now + 0.15);
    }

    const categoryConfig = {
      Food: {
        badge: 'bg-amber-50 text-amber-700 border-amber-200',
        barColor: 'bg-amber-500',
        dotColor: 'bg-amber-500',
        label: 'Food'
      },
      Transport: {
        badge: 'bg-blue-50 text-blue-700 border-blue-200',
        barColor: 'bg-blue-500',
        dotColor: 'bg-blue-500',
        label: 'Transport'
      },
      Bills: {
        badge: 'bg-purple-50 text-purple-700 border-purple-200',
        barColor: 'bg-purple-500',
        dotColor: 'bg-purple-500',
        label: 'Bills'
      },
      Shopping: {
        badge: 'bg-pink-50 text-pink-700 border-pink-200',
        barColor: 'bg-pink-500',
        dotColor: 'bg-pink-500',
        label: 'Shopping'
      },
      Other: {
        badge: 'bg-slate-100 text-slate-700 border-slate-200',
        barColor: 'bg-slate-500',
        dotColor: 'bg-slate-500',
        label: 'Other'
      }
    };

    const form = document.getElementById('expense-form');
    const descInput = document.getElementById('desc-input');
    const amountInput = document.getElementById('amount-input');
    const categorySelect = document.getElementById('category-select');
    const totalAmount = document.getElementById('total-amount');
    const budgetDisplay = document.getElementById('budget-display');
    const budgetInput = document.getElementById('budget-input');
    const budgetProgressBar = document.getElementById('budget-progress-bar');
    const budgetStatusText = document.getElementById('budget-status-text');
    const budgetPercentText = document.getElementById('budget-percent-text');
    const runner = document.getElementById('runner');
    const runnerSprite = document.getElementById('runner-sprite');
    const runnerBubble = document.getElementById('runner-bubble');
    const roadLines = document.getElementById('road-lines');
    const coinPopup = document.getElementById('coin-popup');
    const breakdownContainer = document.getElementById('breakdown-container');
    const breakdownBar = document.getElementById('breakdown-bar');
    const breakdownLegend = document.getElementById('breakdown-legend');
    const expenseList = document.getElementById('expense-list');
    const emptyState = document.getElementById('empty-state');
    const expenseCount = document.getElementById('expense-count');
    const filterContainer = document.getElementById('filter-container');
    const exportBtn = document.getElementById('export-btn');
    const soundToggleBtn = document.getElementById('sound-toggle-btn');
    const soundIcon = document.getElementById('sound-icon');
    const presetsContainer = document.getElementById('presets-container');

    budgetInput.value = budgetLimit;
    updateCurrencyUI();
    updateSoundUI();

    soundToggleBtn.addEventListener('click', function() {
      soundEnabled = !soundEnabled;
      saveSoundSetting(soundEnabled);
      updateSoundUI();
      if (soundEnabled) {
        playCoinSound();
      }
    });

    function updateSoundUI() {
      soundIcon.textContent = soundEnabled ? '🔊' : '🔇';
      soundToggleBtn.title = soundEnabled ? 'Sound effects ON (click to mute)' : 'Sound effects OFF (click to unmute)';
    }

    document.querySelectorAll('.currency-btn').forEach(btn => {
      btn.addEventListener('click', function() {
        currencySymbol = this.dataset.currency;
        saveCurrency(currencySymbol);
        updateCurrencyUI();
        render();
      });
    });

    function updateCurrencyUI() {
      document.querySelectorAll('.currency-label').forEach(el => el.textContent = currencySymbol);
      document.querySelectorAll('.currency-btn').forEach(btn => {
        if (btn.dataset.currency === currencySymbol) {
          btn.className = 'currency-btn px-2 py-0.5 rounded bg-white text-emerald-700 shadow-sm transition-colors font-bold';
        } else {
          btn.className = 'currency-btn px-2 py-0.5 rounded text-slate-600 hover:text-slate-900 transition-colors font-medium';
        }
      });
    }

    budgetInput.addEventListener('input', function() {
      const val = parseFloat(budgetInput.value);
      if (!isNaN(val) && val > 0) {
        budgetLimit = val;
        saveBudget(budgetLimit);
        render();
      }
    });

    function triggerRunBurst(amount) {
      roadLines.classList.add('road-moving');

      coinPopup.textContent = `+${currencySymbol}${amount.toFixed(2)}`;
      coinPopup.className = 'text-xs font-bold text-amber-300 drop-shadow float-coin';
      coinPopup.classList.remove('hidden');

      setTimeout(() => {
        roadLines.classList.remove('road-moving');
        coinPopup.classList.add('hidden');
      }, 1200);
    }

    function logNewExpense(description, amount, category) {
      const previousTotal = expenses.reduce((sum, item) => sum + item.amount, 0);
      const newTotal = previousTotal + amount;

      expenses.push({
        id: Date.now(),
        description: description,
        amount: amount,
        category: category
      });

      saveExpenses();
      triggerRunBurst(amount);

      if (newTotal > budgetLimit && previousTotal <= budgetLimit) {
        playWarningBuzzer();
      } else {
        playCoinSound();
      }

      render();
    }

    presetsContainer.addEventListener('click', function(e) {
      const btn = e.target.closest('.preset-btn');
      if (!btn) return;

      const desc = btn.dataset.desc;
      const amount = parseFloat(btn.dataset.amount);
      const category = btn.dataset.cat;

      if (desc && !isNaN(amount)) {
        logNewExpense(desc, amount, category);
      }
    });

    form.addEventListener('submit', function(e) {
      e.preventDefault();

      const description = descInput.value.trim();
      const amount = parseFloat(amountInput.value);
      const category = categorySelect.value;

      if (!description || isNaN(amount) || amount <= 0) return;

      logNewExpense(description, amount, category);

      descInput.value = '';
      amountInput.value = '';
      categorySelect.value = 'Food';
      descInput.focus();
    });

    expenseList.addEventListener('click', function(e) {
      const deleteBtn = e.target.closest('.delete-btn');
      if (!deleteBtn) return;

      const idToDelete = Number(deleteBtn.dataset.id);
      expenses = expenses.filter(item => item.id !== idToDelete);

      saveExpenses();
      playDeleteSound();
      render();
    });

    filterContainer.addEventListener('click', function(e) {
      const pill = e.target.closest('.filter-pill');
      if (!pill) return;

      currentFilter = pill.dataset.filter;
      const pills = filterContainer.querySelectorAll('.filter-pill');
      pills.forEach(p => {
        if (p.dataset.filter === currentFilter) {
          p.className = 'filter-pill px-2.5 py-1 rounded-full font-medium transition-colors bg-emerald-600 text-white';
        } else {
          p.className = 'filter-pill px-2.5 py-1 rounded-full font-medium transition-colors bg-slate-100 text-slate-600 hover:bg-slate-200';
        }
      });

      render();
    });

    exportBtn.addEventListener('click', function() {
      if (expenses.length === 0) return;

      const rows = [
        ['Date', 'Description', 'Category', `Amount (${currencySymbol})`],
        ...expenses.map(item => [
          new Date(item.id).toLocaleDateString(),
          `"${item.description.replace(/"/g, '""')}"`,
          item.category,
          item.amount.toFixed(2)
        ])
      ];

      const csvContent = rows.map(r => r.join(',')).join('\n');
      const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
      const url = URL.createObjectURL(blob);
      const link = document.createElement('a');
      link.href = url;
      link.download = `expenses_${new Date().toISOString().slice(0, 10)}.csv`;
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
      URL.revokeObjectURL(url);
    });

    function render() {
      const total = expenses.reduce((sum, item) => sum + item.amount, 0);
      totalAmount.textContent = `${currencySymbol}${total.toFixed(2)}`;
      budgetDisplay.textContent = `${currencySymbol}${budgetLimit.toFixed(2)}`;

      const percentage = (total / budgetLimit) * 100;
      const cappedWidth = Math.min(percentage, 100);
      budgetProgressBar.style.width = `${cappedWidth}%`;
      budgetPercentText.textContent = `${Math.round(percentage)}% used`;

      const clampedVisualPercent = Math.min(Math.max(percentage, 0), 100);
      runner.style.left = `calc(${clampedVisualPercent}% * 0.84 + 8px)`;
      runnerBubble.textContent = `${Math.round(percentage)}%`;

      budgetProgressBar.className = 'h-2 rounded-full transition-all duration-300';
      if (percentage >= 100) {
        runnerSprite.textContent = '🥵';
        budgetProgressBar.classList.add('bg-rose-500');
        runnerBubble.className = 'text-[9px] font-bold text-white bg-rose-600 px-1.5 py-0.5 rounded shadow';
        const overBudget = total - budgetLimit;
        budgetStatusText.textContent = `Over budget by ${currencySymbol}${overBudget.toFixed(2)}!`;
        budgetStatusText.className = 'text-rose-600 font-semibold';
      } else if (percentage >= 75) {
        runnerSprite.textContent = '🏃💦';
        budgetProgressBar.classList.add('bg-amber-500');
        runnerBubble.className = 'text-[9px] font-bold text-white bg-amber-600 px-1.5 py-0.5 rounded shadow';
        const remaining = budgetLimit - total;
        budgetStatusText.textContent = `${currencySymbol}${remaining.toFixed(2)} remaining`;
        budgetStatusText.className = 'text-amber-700';
      } else if (percentage > 0) {
        runnerSprite.textContent = '🏃';
        budgetProgressBar.classList.add('bg-emerald-500');
        runnerBubble.className = 'text-[9px] font-bold text-white bg-emerald-700 px-1.5 py-0.5 rounded shadow';
        const remaining = budgetLimit - total;
        budgetStatusText.textContent = `${currencySymbol}${remaining.toFixed(2)} remaining`;
        budgetStatusText.className = 'text-emerald-700';
      } else {
        runnerSprite.textContent = '🧍';
        budgetProgressBar.classList.add('bg-slate-300');
        runnerBubble.className = 'text-[9px] font-bold text-white bg-slate-800 px-1.5 py-0.5 rounded shadow';
        budgetStatusText.textContent = `${currencySymbol}${budgetLimit.toFixed(2)} remaining`;
        budgetStatusText.className = 'text-slate-600';
      }

      if (total > 0) {
        breakdownContainer.classList.remove('hidden');
        const totalsByCategory = {};
        expenses.forEach(item => {
          totalsByCategory[item.category] = (totalsByCategory[item.category] || 0) + item.amount;
        });

        breakdownBar.innerHTML = '';
        breakdownLegend.innerHTML = '';

        Object.keys(totalsByCategory).forEach(cat => {
          const catTotal = totalsByCategory[cat];
          const pct = ((catTotal / total) * 100).toFixed(1);
          const config = categoryConfig[cat] || categoryConfig.Other;

          const segment = document.createElement('div');
          segment.className = `${config.barColor} h-full transition-all duration-300`;
          segment.style.width = `${pct}%`;
          segment.title = `${cat}: ${currencySymbol}${catTotal.toFixed(2)} (${pct}%)`;
          breakdownBar.appendChild(segment);

          const chip = document.createElement('div');
          chip.className = 'flex items-center space-x-1 bg-slate-100 px-2 py-0.5 rounded text-[11px] text-slate-700';
          chip.innerHTML = `
            <span class="w-1.5 h-1.5 rounded-full ${config.dotColor}"></span>
            <span class="font-medium">${cat}</span>
            <span class="text-slate-500 font-semibold">${currencySymbol}${catTotal.toFixed(0)} (${Math.round(pct)}%)</span>
          `;
          breakdownLegend.appendChild(chip);
        });
      } else {
        breakdownContainer.classList.add('hidden');
      }

      const visibleExpenses = currentFilter === 'All' 
        ? expenses 
        : expenses.filter(item => item.category === currentFilter);

      expenseCount.textContent = `${visibleExpenses.length} ${visibleExpenses.length === 1 ? 'item' : 'items'}`;
      if (expenses.length === 0) {
        exportBtn.classList.add('opacity-50', 'pointer-events-none');
      } else {
        exportBtn.classList.remove('opacity-50', 'pointer-events-none');
      }

      if (visibleExpenses.length === 0) {
        emptyState.classList.remove('hidden');
        expenseList.classList.add('hidden');
        expenseList.innerHTML = '';
      } else {
        emptyState.classList.add('hidden');
        expenseList.classList.remove('hidden');
        expenseList.innerHTML = '';

        visibleExpenses.slice().reverse().forEach(item => {
          const config = categoryConfig[item.category] || categoryConfig.Other;
          const li = document.createElement('li');
          li.className = 'flex justify-between items-center p-3 bg-slate-50 border border-slate-200 rounded-lg text-sm group hover:border-slate-300 transition-colors';
          li.innerHTML = `
            <div class="flex items-center space-x-2.5">
              <span class="text-xs px-2 py-0.5 rounded-full font-medium border ${config.badge}">
                ${item.category}
              </span>
              <span class="font-medium text-slate-800">${item.description}</span>
            </div>
            <div class="flex items-center space-x-3">
              <span class="font-semibold text-slate-900">${currencySymbol}${item.amount.toFixed(2)}</span>
              <button 
                type="button" 
                data-id="${item.id}" 
                class="delete-btn text-slate-400 hover:text-rose-600 transition-colors p-1 rounded hover:bg-rose-50"
                title="Delete expense"
                aria-label="Delete ${item.description}"
              >
                <svg class="w-4 h-4 pointer-events-none" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                </svg>
              </button>
            </div>
          `;
          expenseList.appendChild(li);
        });
      }
    }

    render();
  </script>
</body>
</html>

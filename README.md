<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Petrol Expense Tracker</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
            background-color: #f8fafc;
        }
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
        .modal-enter {
            animation: modalFadeIn 0.25s cubic-bezier(0.16, 1, 0.3, 1) forwards;
        }
        @keyframes modalFadeIn {
            from {
                opacity: 0;
                transform: scale(0.95) translateY(10px);
            }
            to {
                opacity: 1;
                transform: scale(1) translateY(0);
            }
        }
    </style>
</head>
<body class="text-slate-800 antialiased min-h-screen flex flex-col bg-slate-50">

    <header class="bg-slate-900 text-white shadow-lg border-b border-slate-800 sticky top-0 z-30">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-3.5 flex flex-col sm:flex-row justify-between items-center gap-3">
            <div class="flex items-center space-x-3">
                <div class="bg-emerald-500/10 border border-emerald-500/20 p-2.5 rounded-xl text-emerald-400">
                    <i class="fa-solid fa-gas-pump text-2xl"></i>
                </div>
                <div>
                    <h1 class="text-lg font-bold tracking-tight text-white flex items-center gap-2">
                        Petrol Expense Tracker
                        <span class="text-[10px] bg-emerald-500/20 text-emerald-300 px-2 py-0.5 rounded-full border border-emerald-500/30 uppercase tracking-wide font-semibold">INR ₹</span>
                    </h1>
                    <p class="text-xs text-slate-400">Log, track and analyze your vehicle fuel spending</p>
                </div>
            </div>

            <div class="flex items-center gap-2 flex-wrap sm:flex-nowrap justify-center">
                <button onclick="openModal()" class="bg-emerald-600 hover:bg-emerald-500 text-white text-xs font-semibold py-2 px-3.5 rounded-lg transition-all duration-150 flex items-center gap-1.5 shadow-md shadow-emerald-950/40 active:scale-95">
                    <i class="fa-solid fa-plus text-sm"></i> Add Fill-up
                </button>
                <div class="h-4 w-px bg-slate-700 hidden sm:block"></div>
                <button onclick="exportCSV()" class="bg-slate-800 hover:bg-slate-700 text-slate-200 text-xs font-semibold py-2 px-3 rounded-lg border border-slate-700 transition-colors flex items-center gap-1.5 shadow-sm">
                    <i class="fa-solid fa-file-csv text-slate-400"></i> Export CSV
                </button>
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6 flex-1 w-full space-y-6">

        <!-- Sync Indicator Banner -->
        <div id="syncStatus" class="hidden text-xs font-semibold px-4 py-2.5 rounded-xl border flex items-center justify-between transition-all">
            <span id="syncText" class="flex items-center gap-2"></span>
        </div>

        <!-- Metrics Overview Cards -->
        <section>
            <div class="flex items-center justify-between mb-3">
                <h2 class="text-sm font-bold text-slate-700 uppercase tracking-wider flex items-center gap-2">
                    <i class="fa-solid fa-chart-simple text-emerald-600"></i> Expense Overview
                </h2>
                <span id="filteredCountBadge" class="text-xs bg-slate-200/80 text-slate-700 font-medium px-2.5 py-0.5 rounded-full border border-slate-300/60">
                    Showing All Entries
                </span>
            </div>

            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white rounded-2xl p-4 shadow-sm border border-slate-200 flex items-center justify-between">
                    <div>
                        <p class="text-xs font-semibold uppercase tracking-wider text-slate-400">Total Expenditure</p>
                        <h3 id="cardTotalAmount" class="text-2xl font-extrabold text-slate-900 mt-1">₹0.00</h3>
                    </div>
                    <div class="w-11 h-11 bg-emerald-50 text-emerald-600 rounded-xl flex items-center justify-center text-xl font-bold border border-emerald-100">
                        ₹
                    </div>
                </div>

                <div class="bg-white rounded-2xl p-4 shadow-sm border border-slate-200 flex items-center justify-between">
                    <div>
                        <p class="text-xs font-semibold uppercase tracking-wider text-slate-400">Total Fuel Consumed</p>
                        <h3 id="cardTotalLiters" class="text-2xl font-extrabold text-slate-900 mt-1">0.00 L</h3>
                    </div>
                    <div class="w-11 h-11 bg-teal-50 text-teal-600 rounded-xl flex items-center justify-center text-lg border border-teal-100">
                        <i class="fa-solid fa-oil-can"></i>
                    </div>
                </div>

                <div class="bg-white rounded-2xl p-4 shadow-sm border border-slate-200 flex items-center justify-between">
                    <div>
                        <p class="text-xs font-semibold uppercase tracking-wider text-slate-400">Total Fill-ups</p>
                        <h3 id="cardTotalFillups" class="text-2xl font-extrabold text-slate-900 mt-1">0</h3>
                    </div>
                    <div class="w-11 h-11 bg-sky-50 text-sky-600 rounded-xl flex items-center justify-center text-lg border border-sky-100">
                        <i class="fa-solid fa-car-side"></i>
                    </div>
                </div>

                <div class="bg-white rounded-2xl p-4 shadow-sm border border-slate-200 flex items-center justify-between">
                    <div>
                        <p class="text-xs font-semibold uppercase tracking-wider text-slate-400">Avg Price / Liter</p>
                        <h3 id="cardAvgPrice" class="text-2xl font-extrabold text-slate-900 mt-1">₹0.00</h3>
                    </div>
                    <div class="w-11 h-11 bg-amber-50 text-amber-600 rounded-xl flex items-center justify-center text-lg border border-amber-100">
                        <i class="fa-solid fa-calculator"></i>
                    </div>
                </div>
            </div>
        </section>

        <section class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
            <div class="p-4 sm:p-5 border-b border-slate-200 flex flex-col md:flex-row md:items-center justify-between gap-4 bg-slate-50/70">
                <div>
                    <h2 class="text-base font-bold text-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-receipt text-emerald-600"></i> Fuel Fill-up History
                    </h2>
                    <p class="text-xs text-slate-500 mt-0.5">Detailed log of individual petrol purchases and fuel pump entries</p>
                </div>

                <div class="flex flex-wrap items-center gap-2">
                    <div class="relative min-w-[180px] flex-1 sm:flex-initial">
                        <i class="fa-solid fa-magnifying-glass absolute left-3 top-1/2 -translate-y-1/2 text-slate-400 text-xs"></i>
                        <input type="text" id="searchInput" oninput="applyFilters()" placeholder="Search station, date..." 
                            class="w-full pl-8 pr-3 py-1.5 text-xs bg-white border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500">
                    </div>

                    <select id="filterYear" onchange="applyFilters()" class="text-xs bg-white border border-slate-300 rounded-lg px-2.5 py-1.5 focus:outline-none focus:ring-2 focus:ring-emerald-500">
                        <option value="ALL">All Years</option>
                    </select>

                    <select id="filterMonth" onchange="applyFilters()" class="text-xs bg-white border border-slate-300 rounded-lg px-2.5 py-1.5 focus:outline-none focus:ring-2 focus:ring-emerald-500">
                        <option value="ALL">All Months</option>
                        <option value="January">January</option>
                        <option value="February">February</option>
                        <option value="March">March</option>
                        <option value="April">April</option>
                        <option value="May">May</option>
                        <option value="June">June</option>
                        <option value="July">July</option>
                        <option value="August">August</option>
                        <option value="September">September</option>
                        <option value="October">October</option>
                        <option value="November">November</option>
                        <option value="December">December</option>
                    </select>

                    <button onclick="clearFilters()" class="text-xs text-slate-500 hover:text-emerald-700 px-2 py-1 underline font-medium">
                        Clear
                    </button>
                </div>
            </div>

            <div class="overflow-x-auto">
                <table class="w-full text-left border-collapse">
                    <thead>
                        <tr class="bg-slate-100/80 text-slate-600 uppercase text-[11px] font-bold tracking-wider border-b border-slate-200">
                            <th class="py-3.5 px-4">Date</th>
                            <th class="py-3.5 px-4">Period</th>
                            <th class="py-3.5 px-4">Station / Notes</th>
                            <th class="py-3.5 px-4 text-right">User Amount (₹)</th>
                            <th class="py-3.5 px-4 text-right">Price / Liter (₹/L)</th>
                            <th class="py-3.5 px-4 text-right">Quantity (Liters)</th>
                            <th class="py-3.5 px-4 text-center">Actions</th>
                        </tr>
                    </thead>
                    <tbody id="historyTableBody" class="divide-y divide-slate-200 text-sm">
                        <!-- Dynamic rows injected via JavaScript -->
                    </tbody>
                </table>
            </div>
        </section>

    </main>

    <div id="entryModal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-slate-900/60 backdrop-blur-sm hidden">
        <div class="bg-white rounded-2xl shadow-2xl border border-slate-200 w-full max-w-lg overflow-hidden modal-enter">
            <!-- Modal Header -->
            <div class="bg-slate-900 text-white px-6 py-4 flex items-center justify-between">
                <div class="flex items-center gap-2.5">
                    <div class="w-8 h-8 rounded-lg bg-emerald-500/20 flex items-center justify-center text-emerald-400">
                        <i class="fa-solid fa-gas-pump"></i>
                    </div>
                    <h3 id="modalTitle" class="text-base font-bold text-white">Add Fuel Fill-up Entry</h3>
                </div>
                <button onclick="closeModal()" class="text-slate-400 hover:text-white p-1 rounded-lg hover:bg-slate-800 transition-colors" title="Close Modal">
                    <i class="fa-solid fa-xmark text-lg"></i>
                </button>
            </div>

            <!-- Modal Body Form -->
            <form id="fillupForm" onsubmit="handleFormSubmit(event)" class="p-6 space-y-4">
                <input type="hidden" id="editEntryId" value="">

                <div>
                    <label for="entryDate" class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-1.5">
                        Date <span class="text-rose-500">*</span>
                    </label>
                    <input type="date" id="entryDate" required
                        class="w-full px-3.5 py-2.5 bg-slate-50 border border-slate-300 rounded-xl text-sm font-medium focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white transition-all">
                </div>

                <div>
                    <label for="userAmount" class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-1.5">
                        Total Paid (₹) <span class="text-rose-500">*</span>
                    </label>
                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 pl-3.5 flex items-center text-slate-400 font-semibold text-sm">₹</span>
                        <input type="number" step="0.01" min="0.01" id="userAmount" placeholder="0.00" required oninput="calculateLitersPreview()"
                            class="w-full pl-8 pr-3.5 py-2.5 bg-slate-50 border border-slate-300 rounded-xl text-sm font-medium focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white transition-all">
                    </div>
                </div>

                <!-- Price Per Liter Preset Selector + Custom Field Option -->
                <div>
                    <label class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-1.5">
                        Price / Liter (₹/L) <span class="text-rose-500">*</span>
                    </label>
                    <div class="grid grid-cols-2 gap-2 mb-2">
                        <button type="button" id="btnPreset113" onclick="selectPricePreset(113.47)"
                            class="px-3 py-2 text-xs font-bold rounded-xl border transition-all flex items-center justify-center gap-1.5 border-emerald-500 bg-emerald-50 text-emerald-800 shadow-sm">
                            <i class="fa-solid fa-circle-dot text-[10px] text-emerald-600"></i> ₹113.47 / L
                        </button>
                        <button type="button" id="btnPreset167" onclick="selectPricePreset(167.35)"
                            class="px-3 py-2 text-xs font-bold rounded-xl border transition-all flex items-center justify-center gap-1.5 border-slate-300 bg-slate-50 text-slate-700 hover:bg-slate-100">
                            <i class="fa-regular fa-circle text-[10px] text-slate-400"></i> ₹167.35 / L
                        </button>
                    </div>

                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 pl-3.5 flex items-center text-slate-400 font-semibold text-sm">₹</span>
                        <input type="number" step="0.01" min="0.01" id="petrolPrice" placeholder="Select price or enter custom" required oninput="onCustomPriceInput()"
                            class="w-full pl-8 pr-16 py-2.5 bg-slate-50 border border-slate-300 rounded-xl text-sm font-medium focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white transition-all">
                        <span class="absolute inset-y-0 right-0 pr-3.5 flex items-center text-slate-400 font-semibold text-xs uppercase">₹/L</span>
                    </div>
                </div>

                <!-- Read-Only Quantity Field -->
                <div>
                    <div class="flex items-center justify-between mb-1.5">
                        <label for="calculatedLiters" class="block text-xs font-bold text-slate-700 uppercase tracking-wider">
                            Quantity (Liters)
                        </label>
                        <span class="text-[10px] bg-slate-100 text-slate-600 px-2 py-0.5 rounded font-semibold uppercase tracking-wider border border-slate-200">
                            <i class="fa-solid fa-lock text-[9px] mr-1"></i> Read-Only Auto-Computed
                        </span>
                    </div>
                    <div class="relative">
                        <input type="number" step="0.01" min="0" id="calculatedLiters" placeholder="0.00" readonly
                            class="w-full px-3.5 py-2.5 bg-slate-100 border border-slate-300 text-slate-900 font-bold rounded-xl text-sm cursor-not-allowed select-none focus:outline-none">
                        <span class="absolute inset-y-0 right-0 pr-3.5 flex items-center text-emerald-700 font-extrabold text-xs uppercase">Liters (L)</span>
                    </div>
                    <p class="text-[11px] text-slate-400 mt-1">Automatically computed as: Total Amount Paid (₹) ÷ Price per Liter (₹/L)</p>
                </div>

                <div>
                    <label for="stationNotes" class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-1.5">
                        Station Name / Notes <span class="text-slate-400 font-normal">(Optional)</span>
                    </label>
                    <input type="text" id="stationNotes" placeholder="e.g. HP Petrol Pump, Salt Lake"
                        class="w-full px-3.5 py-2.5 bg-slate-50 border border-slate-300 rounded-xl text-sm font-medium focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white transition-all">
                </div>

                <!-- Modal Actions Footer -->
                <div class="pt-4 border-t border-slate-100 flex items-center justify-end gap-3">
                    <button type="button" onclick="closeModal()" class="px-4 py-2.5 text-xs font-semibold text-slate-600 hover:text-slate-800 bg-slate-100 hover:bg-slate-200 rounded-xl transition-colors">
                        Cancel
                    </button>
                    <button type="submit" id="submitFormBtn"
                        class="px-5 py-2.5 bg-emerald-600 hover:bg-emerald-500 text-white font-semibold text-xs rounded-xl shadow-md transition-all flex items-center gap-1.5">
                        <i class="fa-solid fa-check"></i> <span id="submitBtnText">Save Entry</span>
                    </button>
                </div>
            </form>
        </div>
    </div>

    <footer class="bg-white border-t border-slate-200 py-4 mt-auto text-center text-xs text-slate-400">
        Petrol Expense Tracker &bull; Connected to Google Sheets
    </footer>

    <!-- Toast Notification -->
    <div id="toast" class="fixed bottom-5 right-5 bg-slate-900 text-white text-xs px-4 py-3 rounded-xl shadow-xl transition-all transform translate-y-20 opacity-0 pointer-events-none flex items-center gap-2.5 z-50 border border-slate-800">
        <i id="toastIcon" class="fa-solid fa-circle-check text-emerald-400 text-base"></i>
        <span id="toastMsg" class="font-medium">Notification message</span>
    </div>

    <script>
        // REPLACE WITH YOUR GOOGLE APPS SCRIPT WEB APP URL
        const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbzwX-5zxNl3bQ6VsGnGEiIBQN4CXCcfkk_2DIXOCUOx7Y_X6by1p9hgHolUTxxzSPeyWA/exec";

        let entries = [];

        const MONTH_NAMES = [
            "January", "February", "March", "April", "May", "June",
            "July", "August", "September", "October", "November", "December"
        ];

        function showSyncStatus(msg, status = 'loading') {
            const syncEl = document.getElementById('syncStatus');
            const syncText = document.getElementById('syncText');
            syncEl.classList.remove('hidden', 'bg-amber-50', 'text-amber-800', 'border-amber-200', 'bg-rose-50', 'text-rose-800', 'border-rose-200', 'bg-emerald-50', 'text-emerald-800', 'border-emerald-200');

            if (status === 'loading') {
                syncEl.classList.add('bg-amber-50', 'text-amber-800', 'border-amber-200');
                syncText.innerHTML = `<i class="fa-solid fa-circle-notch fa-spin text-amber-600"></i> ${msg}`;
            } else if (status === 'error') {
                syncEl.classList.add('bg-rose-50', 'text-rose-800', 'border-rose-200');
                syncText.innerHTML = `<i class="fa-solid fa-triangle-exclamation text-rose-600"></i> ${msg}`;
            } else {
                syncEl.classList.add('bg-emerald-50', 'text-emerald-800', 'border-emerald-200');
                syncText.innerHTML = `<i class="fa-solid fa-circle-check text-emerald-600"></i> ${msg}`;
                setTimeout(() => syncEl.classList.add('hidden'), 3000);
            }
        }

        async function fetchEntriesFromSheet() {
            if (!SCRIPT_URL || SCRIPT_URL === 'YOUR_GOOGLE_APPS_SCRIPT_WEB_APP_URL') {
                showSyncStatus("Please configure SCRIPT_URL with your Google Apps Script Web App URL.", "error");
                return;
            }

            showSyncStatus("Fetching expense records from Google Sheet...", "loading");

            try {
                const response = await fetch(SCRIPT_URL);
                const data = await response.json();

                if (Array.isArray(data)) {
                    entries = data;
                    populateFilterYears();
                    renderHistoryTable();
                    showSyncStatus("Data synced with Google Sheet", "success");
                } else {
                    showSyncStatus("Failed to load records from backend.", "error");
                }
            } catch (err) {
                console.error(err);
                showSyncStatus("Network error connecting to Google Sheet backend.", "error");
            }
        }

        async function saveEntriesToSheet() {
            if (!SCRIPT_URL || SCRIPT_URL === 'YOUR_GOOGLE_APPS_SCRIPT_WEB_APP_URL') return;

            showSyncStatus("Saving changes to Google Sheet...", "loading");

            try {
                const response = await fetch(SCRIPT_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'text/plain;charset=utf-8' },
                    body: JSON.stringify(entries)
                });
                const resData = await response.json();

                if (resData.status === 'success') {
                    showSyncStatus("Saved successfully to Google Sheet!", "success");
                } else {
                    showSyncStatus("Save failed: " + resData.message, "error");
                }
            } catch (err) {
                console.error(err);
                showSyncStatus("Network error saving changes.", "error");
            }
        }

        function formatINR(val) {
            return new Intl.NumberFormat('en-IN', {
                style: 'currency',
                currency: 'INR',
                minimumFractionDigits: 2,
                maximumFractionDigits: 2
            }).format(val);
        }

        /* Modal Logic */
        function openModal(editId = null) {
            const modal = document.getElementById('entryModal');
            const modalTitle = document.getElementById('modalTitle');
            const submitBtnText = document.getElementById('submitBtnText');
            
            if (editId) {
                const item = entries.find(e => e.id === editId);
                if (!item) return;

                document.getElementById('editEntryId').value = item.id;
                document.getElementById('entryDate').value = item.date;
                document.getElementById('userAmount').value = item.userAmount;
                document.getElementById('stationNotes').value = item.notes || '';

                selectPricePreset(item.petrolPrice);

                modalTitle.innerText = "Edit Fuel Fill-up Entry";
                submitBtnText.innerText = "Update Entry";
            } else {
                document.getElementById('fillupForm').reset();
                document.getElementById('editEntryId').value = '';
                document.getElementById('entryDate').valueAsDate = new Date();
                
                selectPricePreset(113.47);

                modalTitle.innerText = "Add Fuel Fill-up Entry";
                submitBtnText.innerText = "Save Entry";
            }

            modal.classList.remove('hidden');
        }

        function closeModal() {
            document.getElementById('entryModal').classList.add('hidden');
            document.getElementById('fillupForm').reset();
            document.getElementById('editEntryId').value = '';
        }

        /* Preset Price Selector Handling */
        function selectPricePreset(price) {
            const priceInput = document.getElementById('petrolPrice');
            priceInput.value = price;
            updatePresetButtonStyles(price);
            calculateLitersPreview();
        }

        function onCustomPriceInput() {
            const currentVal = parseFloat(document.getElementById('petrolPrice').value);
            updatePresetButtonStyles(currentVal);
            calculateLitersPreview();
        }

        function updatePresetButtonStyles(activePrice) {
            const btn113 = document.getElementById('btnPreset113');
            const btn167 = document.getElementById('btnPreset167');

            const is113 = Math.abs(activePrice - 113.47) < 0.01;
            const is167 = Math.abs(activePrice - 167.35) < 0.01;

            if (is113) {
                btn113.className = "px-3 py-2 text-xs font-bold rounded-xl border transition-all flex items-center justify-center gap-1.5 border-emerald-500 bg-emerald-50 text-emerald-800 shadow-sm";
                btn113.innerHTML = `<i class="fa-solid fa-circle-dot text-[10px] text-emerald-600"></i> ₹113.47 / L`;
            } else {
                btn113.className = "px-3 py-2 text-xs font-bold rounded-xl border transition-all flex items-center justify-center gap-1.5 border-slate-300 bg-slate-50 text-slate-700 hover:bg-slate-100";
                btn113.innerHTML = `<i class="fa-regular fa-circle text-[10px] text-slate-400"></i> ₹113.47 / L`;
            }

            if (is167) {
                btn167.className = "px-3 py-2 text-xs font-bold rounded-xl border transition-all flex items-center justify-center gap-1.5 border-emerald-500 bg-emerald-50 text-emerald-800 shadow-sm";
                btn167.innerHTML = `<i class="fa-solid fa-circle-dot text-[10px] text-emerald-600"></i> ₹167.35 / L`;
            } else {
                btn167.className = "px-3 py-2 text-xs font-bold rounded-xl border transition-all flex items-center justify-center gap-1.5 border-slate-300 bg-slate-50 text-slate-700 hover:bg-slate-100";
                btn167.innerHTML = `<i class="fa-regular fa-circle text-[10px] text-slate-400"></i> ₹167.35 / L`;
            }
        }

        /* Auto Computation of Non-Editable Quantity */
        function calculateLitersPreview() {
            const amount = parseFloat(document.getElementById('userAmount').value) || 0;
            const price = parseFloat(document.getElementById('petrolPrice').value) || 0;
            const litersInput = document.getElementById('calculatedLiters');

            if (amount > 0 && price > 0) {
                const liters = (amount / price).toFixed(2);
                litersInput.value = liters;
            } else {
                litersInput.value = '';
            }
        }

        async function handleFormSubmit(e) {
            e.preventDefault();

            const dateVal = document.getElementById('entryDate').value;
            const amountVal = parseFloat(document.getElementById('userAmount').value);
            const priceVal = parseFloat(document.getElementById('petrolPrice').value);
            const qtyVal = parseFloat((amountVal / priceVal).toFixed(2));
            const notesVal = document.getElementById('stationNotes').value.trim();
            const editId = document.getElementById('editEntryId').value;

            if (!dateVal || isNaN(amountVal) || isNaN(priceVal) || priceVal <= 0 || amountVal <= 0) {
                showToast("Please enter valid positive amounts and date.", "error");
                return;
            }

            if (editId) {
                const idx = entries.findIndex(item => item.id === editId);
                if (idx !== -1) {
                    entries[idx] = { 
                        id: editId, 
                        date: dateVal, 
                        userAmount: amountVal, 
                        petrolPrice: priceVal,
                        quantity: qtyVal,
                        notes: notesVal 
                    };
                    showToast("Entry updated!");
                }
            } else {
                const newEntry = {
                    id: 'e_' + Date.now(),
                    date: dateVal,
                    userAmount: amountVal,
                    petrolPrice: priceVal,
                    quantity: qtyVal,
                    notes: notesVal
                };
                entries.unshift(newEntry);
                showToast("New fuel fill-up recorded!");
            }

            closeModal();
            populateFilterYears();
            renderHistoryTable();
            await saveEntriesToSheet();
        }

        function editEntry(id) {
            openModal(id);
        }

        async function deleteEntry(id) {
            if (confirm("Are you sure you want to delete this fill-up record?")) {
                entries = entries.filter(item => item.id !== id);
                populateFilterYears();
                renderHistoryTable();
                showToast("Entry deleted.");
                await saveEntriesToSheet();
            }
        }

        function populateFilterYears() {
            const yearSelect = document.getElementById('filterYear');
            const currentSelectedYear = yearSelect.value;
            
            const years = [...new Set(entries.map(e => new Date(e.date).getFullYear()))].sort((a,b) => b - a);

            yearSelect.innerHTML = '<option value="ALL">All Years</option>';
            years.forEach(y => {
                if(!isNaN(y)){
                    const opt = document.createElement('option');
                    opt.value = y;
                    opt.textContent = y;
                    yearSelect.appendChild(opt);
                }
            });

            if (years.includes(parseInt(currentSelectedYear))) {
                yearSelect.value = currentSelectedYear;
            } else {
                yearSelect.value = "ALL";
            }
        }

        function applyFilters() {
            renderHistoryTable();
        }

        function clearFilters() {
            document.getElementById('filterYear').value = "ALL";
            document.getElementById('filterMonth').value = "ALL";
            document.getElementById('searchInput').value = "";
            renderHistoryTable();
        }

        function renderHistoryTable() {
            const tbody = document.getElementById('historyTableBody');
            const selectedYear = document.getElementById('filterYear').value;
            const selectedMonth = document.getElementById('filterMonth').value;
            const searchQuery = document.getElementById('searchInput').value.toLowerCase().trim();

            let filtered = [...entries].sort((a,b) => new Date(b.date) - new Date(a.date));

            if (selectedYear !== "ALL") {
                filtered = filtered.filter(e => new Date(e.date).getFullYear() == selectedYear);
            }

            if (selectedMonth !== "ALL") {
                filtered = filtered.filter(e => {
                    const mName = MONTH_NAMES[new Date(e.date).getMonth()];
                    return mName === selectedMonth;
                });
            }

            if (searchQuery) {
                filtered = filtered.filter(e => {
                    const d = e.date.toLowerCase();
                    const amt = e.userAmount.toString();
                    const price = e.petrolPrice.toString();
                    const notes = (e.notes || '').toLowerCase();
                    return d.includes(searchQuery) || amt.includes(searchQuery) || price.includes(searchQuery) || notes.includes(searchQuery);
                });
            }

            // Calculate KPI Summary Cards
            let totalAmount = 0;
            let totalLiters = 0;
            let count = filtered.length;

            filtered.forEach(item => {
                const liters = item.quantity || (item.userAmount / item.petrolPrice);
                totalAmount += Number(item.userAmount);
                totalLiters += Number(liters);
            });

            const avgPrice = totalLiters > 0 ? (totalAmount / totalLiters) : 0;

            document.getElementById('cardTotalAmount').innerText = formatINR(totalAmount);
            document.getElementById('cardTotalLiters').innerText = `${totalLiters.toFixed(2)} L`;
            document.getElementById('cardTotalFillups').innerText = count;
            document.getElementById('cardAvgPrice').innerText = `₹${avgPrice.toFixed(2)}`;

            const badge = document.getElementById('filteredCountBadge');
            if (filtered.length === entries.length) {
                badge.innerText = `Showing All ${entries.length} Entries`;
            } else {
                badge.innerText = `Filtered (${filtered.length} of ${entries.length})`;
            }

            if (filtered.length === 0) {
                tbody.innerHTML = `
                    <tr>
                        <td colspan="7" class="text-center py-12 text-slate-400">
                            <i class="fa-solid fa-folder-open text-3xl mb-2 block text-slate-300"></i>
                            <p class="text-xs font-semibold text-slate-500">No fuel records found</p>
                            <p class="text-[11px] text-slate-400 mt-0.5">Try clearing filters or adding a new fill-up entry.</p>
                        </td>
                    </tr>`;
                return;
            }

            tbody.innerHTML = filtered.map(item => {
                const d = new Date(item.date);
                const year = d.getFullYear();
                const monthName = MONTH_NAMES[d.getMonth()];
                const liters = item.quantity ? Number(item.quantity).toFixed(2) : (item.userAmount / item.petrolPrice).toFixed(2);
                const notesText = item.notes || '-';

                return `
                    <tr class="hover:bg-slate-50 transition-colors">
                        <td class="py-3.5 px-4 font-semibold text-slate-800">${item.date}</td>
                        <td class="py-3.5 px-4 text-slate-600">
                            <span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-[11px] font-semibold bg-emerald-50 text-emerald-800 border border-emerald-200">
                                ${year} ${monthName}
                            </span>
                        </td>
                        <td class="py-3.5 px-4 text-slate-600 text-xs font-medium">${notesText}</td>
                        <td class="py-3.5 px-4 text-right font-bold text-slate-900">${formatINR(item.userAmount)}</td>
                        <td class="py-3.5 px-4 text-right text-slate-600 font-semibold">₹${Number(item.petrolPrice).toFixed(2)}</td>
                        <td class="py-3.5 px-4 text-right font-semibold text-emerald-700">${liters} L</td>
                        <td class="py-3.5 px-4 text-center">
                            <div class="inline-flex items-center gap-1">
                                <button onclick="editEntry('${item.id}')" class="text-slate-500 hover:text-emerald-600 p-1.5 rounded-lg hover:bg-emerald-50 transition-colors" title="Edit Entry">
                                    <i class="fa-solid fa-pen-to-square"></i>
                                </button>
                                <button onclick="deleteEntry('${item.id}')" class="text-slate-500 hover:text-rose-600 p-1.5 rounded-lg hover:bg-rose-50 transition-colors" title="Delete Entry">
                                    <i class="fa-solid fa-trash-can"></i>
                                </button>
                            </div>
                        </td>
                    </tr>
                `;
            }).join('');
        }

        function exportCSV() {
            if (entries.length === 0) {
                showToast("No data to export.", "error");
                return;
            }

            let csvContent = "data:text/csv;charset=utf-8,Date,Station Notes,User Amount (INR),Price per Liter (INR),Quantity (Liters)\n";
            entries.forEach(e => {
                const qty = e.quantity || (e.userAmount / e.petrolPrice).toFixed(2);
                const notes = `"${(e.notes || '').replace(/"/g, '""')}"`;
                csvContent += `${e.date},${notes},${e.userAmount},${e.petrolPrice},${qty}\n`;
            });

            const encodedUri = encodeURI(csvContent);
            const link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", `Petrol_Expenses_${new Date().toISOString().split('T')[0]}.csv`);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            showToast("CSV exported successfully.");
        }

        function showToast(message, type = "success") {
            const toast = document.getElementById('toast');
            const toastMsg = document.getElementById('toastMsg');
            const toastIcon = document.getElementById('toastIcon');

            toastMsg.innerText = message;
            if (type === "error") {
                toastIcon.className = "fa-solid fa-circle-xmark text-rose-400 text-base";
            } else {
                toastIcon.className = "fa-solid fa-circle-check text-emerald-400 text-base";
            }

            toast.classList.remove('translate-y-20', 'opacity-0', 'pointer-events-none');
            
            setTimeout(() => {
                toast.classList.add('translate-y-20', 'opacity-0', 'pointer-events-none');
            }, 3000);
        }

        window.onclick = function(e) {
            const modal = document.getElementById('entryModal');
            if (e.target === modal) {
                closeModal();
            }
        };

        window.onload = function() {
            fetchEntriesFromSheet();
        };
    </script>
</body>
</html>

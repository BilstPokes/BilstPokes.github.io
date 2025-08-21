<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GPay</title>
    <!-- Use Inter font from Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <!-- Load Tailwind CSS from CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        /* Custom scrollbar for transaction history */
        .transaction-history-container::-webkit-scrollbar {
            width: 8px;
        }
        .transaction-history-container::-webkit-scrollbar-thumb {
            background-color: #cbd5e1;
            border-radius: 4px;
        }
        /* Style for the clickable user list items */
        .user-list-item {
            cursor: pointer;
            transition: transform 0.2s;
        }
        .user-list-item:hover {
            transform: scale(1.02);
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen flex items-center justify-center p-4">

    <!-- Main Container -->
    <div id="app-container" class="w-full max-w-xl mx-auto bg-white rounded-2xl shadow-xl overflow-hidden md:p-10 p-6 flex flex-col items-center">

        <!-- Logo/Title -->
        <div class="flex items-center space-x-2 mb-8">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10 text-emerald-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8z"/><path d="M12 7v5l4 2"/>
            </svg>
            <h1 class="text-3xl font-bold text-slate-800">GPay</h1>
        </div>

        <!-- Message Box -->
        <div id="message-box" class="w-full text-center p-3 mb-6 hidden rounded-lg transition-all duration-300 ease-in-out"></div>

        <!-- Login View -->
        <div id="login-view" class="w-full max-w-sm">
            <h2 class="text-2xl font-bold mb-4 text-center">Log In to Your Account</h2>
            <form id="login-form" class="space-y-4">
                <div>
                    <label for="username" class="block text-sm font-medium text-slate-600">Username</label>
                    <input type="text" id="username" name="username" class="mt-1 block w-full px-4 py-2 bg-slate-100 border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" required>
                </div>
                <div>
                    <label for="password" class="block text-sm font-medium text-slate-600">Password</label>
                    <input type="password" id="password" name="password" class="mt-1 block w-full px-4 py-2 bg-slate-100 border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" required>
                </div>
                <button type="submit" class="w-full py-3 px-4 bg-emerald-500 text-white font-semibold rounded-lg shadow-md hover:bg-emerald-600 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2">Log In</button>
            </form>
        </div>

        <!-- Dashboard View (hidden by default) -->
        <div id="dashboard-view" class="w-full hidden">
            <div class="flex flex-col md:flex-row md:items-center justify-between mb-6 relative">
                <div class="mb-4 md:mb-0">
                    <p class="text-lg text-slate-500">Welcome, <span id="welcome-name" class="font-semibold text-slate-700"></span></p>
                    <h3 class="text-3xl font-bold text-emerald-600 mt-1">
                        $<span id="current-balance">0.00</span>
                    </h3>
                </div>
                
                <!-- User Profile and Dropdown Menu -->
                <div class="absolute top-0 right-0">
                    <div id="profile-icon" class="w-12 h-12 rounded-full overflow-hidden shadow-lg cursor-pointer ring-2 ring-emerald-500 ring-offset-2 ring-offset-slate-50 transition-transform hover:scale-105 flex items-center justify-center">
                        <img id="profile-picture" src="" alt="Profile Picture" class="w-full h-full object-cover hidden">
                        <div id="default-profile-icon" class="w-full h-full text-slate-400 p-2">
                             <svg xmlns="http://www.w3.org/2000/svg" class="h-full w-full" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                                 <circle cx="12" cy="8" r="5" />
                                 <path d="M20 21a8 8 0 0 0-16 0" />
                            </svg>
                        </div>
                    </div>
                    
                    <!-- Dropdown Menu -->
                    <div id="user-menu" class="hidden absolute top-14 right-0 w-48 bg-white rounded-lg shadow-xl py-2 z-10">
                        <a href="#" id="account-settings-link" class="block px-4 py-2 text-slate-700 hover:bg-slate-100">Account Settings</a>
                        <a href="#" id="redeem-link" class="block px-4 py-2 text-slate-700 hover:bg-slate-100">Redeem</a>
                        <div class="border-t border-slate-200 my-1"></div>
                        <a href="#" id="sign-out-link" class="block px-4 py-2 text-red-500 hover:bg-red-100">Sign Out</a>
                    </div>
                </div>
            </div>

            <!-- Main Dashboard Content -->
            <div id="dashboard-content" class="w-full">
                <!-- Transaction/Transfer Section -->
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">

                    <!-- Transfer Funds Form -->
                    <div class="bg-slate-100 p-6 rounded-xl">
                        <h4 class="text-xl font-semibold mb-4 text-slate-700">Transfer Funds</h4>
                        <form id="transfer-form" class="space-y-4">
                            <div>
                                <label for="recipient-username" class="block text-sm font-medium text-slate-600">Recipient</label>
                                <input type="text" id="recipient-username" name="recipient-username" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" placeholder="Username" required>
                            </div>
                            <div>
                                <label for="transfer-amount" class="block text-sm font-medium text-slate-600">Amount ($)</label>
                                <input type="number" id="transfer-amount" name="transfer-amount" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" min="0.01" step="0.01" required>
                            </div>
                            <button type="submit" class="w-full py-3 px-4 bg-emerald-500 text-white font-semibold rounded-lg shadow-md hover:bg-emerald-600 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2">
                                Transfer
                            </button>
                        </form>
                    </div>

                    <!-- Transaction History -->
                    <div class="bg-slate-100 p-6 rounded-xl">
                        <h4 class="text-xl font-semibold mb-4 text-slate-700">Recent Transactions</h4>
                        <div id="transaction-history" class="h-60 overflow-y-auto space-y-3 pr-2 transaction-history-container">
                            <!-- Transaction items will be inserted here by JavaScript -->
                        </div>
                    </div>

                    <!-- Admin Panel (hidden by default) -->
                    <div id="admin-panel" class="bg-slate-100 p-6 rounded-xl hidden md:col-span-2">
                        <h4 class="text-xl font-semibold mb-4 text-slate-700">Admin Panel</h4>
                        
                        <!-- Add New User Form -->
                        <h5 class="text-lg font-medium text-slate-600 mb-2 mt-4">Add New User</h5>
                        <form id="add-user-form" class="space-y-4">
                            <div>
                                <label for="new-username" class="block text-sm font-medium text-slate-600">New Username</label>
                                <input type="text" id="new-username" name="new-username" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" placeholder="Username" required>
                            </div>
                            <div>
                                <label for="new-password" class="block text-sm font-medium text-slate-600">New Password</label>
                                <input type="password" id="new-password" name="new-password" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" required>
                            </div>
                            <button type="submit" class="w-full py-3 px-4 bg-emerald-500 text-white font-semibold rounded-lg shadow-md hover:bg-emerald-600 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2">
                                Add User
                            </button>
                        </form>

                        <!-- Manage Users Section - Main List View -->
                        <div id="manage-users-list-view">
                            <h5 class="text-lg font-medium text-slate-600 mb-2 mt-8">Manage Users</h5>
                            <div id="user-list" class="space-y-2">
                                <!-- User list will be populated by JavaScript -->
                            </div>
                        </div>

                        <!-- Manage User Section - Individual User View (hidden) -->
                        <div id="manage-user-view" class="hidden">
                            <div class="flex items-center justify-between">
                                 <h5 id="manage-user-title" class="text-lg font-medium text-slate-600 mb-2 mt-8">Manage User</h5>
                                 <button id="back-to-users-btn" class="py-1 px-3 bg-slate-300 text-slate-800 text-sm font-semibold rounded-full hover:bg-slate-400 transition-colors duration-200">
                                    Back
                                </button>
                            </div>
                            <div id="selected-user-details" class="bg-white p-4 rounded-lg shadow-sm mt-4 space-y-2">
                                <!-- User details will be populated by JavaScript -->
                            </div>
                            
                            <!-- Action Buttons -->
                            <div class="grid grid-cols-2 gap-4 mt-4">
                                <button id="delete-user-btn" class="w-full py-2 px-4 bg-red-500 text-white font-semibold rounded-lg shadow-md hover:bg-red-600 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2">
                                    Delete User
                                </button>
                                <button id="toggle-card-btn" class="w-full py-2 px-4 bg-yellow-500 text-white font-semibold rounded-lg shadow-md hover:bg-yellow-600 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-yellow-500 focus:ring-offset-2">
                                    Toggle Card
                                </button>
                            </div>

                            <!-- Add/Remove Funds Form -->
                            <div class="flex flex-col space-y-2 mt-4">
                                 <h6 class="text-md font-medium text-slate-600 mt-4">Adjust Funds</h6>
                                 <input type="number" id="funds-amount-input" name="funds-amount" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" placeholder="Amount ($)" min="0.01" step="0.01">
                                 <div class="flex space-x-2">
                                    <button id="add-funds-btn" class="w-1/2 py-2 px-4 bg-emerald-500 text-white font-semibold rounded-lg shadow-md hover:bg-emerald-600 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2">
                                        Add Funds
                                    </button>
                                    <button id="remove-funds-btn" class="w-1/2 py-2 px-4 bg-red-500 text-white font-semibold rounded-lg shadow-md hover:bg-red-600 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2">
                                        Remove Funds
                                    </button>
                                 </div>
                            </div>
                            
                            <!-- Daily Spending Limit Section -->
                            <div class="flex flex-col space-y-2 mt-6">
                                <h6 class="text-md font-medium text-slate-600">Daily Spending Limit</h6>
                                <p id="current-limit-text" class="text-sm text-slate-500"></p>
                                <input type="number" id="daily-limit-input" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" placeholder="Set limit ($)">
                                <div class="flex space-x-2">
                                     <button id="set-daily-limit-btn" class="w-1/2 py-2 px-4 bg-emerald-500 text-white font-semibold rounded-lg shadow-md hover:bg-emerald-600 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2">
                                        Set Limit
                                    </button>
                                    <button id="clear-daily-limit-btn" class="w-1/2 py-2 px-4 bg-slate-500 text-white font-semibold rounded-lg shadow-md hover:bg-slate-600 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-slate-500 focus:ring-offset-2">
                                        Clear Limit
                                    </button>
                                </div>
                            </div>

                            <!-- Change Password Form -->
                            <div class="flex flex-col space-y-2 mt-6">
                                <h6 class="text-md font-medium text-slate-600">Change Password</h6>
                                <input type="text" id="new-password-input" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" placeholder="Enter new password">
                                <button id="change-password-btn" class="w-full py-2 px-4 bg-gray-700 text-white font-semibold rounded-lg shadow-md hover:bg-gray-800 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-gray-700 focus:ring-offset-2">
                                    Change Password
                                </button>
                            </div>

                            <!-- User Transaction History -->
                            <div id="admin-user-transactions-container" class="mt-6">
                                 <h6 class="text-md font-medium text-slate-600 mb-2">User Transaction History</h6>
                                 <div id="admin-user-transactions" class="h-40 overflow-y-auto space-y-2 pr-2 transaction-history-container">
                                    <!-- Transactions will be inserted here by JavaScript -->
                                 </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Account Settings View (hidden by default) -->
            <div id="account-settings-view" class="w-full hidden">
                <div class="flex items-center justify-between mb-6">
                    <h2 class="text-2xl font-bold text-slate-800">Account Settings</h2>
                    <button id="back-to-dashboard-btn" class="py-2 px-4 bg-slate-300 text-slate-800 font-semibold rounded-lg shadow-md hover:bg-slate-400 transition-colors duration-200">Back</button>
                </div>
                
                <div class="bg-slate-100 p-6 rounded-xl space-y-6">
                    <!-- Change Profile Picture -->
                    <div>
                        <h4 class="text-xl font-semibold mb-2 text-slate-700">Change Profile Photo</h4>
                        <div class="flex items-center space-x-4">
                            <div id="settings-profile-container" class="w-16 h-16 rounded-full overflow-hidden shadow-lg flex items-center justify-center">
                                <img id="settings-profile-picture" src="" alt="Profile Picture" class="w-full h-full object-cover hidden">
                                <div id="default-settings-icon" class="w-full h-full text-slate-400 p-2">
                                    <svg xmlns="http://www.w3.org/2000/svg" class="h-full w-full" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                                         <circle cx="12" cy="8" r="5" />
                                         <path d="M20 21a8 8 0 0 0-16 0" />
                                    </svg>
                                </div>
                            </div>
                            <input type="file" id="profile-photo-input" accept="image/*" class="text-sm text-slate-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-emerald-50 file:text-emerald-700 hover:file:bg-emerald-100">
                        </div>
                    </div>

                    <!-- Change Password Form -->
                    <div class="border-t border-slate-200 pt-6">
                        <h4 class="text-xl font-semibold mb-4 text-slate-700">Change Password</h4>
                        <form id="change-password-form" class="space-y-4">
                            <div>
                                <label for="current-password" class="block text-sm font-medium text-slate-600">Current Password</label>
                                <input type="password" id="current-password" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" required>
                            </div>
                            <div>
                                <label for="new-password-user" class="block text-sm font-medium text-slate-600">New Password</label>
                                <input type="password" id="new-password-user" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" required>
                            </div>
                            <div>
                                <label for="confirm-password-user" class="block text-sm font-medium text-slate-600">Confirm New Password</label>
                                <input type="password" id="confirm-password-user" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" required>
                            </div>
                            <button type="submit" class="w-full py-3 px-4 bg-emerald-500 text-white font-semibold rounded-lg shadow-md hover:bg-emerald-600 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2">
                                Update Password
                            </button>
                        </form>
                    </div>
                </div>
            </div>

            <!-- Redeem View (hidden by default) -->
            <div id="redeem-view" class="w-full hidden">
                <div class="flex items-center justify-between mb-6">
                    <h2 class="text-2xl font-bold text-slate-800">Redeem Code</h2>
                    <button id="back-to-dashboard-from-redeem-btn" class="py-2 px-4 bg-slate-300 text-slate-800 font-semibold rounded-lg shadow-md hover:bg-slate-400 transition-colors duration-200">Back</button>
                </div>
                
                <div class="bg-slate-100 p-6 rounded-xl">
                    <form id="redeem-form" class="space-y-4">
                        <div>
                            <label for="redeem-code-input" class="block text-sm font-medium text-slate-600">Enter Redeem Code</label>
                            <input type="text" id="redeem-code-input" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" placeholder="e.g., WELCOME50" required>
                        </div>
                        <button type="submit" class="w-full py-3 px-4 bg-emerald-500 text-white font-semibold rounded-lg shadow-md hover:bg-emerald-600 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2">
                            Redeem
                        </button>
                    </form>
                </div>
            </div>

            <!-- Admin Redeem Management View (hidden by default) -->
            <div id="admin-redeem-view" class="w-full hidden">
                <div class="flex items-center justify-between mb-6">
                    <h2 class="text-2xl font-bold text-slate-800">Manage Redeem Codes</h2>
                    <button id="back-to-dashboard-from-admin-redeem-btn" class="py-2 px-4 bg-slate-300 text-slate-800 font-semibold rounded-lg shadow-md hover:bg-slate-400 transition-colors duration-200">Back</button>
                </div>
                
                <div class="bg-slate-100 p-6 rounded-xl space-y-6">
                    <!-- Create New Code Form -->
                    <div>
                        <h4 class="text-xl font-semibold mb-4 text-slate-700">Create New Code</h4>
                        <form id="create-redeem-form" class="space-y-4">
                            <div>
                                <label for="new-redeem-code-name" class="block text-sm font-medium text-slate-600">Code Name</label>
                                <input type="text" id="new-redeem-code-name" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" placeholder="e.g., FREE10" required>
                            </div>
                            <div>
                                <label for="new-redeem-prize-amount" class="block text-sm font-medium text-slate-600">Prize Amount ($)</label>
                                <input type="number" id="new-redeem-prize-amount" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" placeholder="e.g., 10.00" min="0.01" step="0.01" required>
                            </div>
                            <div>
                                <label for="new-redeem-exp-date" class="block text-sm font-medium text-slate-600">Expiration Date (Optional)</label>
                                <input type="date" id="new-redeem-exp-date" class="mt-1 block w-full px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500">
                            </div>
                            <button type="submit" class="w-full py-3 px-4 bg-emerald-500 text-white font-semibold rounded-lg shadow-md hover:bg-emerald-600 transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2">
                                Create Code
                            </button>
                        </form>
                    </div>

                    <!-- Active Codes List -->
                    <div>
                        <h4 class="text-xl font-semibold mb-4 text-slate-700">Active Codes</h4>
                        <div id="active-redeem-codes-table" class="bg-white rounded-xl shadow overflow-hidden">
                             <div class="grid grid-cols-4 gap-4 px-4 py-3 bg-slate-100 font-bold text-sm text-slate-600">
                                <span>Code</span>
                                <span>Prize</span>
                                <span>Expires</span>
                                <span>Used</span>
                             </div>
                            <div id="redeem-code-list" class="space-y-2 p-4">
                                <!-- Code items will be inserted here by JavaScript -->
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </div>

    <script>
        // Data structure for the fake bank accounts
        const accounts = [
            {
                username: 'gereltodm',
                password: 'Gn89717188.',
                balance: Infinity,
                transactions: [],
                isAdmin: true,
                isCardBlocked: false,
                cardNumber: '1234-5678-9012-3456',
                profilePicture: null,
                dailyLimit: null,
                spentToday: 0,
                lastSpendingDate: null,
            },
            {
                username: 'jane',
                password: '5678',
                balance: 1500.00,
                transactions: [],
                isAdmin: false,
                isCardBlocked: false,
                cardNumber: '9876-5432-1098-7654',
                profilePicture: null,
                dailyLimit: null,
                spentToday: 0,
                lastSpendingDate: null,
            },
            {
                username: 'peter',
                password: '9012',
                balance: 500.00,
                transactions: [],
                isAdmin: false,
                isCardBlocked: false,
                cardNumber: '1111-2222-3333-4444',
                profilePicture: null,
                dailyLimit: null,
                spentToday: 0,
                lastSpendingDate: null,
            },
            {
                username: 'mary',
                password: '3456',
                balance: 750.00,
                transactions: [],
                isAdmin: false,
                isCardBlocked: false,
                cardNumber: '5555-6666-7777-8888',
                profilePicture: null,
                dailyLimit: 200,
                spentToday: 0,
                lastSpendingDate: null,
            }
        ];

        // Global data structure for redeem codes
        const redeemCodes = [
            { code: 'WELCOME50', prize: 50.00, usedBy: [], expirationDate: null },
            { code: 'FREE10', prize: 10.00, usedBy: [], expirationDate: null },
        ];
        
        // Custom Confirmation Modal
        const confirmationModal = document.createElement('div');
        confirmationModal.className = 'fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full z-50 flex items-center justify-center hidden';
        confirmationModal.innerHTML = `
            <div class="relative top-20 mx-auto p-5 border w-96 shadow-lg rounded-md bg-white">
                <div class="mt-3 text-center">
                    <h3 id="modal-title" class="text-lg leading-6 font-medium text-gray-900"></h3>
                    <div class="mt-2 px-7 py-3">
                        <p id="modal-body" class="text-sm text-gray-500"></p>
                    </div>
                    <div class="items-center px-4 py-3">
                        <button id="modal-confirm-btn" class="px-4 py-2 bg-blue-600 text-white text-base font-medium rounded-md w-full shadow-sm hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500">
                            OK
                        </button>
                        <button id="modal-cancel-btn" class="mt-2 px-4 py-2 bg-gray-200 text-gray-800 text-base font-medium rounded-md w-full shadow-sm hover:bg-gray-300 focus:outline-none focus:ring-2 focus:ring-gray-400">
                            Cancel
                        </button>
                    </div>
                </div>
            </div>
        `;
        document.body.appendChild(confirmationModal);

        let currentUser = null;
        let selectedUserForAdmin = null;

        // Get DOM elements
        const loginView = document.getElementById('login-view');
        const dashboardView = document.getElementById('dashboard-view');
        const loginForm = document.getElementById('login-form');
        const transferForm = document.getElementById('transfer-form');
        const welcomeName = document.getElementById('welcome-name');
        const currentBalance = document.getElementById('current-balance');
        const transactionHistory = document.getElementById('transaction-history');
        const messageBox = document.getElementById('message-box');
        const adminPanel = document.getElementById('admin-panel');
        const addUserForm = document.getElementById('add-user-form');
        const manageUsersListView = document.getElementById('manage-users-list-view');
        const userList = document.getElementById('user-list');
        const manageUserView = document.getElementById('manage-user-view');
        const manageUserTitle = document.getElementById('manage-user-title');
        const backToUsersBtn = document.getElementById('back-to-users-btn');
        const selectedUserDetails = document.getElementById('selected-user-details');
        const deleteUserBtn = document.getElementById('delete-user-btn');
        const toggleCardBtn = document.getElementById('toggle-card-btn');
        const fundsAmountInput = document.getElementById('funds-amount-input');
        const addFundsBtn = document.getElementById('add-funds-btn');
        const removeFundsBtn = document.getElementById('remove-funds-btn');
        const newPasswordInput = document.getElementById('new-password-input');
        const changePasswordBtn = document.getElementById('change-password-btn');
        const adminUserTransactions = document.getElementById('admin-user-transactions');
        
        // Admin Daily Limit Elements
        const currentLimitText = document.getElementById('current-limit-text');
        const dailyLimitInput = document.getElementById('daily-limit-input');
        const setDailyLimitBtn = document.getElementById('set-daily-limit-btn');
        const clearDailyLimitBtn = document.getElementById('clear-daily-limit-btn');

        // Admin Redeem Code Elements
        const createRedeemForm = document.getElementById('create-redeem-form');
        const newRedeemCodeName = document.getElementById('new-redeem-code-name');
        const newRedeemPrizeAmount = document.getElementById('new-redeem-prize-amount');
        const newRedeemExpDate = document.getElementById('new-redeem-exp-date');
        const redeemCodeList = document.getElementById('redeem-code-list');

        // User Profile Elements
        const profileIcon = document.getElementById('profile-icon');
        const userMenu = document.getElementById('user-menu');
        const signOutLink = document.getElementById('sign-out-link');
        const accountSettingsLink = document.getElementById('account-settings-link');
        const redeemLink = document.getElementById('redeem-link');
        
        // Views
        const dashboardContent = document.getElementById('dashboard-content');
        const accountSettingsView = document.getElementById('account-settings-view');
        const redeemView = document.getElementById('redeem-view');
        const adminRedeemView = document.getElementById('admin-redeem-view'); // New Admin Redeem View
        
        // Account Settings Elements
        const backToDashboardBtn = document.getElementById('back-to-dashboard-btn');
        const settingsProfilePicture = document.getElementById('settings-profile-picture');
        const defaultSettingsIcon = document.getElementById('default-settings-icon');
        const profilePhotoInput = document.getElementById('profile-photo-input');
        const changePasswordForm = document.getElementById('change-password-form');
        const currentPasswordInput = document.getElementById('current-password');
        const newPasswordUser = document.getElementById('new-password-user');
        const confirmPasswordUser = document.getElementById('confirm-password-user');
        
        // Redeem Elements
        const redeemForm = document.getElementById('redeem-form');
        const redeemCodeInput = document.getElementById('redeem-code-input');
        const backToDashboardFromRedeemBtn = document.getElementById('back-to-dashboard-from-redeem-btn');
        const backToDashboardFromAdminRedeemBtn = document.getElementById('back-to-dashboard-from-admin-redeem-btn'); // New Admin Back button
        
        // Modal elements
        const modalTitle = document.getElementById('modal-title');
        const modalBody = document.getElementById('modal-body');
        const modalConfirmBtn = document.getElementById('modal-confirm-btn');
        const modalCancelBtn = document.getElementById('modal-cancel-btn');


        // Helper function to show a temporary message
        function showMessage(message, type = 'success') {
            messageBox.textContent = message;
            messageBox.className = `w-full text-center p-3 mb-6 rounded-lg transition-all duration-300 ease-in-out`;
            if (type === 'success') {
                messageBox.classList.add('bg-emerald-100', 'text-emerald-700');
            } else {
                messageBox.classList.add('bg-red-100', 'text-red-700');
            }
            messageBox.style.display = 'block';
            setTimeout(() => {
                messageBox.style.display = 'none';
            }, 5000);
        }

        // Custom modal to replace alerts
        const showModal = (title, body, onConfirm, onCancel) => {
            modalTitle.textContent = title;
            modalBody.textContent = body;
            confirmationModal.classList.remove('hidden');

            const confirmHandler = () => {
                confirmationModal.classList.add('hidden');
                if (onConfirm) onConfirm();
                modalConfirmBtn.removeEventListener('click', confirmHandler);
                modalCancelBtn.removeEventListener('click', cancelHandler);
            };
            const cancelHandler = () => {
                confirmationModal.classList.add('hidden');
                if (onCancel) onCancel();
                modalConfirmBtn.removeEventListener('click', confirmHandler);
                modalCancelBtn.removeEventListener('click', cancelHandler);
            };

            modalConfirmBtn.addEventListener('click', confirmHandler);
            modalCancelBtn.addEventListener('click', cancelHandler);
        };
        
        // Function to generate a random 16-digit card number with dashes
        function generateCardNumber() {
            const part1 = Math.floor(1000 + Math.random() * 9000);
            const part2 = Math.floor(1000 + Math.random() * 9000);
            const part3 = Math.floor(1000 + Math.random() * 9000);
            const part4 = Math.floor(1000 + Math.random() * 9000);
            return `${part1}-${part2}-${part3}-${part4}`;
        }

        // Function to render the correct view
        function renderView(viewName) {
            dashboardContent.style.display = 'none';
            accountSettingsView.style.display = 'none';
            redeemView.style.display = 'none';
            adminRedeemView.style.display = 'none';
            adminPanel.style.display = 'none';

            if (viewName === 'dashboard') {
                dashboardContent.style.display = 'block';
                if (currentUser.isAdmin) {
                    adminPanel.style.display = 'block';
                }
            } else if (viewName === 'accountSettings') {
                accountSettingsView.style.display = 'block';
            } else if (viewName === 'redeem') {
                redeemView.style.display = 'block';
            } else if (viewName === 'adminRedeem') {
                 adminRedeemView.style.display = 'block';
                 renderAdminRedeemCodes();
            }
        }
        
        // Function to populate the user list for the admin panel
        function renderUserList() {
            userList.innerHTML = '';
            accounts.filter(acc => !acc.isAdmin).forEach(account => {
                const userItem = document.createElement('div');
                userItem.className = 'user-list-item bg-white p-4 rounded-lg shadow-sm flex justify-between items-center';
                userItem.dataset.username = account.username;
                userItem.innerHTML = `
                    <span class="font-semibold text-slate-800">${account.username}</span>
                `;
                userList.appendChild(userItem);
            });
            // Add click listener to the list container
            userList.addEventListener('click', handleUserSelection);
        }
        
        // Helper function to check and reset daily spent amount
        function checkAndResetDailySpent(user) {
            const today = new Date().toDateString();
            if (user.lastSpendingDate !== today) {
                user.spentToday = 0;
                user.lastSpendingDate = today;
            }
        }

        // Handle user selection in the admin list
        function handleUserSelection(e) {
            const userItem = e.target.closest('.user-list-item');
            if (userItem) {
                const username = userItem.dataset.username;
                selectedUserForAdmin = accounts.find(acc => acc.username === username);
                if (selectedUserForAdmin) {
                    checkAndResetDailySpent(selectedUserForAdmin);
                    manageUsersListView.style.display = 'none';
                    manageUserView.style.display = 'block';
                    updateSelectedUserView();
                }
            }
        }

        // Function to update the details of the selected user AND their transactions
        function updateSelectedUserView() {
            if (!selectedUserForAdmin) return;
            
            manageUserTitle.textContent = `Manage ${selectedUserForAdmin.username}`;
            
            const cardStatus = selectedUserForAdmin.isCardBlocked ? 'Blocked' : 'Active';
            const limitStatus = selectedUserForAdmin.dailyLimit !== null ? `$${selectedUserForAdmin.dailyLimit.toFixed(2)}` : 'No Limit';
            const spentStatus = selectedUserForAdmin.dailyLimit !== null ? `$${selectedUserForAdmin.spentToday.toFixed(2)} of $${selectedUserForAdmin.dailyLimit.toFixed(2)}` : 'N/A';

            selectedUserDetails.innerHTML = `
                <p class="text-md font-medium text-slate-600">Username: <span class="text-slate-800">${selectedUserForAdmin.username}</span></p>
                <p class="text-md font-medium text-slate-600">Password: <span class="text-slate-800">${selectedUserForAdmin.password}</span></p>
                <p class="text-md font-medium text-slate-600">Balance: <span class="text-slate-800">$${selectedUserForAdmin.balance.toFixed(2)}</span></p>
                <p class="text-md font-medium text-slate-600">Card Number: <span class="text-slate-800">${selectedUserForAdmin.cardNumber}</span></p>
                <p class="text-md font-medium text-slate-600">Card Status: <span class="${selectedUserForAdmin.isCardBlocked ? 'text-red-600' : 'text-emerald-600'}">${cardStatus}</span></p>
                <p class="text-md font-medium text-slate-600">Daily Limit: <span class="text-slate-800">${limitStatus}</span></p>
                <p class="text-md font-medium text-slate-600">Spent Today: <span class="text-slate-800">${spentStatus}</span></p>
            `;

            toggleCardBtn.textContent = selectedUserForAdmin.isCardBlocked ? 'Unblock Card' : 'Block Card';
            dailyLimitInput.value = selectedUserForAdmin.dailyLimit;
            
            // Update the text for current daily limit
            currentLimitText.textContent = selectedUserForAdmin.dailyLimit !== null 
                ? `Current Limit: $${selectedUserForAdmin.dailyLimit.toFixed(2)}`
                : 'No daily spending limit is set.';

            adminUserTransactions.innerHTML = '';
            const sortedTransactions = [...selectedUserForAdmin.transactions].sort((a, b) => b.timestamp - a.timestamp);

            if (sortedTransactions.length === 0) {
                const noTransactions = document.createElement('p');
                noTransactions.className = 'text-center text-slate-400 mt-2';
                noTransactions.textContent = 'No transactions for this user.';
                adminUserTransactions.appendChild(noTransactions);
            } else {
                 sortedTransactions.forEach(t => {
                    const transactionItem = document.createElement('div');
                    const isCredit = t.type === 'credit';
                    const colorClass = isCredit ? 'text-emerald-600' : 'text-red-600';
                    const sign = isCredit ? '+' : '-';

                    transactionItem.className = 'flex items-center justify-between p-3 bg-white rounded-lg shadow-sm';
                    transactionItem.innerHTML = `
                        <div>
                            <p class="font-medium text-slate-800">${t.description}</p>
                            <p class="text-sm text-slate-500">${new Date(t.timestamp).toLocaleDateString()}</p>
                        </div>
                        <p class="font-bold ${colorClass}">${sign}$${t.amount.toFixed(2)}</p>
                    `;
                    adminUserTransactions.appendChild(transactionItem);
                });
            }
        }
        
        // Function to render the list of active redeem codes for the admin
        function renderAdminRedeemCodes() {
            redeemCodeList.innerHTML = '';
            
            // Filter out expired codes
            const activeCodes = redeemCodes.filter(c => !c.expirationDate || new Date(c.expirationDate) >= new Date());

            if (activeCodes.length === 0) {
                redeemCodeList.innerHTML = '<p class="text-center text-slate-400">No active codes.</p>';
                return;
            }
            
            activeCodes.forEach(code => {
                const codeItem = document.createElement('div');
                codeItem.className = 'grid grid-cols-4 items-center gap-4 bg-slate-50 p-3 rounded-lg shadow-inner text-sm';
                
                const expDate = code.expirationDate ? new Date(code.expirationDate).toLocaleDateString() : 'N/A';
                
                // Used by users list, with max 3 shown
                const usedByText = code.usedBy.length > 0 ? code.usedBy.slice(0, 3).join(', ') + (code.usedBy.length > 3 ? ` (+${code.usedBy.length - 3})` : '') : 'None';

                codeItem.innerHTML = `
                    <div class="font-semibold text-slate-700 break-all">${code.code}</div>
                    <div class="text-slate-600">$${code.prize.toFixed(2)}</div>
                    <div class="text-slate-600">${expDate}</div>
                    <div class="flex items-center space-x-2">
                        <span class="text-slate-600">${code.usedBy.length}</span>
                        <button class="delete-code-btn py-1 px-2 bg-red-500 text-white rounded-full text-xs hover:bg-red-600 transition-colors duration-200" data-code="${code.code}">Delete</button>
                    </div>
                `;
                redeemCodeList.appendChild(codeItem);
            });
            
            // Add event listeners for delete buttons
            document.querySelectorAll('.delete-code-btn').forEach(button => {
                button.addEventListener('click', (e) => {
                    const codeToDelete = e.target.dataset.code;
                    const codeIndex = redeemCodes.findIndex(c => c.code === codeToDelete);
                    if (codeIndex !== -1) {
                        redeemCodes.splice(codeIndex, 1);
                        renderAdminRedeemCodes();
                        showMessage(`Redeem code "${codeToDelete}" has been deleted.`, 'success');
                    }
                });
            });
        }

        // Function to update the dashboard UI with current user data
        function updateDashboard() {
            if (!currentUser) return;
            
            checkAndResetDailySpent(currentUser);

            welcomeName.textContent = currentUser.username.charAt(0).toUpperCase() + currentUser.username.slice(1);
            currentBalance.textContent = currentUser.balance === Infinity ? '∞' : currentUser.balance.toFixed(2);
            
            // Handle profile picture visibility
            const profilePictureElement = document.getElementById('profile-picture');
            const defaultProfileIcon = document.getElementById('default-profile-icon');
            if (currentUser.profilePicture) {
                profilePictureElement.src = currentUser.profilePicture;
                profilePictureElement.classList.remove('hidden');
                defaultProfileIcon.classList.add('hidden');
            } else {
                profilePictureElement.classList.add('hidden');
                defaultProfileIcon.classList.remove('hidden');
            }
            
            // Render user-specific transactions
            transactionHistory.innerHTML = '';
            const sortedTransactions = [...currentUser.transactions].sort((a, b) => b.timestamp - a.timestamp);

            if (sortedTransactions.length === 0) {
                const noTransactions = document.createElement('p');
                noTransactions.className = 'text-center text-slate-400 mt-8';
                noTransactions.textContent = 'No transactions yet.';
                transactionHistory.appendChild(noTransactions);
            } else {
                 sortedTransactions.forEach(t => {
                    const transactionItem = document.createElement('div');
                    const isCredit = t.type === 'credit';
                    const colorClass = isCredit ? 'text-emerald-600' : 'text-red-600';
                    const sign = isCredit ? '+' : '-';

                    transactionItem.className = 'flex items-center justify-between p-3 bg-white rounded-lg shadow-sm';
                    transactionItem.innerHTML = `
                        <div>
                            <p class="font-medium text-slate-800">${t.description}</p>
                            <p class="text-sm text-slate-500">${new Date(t.timestamp).toLocaleDateString()}</p>
                        </div>
                        <p class="font-bold ${colorClass}">${sign}$${t.amount.toFixed(2)}</p>
                    `;
                    transactionHistory.appendChild(transactionItem);
                });
            }
        }

        // Event Listeners
        
        // Handle login form submission
        loginForm.addEventListener('submit', (e) => {
            e.preventDefault();
            const username = e.target.username.value.toLowerCase();
            const password = e.target.password.value;

            const account = accounts.find(acc => acc.username === username && acc.password === password);

            if (account) {
                currentUser = account;
                loginView.style.display = 'none';
                dashboardView.style.display = 'block';
                renderView('dashboard');
                updateDashboard();
                showMessage('Login successful!', 'success');
                if (currentUser.isAdmin) {
                    renderUserList(); // Admins see user list on dashboard
                }
            } else {
                showMessage('Invalid username or password.', 'error');
            }
        });

        // Handle user menu and navigation
        profileIcon.addEventListener('click', () => {
            userMenu.classList.toggle('hidden');
        });

        // Close menu if user clicks anywhere else
        window.addEventListener('click', (e) => {
            if (!profileIcon.contains(e.target) && !userMenu.contains(e.target)) {
                userMenu.classList.add('hidden');
            }
        });
        
        // Navigation links
        accountSettingsLink.addEventListener('click', (e) => {
            e.preventDefault();
            userMenu.classList.add('hidden');
            renderView('accountSettings');
            
            // Handle profile picture visibility for settings view
            const settingsProfilePictureElement = document.getElementById('settings-profile-picture');
            const defaultSettingsIcon = document.getElementById('default-settings-icon');
            if (currentUser.profilePicture) {
                settingsProfilePictureElement.src = currentUser.profilePicture;
                settingsProfilePictureElement.classList.remove('hidden');
                defaultSettingsIcon.classList.add('hidden');
            } else {
                settingsProfilePictureElement.classList.add('hidden');
                defaultSettingsIcon.classList.remove('hidden');
            }
        });

        redeemLink.addEventListener('click', (e) => {
            e.preventDefault();
            userMenu.classList.add('hidden');
            if (currentUser.isAdmin) {
                renderView('adminRedeem');
            } else {
                renderView('redeem');
            }
        });

        // Handle logout
        const logout = () => {
            currentUser = null;
            selectedUserForAdmin = null;
            dashboardView.style.display = 'none';
            loginView.style.display = 'block';
            showMessage('You have been logged out.', 'success');
        };

        signOutLink.addEventListener('click', (e) => {
            e.preventDefault();
            logout();
        });
        
        // Back buttons
        backToDashboardBtn.addEventListener('click', () => {
            renderView('dashboard');
        });

        backToDashboardFromRedeemBtn.addEventListener('click', () => {
            renderView('dashboard');
        });
        
        backToDashboardFromAdminRedeemBtn.addEventListener('click', () => {
            renderView('dashboard');
        });

        // Account Settings Functionality
        profilePhotoInput.addEventListener('change', (e) => {
            const file = e.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (event) => {
                    const imageData = event.target.result;
                    currentUser.profilePicture = imageData;
                    updateDashboard();
                    
                    const settingsProfilePictureElement = document.getElementById('settings-profile-picture');
                    const defaultSettingsIcon = document.getElementById('default-settings-icon');
                    settingsProfilePictureElement.src = imageData;
                    settingsProfilePictureElement.classList.remove('hidden');
                    defaultSettingsIcon.classList.add('hidden');
                };
                reader.readAsDataURL(file);
                showMessage('Profile photo updated!', 'success');
            }
        });
        
        changePasswordForm.addEventListener('submit', (e) => {
            e.preventDefault();
            const currentPassword = currentPasswordInput.value;
            const newPassword = newPasswordUser.value;
            const confirmPassword = confirmPasswordUser.value;

            if (currentPassword !== currentUser.password) { showMessage('Current password is incorrect.', 'error'); return; }
            if (newPassword !== confirmPassword) { showMessage('New password and confirm password do not match.', 'error'); return; }
            if (newPassword.length < 4) { showMessage('New password must be at least 4 characters long.', 'error'); return; }
            currentUser.password = newPassword;
            changePasswordForm.reset();
            showMessage('Password has been changed successfully!', 'success');
        });
        
        // User Redeem Functionality
        redeemForm.addEventListener('submit', (e) => {
            e.preventDefault();
            const codeToRedeem = redeemCodeInput.value.trim().toUpperCase();
            
            const redeemCode = redeemCodes.find(c => c.code === codeToRedeem);
            
            if (!redeemCode) {
                showMessage('Invalid redeem code.', 'error');
                return;
            }
            
            const isExpired = redeemCode.expirationDate && new Date(redeemCode.expirationDate) < new Date();
            if (isExpired) {
                showMessage('This redeem code has expired.', 'error');
                return;
            }
            
            if (redeemCode.usedBy.includes(currentUser.username)) {
                showMessage('You have already used this code.', 'error');
                return;
            }

            currentUser.balance += redeemCode.prize;
            redeemCode.usedBy.push(currentUser.username);
            
            currentUser.transactions.push({
                type: 'credit',
                amount: redeemCode.prize,
                description: `Redeemed code: ${redeemCode.code}`,
                timestamp: Date.now()
            });

            updateDashboard();
            redeemForm.reset();
            showMessage(`Code redeemed! You received $${redeemCode.prize.toFixed(2)}.`, 'success');
        });
        
        // Admin Redeem Code Management
        createRedeemForm.addEventListener('submit', (e) => {
            e.preventDefault();
            const codeName = newRedeemCodeName.value.trim().toUpperCase();
            const prizeAmount = parseFloat(newRedeemPrizeAmount.value);
            const expDate = newRedeemExpDate.value || null;
            
            if (codeName === '' || isNaN(prizeAmount) || prizeAmount <= 0) {
                showMessage('Please enter a valid code name and prize amount.', 'error');
                return;
            }
            
            const codeExists = redeemCodes.some(c => c.code === codeName);
            if (codeExists) {
                showMessage('A code with that name already exists.', 'error');
                return;
            }
            
            redeemCodes.push({
                code: codeName,
                prize: prizeAmount,
                usedBy: [],
                expirationDate: expDate
            });
            
            createRedeemForm.reset();
            renderAdminRedeemCodes();
            showMessage(`Redeem code "${codeName}" created successfully!`, 'success');
        });

        // Existing Event Listeners (moved to the bottom for clarity)
        transferForm.addEventListener('submit', (e) => {
            e.preventDefault();
            if (!currentUser) { showMessage('Please log in first.', 'error'); return; }
            if (currentUser.isCardBlocked) { showMessage('Your card is blocked. You cannot make transfers.', 'error'); return; }
            const recipientUsername = e.target['recipient-username'].value.toLowerCase();
            const transferAmount = parseFloat(e.target['transfer-amount'].value);
            const recipientAccount = accounts.find(acc => acc.username === recipientUsername);

            if (!recipientAccount) { showMessage('Recipient not found.', 'error'); return; }
            if (recipientUsername === currentUser.username) { showMessage('You cannot transfer money to yourself.', 'error'); return; }
            if (!currentUser.isAdmin && transferAmount > currentUser.balance) { showMessage('Insufficient funds.', 'error'); return; }
            
            // Check daily spending limit
            if (currentUser.dailyLimit !== null) {
                checkAndResetDailySpent(currentUser);
                if (currentUser.spentToday + transferAmount > currentUser.dailyLimit) {
                    showMessage(`You have exceeded your daily spending limit of $${currentUser.dailyLimit.toFixed(2)}.`, 'error');
                    return;
                }
            }

            showMessage('Processing transfer...', 'success');
            setTimeout(() => {
                if (!currentUser.isAdmin) { 
                    currentUser.balance -= transferAmount; 
                    currentUser.spentToday += transferAmount;
                }
                recipientAccount.balance += transferAmount;
                const now = Date.now();
                currentUser.transactions.push({ type: 'debit', amount: transferAmount, description: `Transfer to ${recipientAccount.username}`, timestamp: now });
                recipientAccount.transactions.push({ type: 'credit', amount: transferAmount, description: `Transfer from ${currentUser.username}`, timestamp: now });
                updateDashboard();
                transferForm.reset();
                showMessage(`Successfully transferred $${transferAmount.toFixed(2)} to ${recipientAccount.username}.`, 'success');
            }, 1500);
        });
        addUserForm.addEventListener('submit', (e) => {
            e.preventDefault();
            if (!currentUser || !currentUser.isAdmin) { showMessage('Access Denied: Only administrators can add new users.', 'error'); return; }
            const newUsername = e.target['new-username'].value.toLowerCase();
            const newPassword = e.target['new-password'].value;
            const userExists = accounts.some(acc => acc.username === newUsername);
            if (userExists) { showMessage('Username already exists. Please choose a different one.', 'error'); return; }
            const newUser = { username: newUsername, password: newPassword, balance: 0.00, transactions: [], isAdmin: false, isCardBlocked: false, cardNumber: generateCardNumber(), profilePicture: null, dailyLimit: null, spentToday: 0, lastSpendingDate: null, };
            accounts.push(newUser);
            addUserForm.reset();
            updateDashboard();
            showMessage(`New user "${newUser.username}" has been added.`, 'success');
        });
        backToUsersBtn.addEventListener('click', () => {
            selectedUserForAdmin = null;
            manageUserView.style.display = 'none';
            manageUsersListView.style.display = 'block';
            renderUserList();
        });
        
        // Updated event listener for Delete User with a confirmation modal
        deleteUserBtn.addEventListener('click', () => {
            if (!selectedUserForAdmin) return;
            showModal(
                'Confirm Deletion',
                `Are you sure you want to delete the account for "${selectedUserForAdmin.username}"? This action cannot be undone.`,
                () => {
                    // Add a safeguard in the confirmation handler
                    if (!selectedUserForAdmin) { return; } 
                    const userIndex = accounts.findIndex(acc => acc.username === selectedUserForAdmin.username);
                    if (userIndex !== -1) {
                        accounts.splice(userIndex, 1);
                        backToUsersBtn.click();
                        showMessage(`User "${selectedUserForAdmin.username}" has been deleted.`, 'success');
                    }
                },
                null // No cancel handler needed for this case
            );
        });
        
        // Updated event listener for Toggle Card with a confirmation modal
        toggleCardBtn.addEventListener('click', () => {
            if (!selectedUserForAdmin) return;
            const action = selectedUserForAdmin.isCardBlocked ? 'unblock' : 'block';
            showModal(
                'Confirm Card Status Change',
                `Are you sure you want to ${action} the card for "${selectedUserForAdmin.username}"?`,
                () => {
                    // Add a safeguard in the confirmation handler
                    if (!selectedUserForAdmin) { return; }
                    selectedUserForAdmin.isCardBlocked = !selectedUserForAdmin.isCardBlocked;
                    updateSelectedUserView();
                    showMessage(`Card for "${selectedUserForAdmin.username}" has been ${selectedUserForAdmin.isCardBlocked ? 'blocked' : 'unblocked'}.`, 'success');
                },
                null
            );
        });
        
        addFundsBtn.addEventListener('click', () => {
            if (!selectedUserForAdmin) { return; }
            const amount = parseFloat(fundsAmountInput.value);
            if (isNaN(amount) || amount <= 0) { showMessage('Please enter a valid positive amount.', 'error'); return; }
            selectedUserForAdmin.balance += amount;
            selectedUserForAdmin.transactions.push({ type: 'credit', amount: amount, description: `Admin added funds`, timestamp: Date.now() });
            updateSelectedUserView();
            fundsAmountInput.value = '';
            showMessage(`$${amount.toFixed(2)} has been added to ${selectedUserForAdmin.username}'s account.`, 'success');
        });
        removeFundsBtn.addEventListener('click', () => {
            if (!selectedUserForAdmin) { return; }
            const amount = parseFloat(fundsAmountInput.value);
            if (isNaN(amount) || amount <= 0) { showMessage('Please enter a valid positive amount.', 'error'); return; }
            selectedUserForAdmin.balance -= amount;
            if (selectedUserForAdmin.balance < 0) { selectedUserForAdmin.balance = 0; }
            selectedUserForAdmin.transactions.push({ type: 'debit', amount: amount, description: `Admin removed funds`, timestamp: Date.now() });
            updateSelectedUserView();
            fundsAmountInput.value = '';
            showMessage(`$${amount.toFixed(2)} has been removed from ${selectedUserForAdmin.username}'s account.`, 'success');
        });
        
        // New event listener for setting daily limit
        setDailyLimitBtn.addEventListener('click', () => {
            if (!selectedUserForAdmin) { return; }
            const newLimit = parseFloat(dailyLimitInput.value);
            if (isNaN(newLimit) || newLimit <= 0) {
                showMessage('Please enter a valid positive limit.', 'error');
                return;
            }
            selectedUserForAdmin.dailyLimit = newLimit;
            updateSelectedUserView();
            dailyLimitInput.value = '';
            showMessage(`Daily spending limit for "${selectedUserForAdmin.username}" has been set to $${newLimit.toFixed(2)}.`, 'success');
        });
        
        // New event listener for clearing daily limit
        clearDailyLimitBtn.addEventListener('click', () => {
            if (!selectedUserForAdmin) { return; }
            selectedUserForAdmin.dailyLimit = null;
            updateSelectedUserView();
            showMessage(`Daily spending limit for "${selectedUserForAdmin.username}" has been cleared.`, 'success');
        });
        
        changePasswordBtn.addEventListener('click', () => {
            if (!selectedUserForAdmin) { return; }
            const newPassword = newPasswordInput.value;
            if (newPassword.trim() === '') { showMessage('New password cannot be empty.', 'error'); return; }
            selectedUserForAdmin.password = newPassword;
            newPasswordInput.value = '';
            updateSelectedUserView();
            showMessage(`Password for "${selectedUserForAdmin.username}" has been changed.`, 'success');
        });
    </script>
</body>
</html>

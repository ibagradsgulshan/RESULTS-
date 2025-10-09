<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Student Result Manager (Professional)</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Inter -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- PDF & Excel Generation Libraries -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.8.2/jspdf.plugin.autotable.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <!-- Chart.js for performance visualization -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f0f2f5;
        }
        /* Basic background styling */
        .body-bg {
            background-image: url('https://images.unsplash.com/photo-1481627834876-b7833e8f5570?q=80&w=2728&auto=format&fit=crop');
            background-size: cover;
            background-position: center;
            background-attachment: fixed;
        }
        /* Glassmorphism container effect */
        .main-container {
            background-color: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        /* Modal styles */
        .modal-overlay {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background-color: rgba(0, 0, 0, 0.6);
            display: flex; justify-content: center; align-items: center;
            z-index: 1000; opacity: 0; visibility: hidden;
            transition: opacity 0.3s ease, visibility 0.3s ease;
        }
        .modal-overlay.active { opacity: 1; visibility: visible; }
        .modal-container {
            background-color: white;
            padding: 2rem;
            border-radius: 0.75rem;
            box-shadow: 0 10px 25px -5px rgba(0,0,0,0.1), 0 8px 10px -6px rgba(0,0,0,0.1);
            max-height: 90vh;
            overflow-y: auto;
            transform: scale(0.95);
            transition: transform 0.3s ease;
            width: 95%;
        }
        .modal-overlay.active .modal-container { transform: scale(1); }
        /* Custom scrollbar for modals */
        .modal-container::-webkit-scrollbar { width: 8px; }
        .modal-container::-webkit-scrollbar-track { background: #f1f1f1; }
        .modal-container::-webkit-scrollbar-thumb { background: #888; border-radius: 4px; }
        .modal-container::-webkit-scrollbar-thumb:hover { background: #555; }

        /* Custom styling for filter buttons */
        .filter-btn.active {
             background-color: #3b82f6; /* blue-600 */
             color: white;
        }
        
        @media print {
            body * { visibility: hidden; }
            #final-printable, #final-printable * { visibility: visible; }
            #final-printable { position: absolute; left: 0; top: 0; width: 100%; padding: 2rem; }
            .no-print { display: none; }
        }
    </style>
</head>
<body class="body-bg flex items-center justify-center min-h-screen p-4 sm:p-6">

    <div class="w-full max-w-screen-2xl mx-auto main-container rounded-2xl shadow-xl p-6 sm:p-8">
        
        <!-- Header Section -->
        <div class="flex flex-col md:flex-row items-start md:items-center justify-between mb-8 pb-4 border-b border-gray-200">
            <div class="flex items-center gap-4">
                <div class="bg-blue-100 p-3 rounded-full">
                    <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="text-blue-600"><path d="M4 19.5v-15A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1 0-5H20"></path></svg>
                </div>
                <div>
                    <h1 class="text-2xl font-bold text-gray-800">IBAGRADS XI-XII</h1>
                    <p class="text-sm text-gray-500">Student Result Management System</p>
                </div>
            </div>
            <div class="text-left md:text-right mt-4 md:mt-0">
                <p id="currentDate" class="font-medium text-gray-700"></p>
                <p class="text-sm text-gray-500">Karachi, Sindh</p>
            </div>
        </div>
        
        <!-- Form & Subject Management in a Grid -->
        <div class="grid grid-cols-1 lg:grid-cols-12 gap-8 mb-8">
            <div class="lg:col-span-9 bg-gray-50 p-6 rounded-xl border border-gray-200">
                <h2 class="text-xl font-semibold text-gray-700 mb-4">ADD NEW RESULTS</h2>
                <form id="resultForm" class="grid grid-cols-1 md:grid-cols-4 gap-4">
                    <div class="md:col-span-2">
                        <label for="studentName" class="block text-sm font-medium text-gray-600 mb-1">Student ka Naam</label>
                        <input type="text" id="studentName" placeholder="Jaise: Anil Kumar" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                    </div>
                     <div class="md:col-span-2">
                        <label for="contactNumber" class="block text-sm font-medium text-gray-600 mb-1">Contact Number</label>
                        <input type="tel" id="contactNumber" placeholder="03xx-xxxxxxx" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition">
                    </div>
                    <div>
                        <label for="gender" class="block text-sm font-medium text-gray-600 mb-1">Gender</label>
                        <select id="gender" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                            <option value="" disabled selected>Select Gender</option>
                            <option value="Male">Male</option>
                            <option value="Female">Female</option>
                        </select>
                    </div>
                    <div>
                        <label for="studentClass" class="block text-sm font-medium text-gray-600 mb-1">Class</label>
                        <select id="studentClass" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                            <option value="" disabled selected>Select Class</option>
                            <option value="XI">XI</option>
                            <option value="XII">XII</option>
                        </select>
                    </div>
                     <div>
                        <label for="degree" class="block text-sm font-medium text-gray-600 mb-1">Program</label>
                        <select id="degree" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                            <option value="" disabled selected>Select Program</option>
                            <option value="Pre Med">Pre-Medical</option>
                            <option value="Pre Eng">Pre-Engineering</option>
                            <option value="CS">Computer Science</option>
                        </select>
                    </div>
                    <div>
                        <label for="subject" class="block text-sm font-medium text-gray-600 mb-1">Subject</label>
                        <select id="subject" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                            <option value="" disabled selected>Select a Subject</option>
                        </select>
                    </div>
                    <div>
                        <label for="testType" class="block text-sm font-medium text-gray-600 mb-1">Test Type</label>
                        <select id="testType" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                            <option value="" disabled selected>Select Type</option>
                            <option value="Weekly Test">Weekly Test</option>
                            <option value="Monthly Test">Monthly Test</option>
                            <option value="Yearly Test">Yearly Test</option>
                        </select>
                    </div>
                    <div>
                        <label for="topicName" class="block text-sm font-medium text-gray-600 mb-1">Topic ka Naam</label>
                        <input type="text" id="topicName" placeholder="Jaise: Algebra" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                    </div>
                    <div>
                        <label for="score" class="block text-sm font-medium text-gray-600 mb-1">Score</label>
                        <input type="number" id="score" placeholder="Jaise: 85" min="0" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                    </div>
                    <div>
                        <label for="totalMarks" class="block text-sm font-medium text-gray-600 mb-1">Total Marks</label>
                        <input type="number" id="totalMarks" value="100" min="1" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                    </div>
                     <div class="md:col-span-2">
                        <label for="resultDate" class="block text-sm font-medium text-gray-600 mb-1">Date</label>
                        <input type="date" id="resultDate" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                    </div>
                    <div class="md:col-span-2 mt-2 self-end">
                        <button type="submit" class="w-full bg-blue-600 text-white font-semibold py-2.5 px-4 rounded-lg hover:bg-blue-700 flex items-center justify-center gap-2 transition duration-300 shadow-md hover:shadow-lg">
                            <i data-lucide="plus-circle" class="w-5 h-5"></i> Add Result
                        </button>
                    </div>
                </form>
                <p id="errorMessage" class="text-red-500 text-sm mt-2 hidden">Please sabhi fields bharein.</p>
            </div>

            <div class="lg:col-span-3 bg-gray-50 p-6 rounded-xl border border-gray-200">
                <h2 class="text-xl font-semibold text-gray-700 mb-4">Subjects Manage Karein</h2>
                <div class="space-y-4">
                    <input type="text" id="newSubjectInput" placeholder="Naya Subject Add Karein" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition">
                    <div class="flex flex-col sm:flex-row gap-2">
                        <button id="addSubjectBtn" class="w-full bg-green-500 text-white font-semibold py-2 px-4 rounded-lg hover:bg-green-600 flex items-center justify-center gap-2 transition duration-300 shadow-md hover:shadow-lg">
                            <i data-lucide="plus" class="w-5 h-5"></i> Add
                        </button>
                        <button id="removeSubjectBtn" class="w-full bg-red-500 text-white font-semibold py-2 px-4 rounded-lg hover:bg-red-600 flex items-center justify-center gap-2 transition duration-300 shadow-md hover:shadow-lg">
                           <i data-lucide="trash-2" class="w-5 h-5"></i> Remove
                        </button>
                    </div>
                </div>
                 <p id="subjectError" class="text-red-600 text-sm mt-2 hidden"></p>
            </div>
        </div>

        <!-- Results Table Section -->
        <div class="bg-gray-50 p-6 rounded-xl border border-gray-200">
            <div class="flex flex-col md:flex-row justify-between items-center mb-4 gap-4">
                <h2 class="text-xl font-semibold text-gray-700">Uploaded Results</h2>
                <div class="w-full md:w-auto flex flex-wrap justify-center md:justify-end items-center gap-2">
                    <input type="text" id="searchInput" placeholder="Student ke naam se khojein..." class="w-full sm:w-auto px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition">
                     <div id="testTypeFilters" class="flex items-center gap-2 bg-gray-100 p-1 rounded-lg">
                        <button data-filter="All" class="filter-btn active font-semibold py-1 px-3 rounded-md transition">All</button>
                        <button data-filter="Weekly Test" class="filter-btn font-semibold py-1 px-3 rounded-md transition">Weekly</button>
                        <button data-filter="Monthly Test" class="filter-btn font-semibold py-1 px-3 rounded-md transition">Monthly</button>
                        <button data-filter="Yearly Test" class="filter-btn font-semibold py-1 px-3 rounded-md transition">Yearly</button>
                    </div>
                    <button id="showFinalResultBtn" class="bg-indigo-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-indigo-700 transition flex items-center gap-2 shadow-md hover:shadow-lg"><i data-lucide="award" class="w-5 h-5"></i> Final</button>
                    <button id="exportPdfBtn" class="bg-red-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-red-700 transition flex items-center gap-2 shadow-md hover:shadow-lg"><i data-lucide="file-text" class="w-5 h-5"></i> PDF</button>
                    <button id="exportExcelBtn" class="bg-green-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-green-700 transition flex items-center gap-2 shadow-md hover:shadow-lg"><i data-lucide="file-spreadsheet" class="w-5 h-5"></i> Excel</button>
                </div>
            </div>

            <div class="overflow-x-auto">
                <table id="resultsTable" class="min-w-full bg-white rounded-lg shadow-md overflow-hidden">
                    <thead class="bg-gray-100 text-gray-600">
                        <tr>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm whitespace-nowrap">Student Naam</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm whitespace-nowrap">Contact</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm whitespace-nowrap">Gender</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm whitespace-nowrap">Class</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm whitespace-nowrap">Program</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm whitespace-nowrap">Test Type</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm whitespace-nowrap">Subject</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm whitespace-nowrap">Score</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm whitespace-nowrap">Date</th>
                            <th class="text-center py-3 px-4 uppercase font-semibold text-sm whitespace-nowrap">Actions</th>
                        </tr>
                    </thead>
                    <tbody id="resultsTableBody" class="text-gray-700 divide-y divide-gray-200"></tbody>
                </table>
            </div>
            <div id="noResultsMessage" class="text-center py-10 text-gray-500 hidden"><p>Abhi tak koi result upload nahi hua hai.</p></div>
        </div>
    </div>

    <!-- Modals -->
    <div id="cardModal" class="modal-overlay"><div id="cardModalContainer" class="modal-container w-full max-w-2xl"></div></div>
    <div id="editModal" class="modal-overlay"><div id="editModalContainer" class="modal-container w-full max-w-lg"></div></div>
    <div id="finalResultModal" class="modal-overlay"><div id="finalResultModalContainer" class="modal-container w-full max-w-7xl"></div></div>
    <div id="confirmModal" class="modal-overlay">
        <div class="modal-container w-full max-w-sm text-center">
            <h3 id="confirmTitle" class="text-lg font-bold text-gray-800 mb-4">Confirm Action</h3>
            <p id="confirmMessage" class="text-gray-600 mb-6">Are you sure you want to proceed?</p>
            <div class="flex justify-center gap-4">
                <button id="confirmOkBtn" class="bg-red-600 text-white font-semibold py-2 px-6 rounded-lg hover:bg-red-700 transition">Yes</button>
                <button id="confirmCancelBtn" class="bg-gray-200 text-gray-800 font-semibold py-2 px-6 rounded-lg hover:bg-gray-300 transition">Cancel</button>
            </div>
        </div>
    </div>
     <div id="alertModal" class="modal-overlay">
         <div class="modal-container w-full max-w-sm text-center">
             <h3 id="alertTitle" class="text-lg font-bold text-gray-800 mb-2">Notice</h3>
             <p id="alertMessage" class="text-gray-600 mb-6"></p>
             <button id="alertOkBtn" class="bg-blue-600 text-white font-semibold py-2 px-8 rounded-lg hover:bg-blue-700 transition">OK</button>
         </div>
     </div>
    
    <script>
        document.addEventListener('DOMContentLoaded', () => {
            lucide.createIcons(); // Initialize icons
            
            // Set current date in header
            document.getElementById('currentDate').textContent = new Date().toLocaleDateString('en-GB', { day: 'numeric', month: 'long', year: 'numeric' });

            const resultForm = document.getElementById('resultForm');
            const resultsTableBody = document.getElementById('resultsTableBody');
            const noResultsMessage = document.getElementById('noResultsMessage');
            const searchInput = document.getElementById('searchInput'); 
            const subjectSelect = document.getElementById('subject');
            const newSubjectInput = document.getElementById('newSubjectInput');
            const addSubjectBtn = document.getElementById('addSubjectBtn');
            const removeSubjectBtn = document.getElementById('removeSubjectBtn');
            const subjectError = document.getElementById('subjectError');
            const testTypeFilters = document.getElementById('testTypeFilters');
            const exportPdfBtn = document.getElementById('exportPdfBtn');
            const exportExcelBtn = document.getElementById('exportExcelBtn');
            const showFinalResultBtn = document.getElementById('showFinalResultBtn');

            let currentResults = [];
            let subjects = [];
            let currentFilter = 'All';

            // --- Data Persistence ---
            const saveResultsToLocal = () => localStorage.setItem('studentResults', JSON.stringify(currentResults));
            const loadResultsFromLocal = () => currentResults = JSON.parse(localStorage.getItem('studentResults')) || [];
            const saveSubjectsToLocal = () => localStorage.setItem('studentSubjects', JSON.stringify(subjects));
            const loadSubjectsFromLocal = () => {
                subjects = JSON.parse(localStorage.getItem('studentSubjects')) || ["Physics", "Chemistry", "Maths", "English", "Biology"];
                if (subjects.length === 0) subjects = ["Physics", "Chemistry", "Maths", "English", "Biology"];
            };

            // --- Utilities ---
            const showModal = (modalId) => document.getElementById(modalId).classList.add('active');
            const hideModal = (modalId) => document.getElementById(modalId).classList.remove('active');
            
            const showAlert = (message, title = 'Notice') => {
                document.getElementById('alertTitle').textContent = title;
                document.getElementById('alertMessage').textContent = message;
                showModal('alertModal');
            };
            
            const showConfirmation = (message, onConfirm, title = 'Confirm Action') => {
                document.getElementById('confirmTitle').textContent = title;
                document.getElementById('confirmMessage').textContent = message;
                showModal('confirmModal');

                const confirmOkBtn = document.getElementById('confirmOkBtn');
                const confirmCancelBtn = document.getElementById('confirmCancelBtn');

                const handleConfirm = () => {
                    onConfirm();
                    cleanup();
                };

                const handleCancel = () => {
                    cleanup();
                };

                const cleanup = () => {
                    hideModal('confirmModal');
                    confirmOkBtn.removeEventListener('click', handleConfirm);
                    confirmCancelBtn.removeEventListener('click', handleCancel);
                };

                confirmOkBtn.addEventListener('click', handleConfirm);
                confirmCancelBtn.addEventListener('click', handleCancel);
            };

            const calculateGrade = (p) => {
                if (p >= 90) return 'A+'; if (p >= 80) return 'A'; if (p >= 70) return 'B';
                if (p >= 60) return 'C'; if (p >= 50) return 'D'; return 'F';
            };
            const getRemarks = (grade) => {
                const remarks = { 'A+': 'Outstanding', 'A': 'Excellent', 'B': 'Good', 'C': 'Satisfactory', 'D': 'Needs Improvement', 'F': 'Requires Attention' };
                return remarks[grade] || 'N/A';
            };
            const getFilteredResults = () => {
                const searchTerm = searchInput.value.toLowerCase();
                const filteredByType = currentFilter === 'All' ? currentResults : currentResults.filter(r => r.testType === currentFilter);
                return filteredByType.filter(r => r.studentName.toLowerCase().includes(searchTerm));
            };

            // --- Rendering ---
            const renderResults = () => {
                resultsTableBody.innerHTML = '';
                const displayResults = getFilteredResults();
                const sorted = [...displayResults].sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp));
                
                sorted.forEach(data => {
                    const row = resultsTableBody.insertRow();
                    row.dataset.id = data.id;
                    const percentage = data.totalMarks > 0 ? ((data.score / data.totalMarks) * 100).toFixed(2) : 'N/A';
                    row.innerHTML = `
                        <td class="py-3 px-4 font-medium whitespace-nowrap">${data.studentName}</td>
                        <td class="py-3 px-4 whitespace-nowrap">
                            <a href="#" data-id="${data.id}" class="contact-link text-blue-600 hover:underline flex items-center gap-1.5 cursor-pointer">
                                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="currentColor" class="text-green-500"><path d="M.057 24l1.687-6.163c-1.041-1.804-1.588-3.849-1.587-5.946.003-6.556 5.338-11.891 11.893-11.891 3.181.001 6.167 1.24 8.413 3.488 2.245 2.248 3.481 5.236 3.48 8.414-.003 6.557-5.338 11.892-11.893 11.892-1.99-.001-3.951-.5-5.688-1.448l-6.305 1.654zm6.597-3.807c1.676.995 3.276 1.591 5.392 1.592 5.448 0 9.886-4.434 9.889-9.885.002-5.462-4.415-9.89-9.881-9.892-5.452 0-9.887 4.434-9.889 9.886-.001 2.267.651 4.383 1.803 6.151l-1.342 4.885 4.897-1.341zM9.043 8.114c-.195-.426-.38-.435-.57-.435-.16 0-.33.004-.495.004-.21 0-.525.074-.795.372-.27.297-.995.976-.995 2.377s1.02 2.768 1.155 2.942c.135.174.995 1.596 2.404 2.247 1.15.525 1.83.84 2.32.96.49.12.89.1.96.03.07-.07.33-.135.33-.135.33-.135.33-.24.33-.305s0-.105-.03-.135c-.03-.03-.105-.06-.21-.105-.105-.045-.69-.345-.8-.385s-.195-.06-.27.06c-.075.12-.27.345-.33.405s-.12.075-.21.045c-.09-.03-.375-.12-.713-.426-.27-.24-.45-.426-.6-.6s-.105-.27-.045-.426c.06-.15.135-.195.195-.255.06-.06.105-.105.15-.165.045-.06.03-.105-.015-.165s-.27-.615-.375-.825c-.105-.21-.21-.18-.285-.18-.075 0-.165-.004-.24-.004z"></path></svg>
                                <span>${data.contactNumber || 'N/A'}</span>
                            </a>
                        </td>
                        <td class="py-3 px-4 whitespace-nowrap">${data.gender}</td>
                        <td class="py-3 px-4 whitespace-nowrap">${data.studentClass}</td>
                        <td class="py-3 px-4 whitespace-nowrap">${data.degree}</td>
                        <td class="py-3 px-4 whitespace-nowrap">${data.testType}</td>
                        <td class="py-3 px-4 whitespace-nowrap">${data.subject}</td>
                        <td class="py-3 px-4 whitespace-nowrap">${data.score} / ${data.totalMarks} (${percentage}%)</td>
                        <td class="py-3 px-4 whitespace-nowrap">${new Date(data.resultDate).toLocaleDateString()}</td>
                        <td class="py-3 px-4 whitespace-nowrap">
                            <div class="flex items-center justify-center space-x-2">
                                <button data-id="${data.id}" class="view-card-btn p-2 text-blue-600 hover:bg-blue-100 rounded-full transition"><i data-lucide="eye" class="w-5 h-5 pointer-events-none"></i></button>
                                <button data-id="${data.id}" class="edit-btn p-2 text-yellow-600 hover:bg-yellow-100 rounded-full transition"><i data-lucide="pencil" class="w-5 h-5 pointer-events-none"></i></button>
                                <button data-id="${data.id}" class="delete-btn p-2 text-red-600 hover:bg-red-100 rounded-full transition"><i data-lucide="trash-2" class="w-5 h-5 pointer-events-none"></i></button>
                            </div>
                        </td>`;
                });
                lucide.createIcons();
                noResultsMessage.classList.toggle('hidden', displayResults.length > 0);
            };

            const populateSubjectDropdowns = () => {
                const editSubjectEl = document.getElementById('editSubject');
                const mainSubjectSelect = document.getElementById('subject');
                
                [mainSubjectSelect, editSubjectEl].forEach(selectEl => {
                    if (!selectEl) return;
                    const currentValue = selectEl.value;
                    selectEl.innerHTML = '<option value="" disabled>Select a Subject</option>';
                    subjects.sort().forEach(s => {
                        const option = document.createElement('option');
                        option.value = s;
                        option.textContent = s;
                        selectEl.appendChild(option);
                    });
                    selectEl.value = currentValue;
                    if (!selectEl.value) {
                         selectEl.selectedIndex = 0;
                    }
                });
            };

            // --- Subject Management ---
            addSubjectBtn.addEventListener('click', () => {
                const newSubject = newSubjectInput.value.trim();
                if (!newSubject) {
                    subjectError.textContent = "Please enter a subject name.";
                    subjectError.classList.remove('hidden');
                    return;
                }
                if (subjects.find(s => s.toLowerCase() === newSubject.toLowerCase())) {
                    subjectError.textContent = "Subject already exists.";
                    subjectError.classList.remove('hidden');
                    return;
                }
                subjects.push(newSubject);
                saveSubjectsToLocal();
                populateSubjectDropdowns();
                newSubjectInput.value = '';
                subjectError.classList.add('hidden');
                 showAlert(`Subject "${newSubject}" has been added.`, 'Success');
            });

            removeSubjectBtn.addEventListener('click', () => {
                const selected = newSubjectInput.value.trim();
                if (!selected || !subjects.includes(selected)) {
                    subjectError.textContent = "Enter a valid, existing subject to remove.";
                    subjectError.classList.remove('hidden');
                    return;
                }
                showConfirmation(`Are you sure you want to remove the subject "${selected}"?`, () => {
                    subjects = subjects.filter(s => s !== selected);
                    saveSubjectsToLocal();
                    populateSubjectDropdowns();
                    newSubjectInput.value = '';
                    subjectError.classList.add('hidden');
                    showAlert(`Subject "${selected}" has been removed.`, 'Success');
                }, 'Remove Subject');
            });
            
            // --- Add/Edit Result Forms ---
            resultForm.addEventListener('submit', (e) => {
                e.preventDefault();
                const newResult = {
                    id: crypto.randomUUID(),
                    studentName: document.getElementById('studentName').value.trim(),
                    contactNumber: document.getElementById('contactNumber').value.trim(),
                    gender: document.getElementById('gender').value,
                    studentClass: document.getElementById('studentClass').value,
                    degree: document.getElementById('degree').value,
                    testType: document.getElementById('testType').value,
                    subject: subjectSelect.value,
                    topicName: document.getElementById('topicName').value.trim(),
                    score: parseInt(document.getElementById('score').value, 10),
                    totalMarks: parseInt(document.getElementById('totalMarks').value, 10),
                    resultDate: document.getElementById('resultDate').value,
                    timestamp: new Date().toISOString()
                };
                
                if (newResult.score > newResult.totalMarks) {
                    showAlert("Score cannot be greater than Total Marks.", "Input Error");
                    return;
                }

                currentResults.push(newResult);
                saveResultsToLocal();
                renderResults();
                resultForm.reset();
                document.getElementById('resultDate').valueAsDate = null;
                ['studentClass', 'degree', 'testType', 'subject', 'gender'].forEach(id => {
                    document.getElementById(id).selectedIndex = 0;
                });
                 showAlert('Result added successfully!', 'Success');
            });

            // --- Table Actions (View, Edit, Delete) ---
            resultsTableBody.addEventListener('click', (e) => {
                const target = e.target;
                const contactLink = target.closest('.contact-link');
                const actionButton = target.closest('button');

                if (contactLink) {
                    e.preventDefault();
                    const id = contactLink.dataset.id;
                    const resultData = currentResults.find(r => r.id === id);
                    if (resultData && resultData.contactNumber) {
                        handleShareCard(resultData, true); // true indicates direct WhatsApp share
                    }
                    return;
                }

                if (actionButton) {
                    const id = actionButton.dataset.id;
                    if (actionButton.classList.contains('delete-btn')) {
                        showConfirmation("Kya aap is result ko delete karna chahte hain?", () => {
                            currentResults = currentResults.filter(r => r.id !== id);
                            saveResultsToLocal();
                            renderResults();
                        }, "Delete Result");
                    } else if (actionButton.classList.contains('view-card-btn')) {
                        const data = currentResults.find(r => r.id === id);
                        if (data) showResultCard(data);
                    } else if (actionButton.classList.contains('edit-btn')) {
                        const data = currentResults.find(r => r.id === id);
                        if (data) showEditModal(data);
                    }
                }
            });

            // --- Professional Result Card ---
            const createProfessionalCardHTML = (data) => {
                const percentage = data.totalMarks > 0 ? ((data.score / data.totalMarks) * 100).toFixed(2) : 0;
                const grade = calculateGrade(percentage);
                const remarks = getRemarks(grade);
                const issueDate = new Date().toLocaleDateString('en-GB', { day: 'numeric', month: 'long', year: 'numeric' });
                return `
                <div id="printableCard" class="p-8 border-2 border-gray-800 bg-white relative font-sans">
                    <div class="absolute inset-0 flex items-center justify-center z-0">
                        <svg xmlns="http://www.w3.org/2000/svg" width="160" height="160" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="0.5" stroke-linecap="round" stroke-linejoin="round" class="text-gray-100">
                            <path d="M4 19.5v-15A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1 0-5H20"></path>
                        </svg>
                    </div>
                    <div class="relative z-10">
                        <div class="text-center border-b-2 border-gray-800 pb-4 mb-6">
                            <h2 class="text-3xl font-bold text-gray-800">IBAGRADS XI-XII</h2>
                            <p class="text-lg text-gray-600">Student Progress Report</p>
                        </div>
                        <div class="grid grid-cols-2 gap-x-8 gap-y-4 mb-6 text-sm">
                            <div><strong class="text-gray-600 w-24 inline-block">Student Name:</strong> ${data.studentName}</div>
                            <div><strong class="text-gray-600 w-24 inline-block">Gender:</strong> ${data.gender}</div>
                            <div><strong class="text-gray-600 w-24 inline-block">Test Type:</strong> ${data.testType}</div>
                            <div><strong class="text-gray-600 w-24 inline-block">Class:</strong> ${data.studentClass}</div>
                            <div><strong class="text-gray-600 w-24 inline-block">Subject:</strong> ${data.subject}</div>
                            <div><strong class="text-gray-600 w-24 inline-block">Program:</strong> ${data.degree}</div>
                            <div><strong class="text-gray-600 w-24 inline-block">Topic:</strong> ${data.topicName}</div>
                        </div>
                        <div class="bg-gray-50 rounded-lg p-6 mb-6">
                            <table class="w-full">
                                <thead class="border-b border-gray-300 text-gray-600">
                                    <tr>
                                        <th class="pb-2 text-left font-semibold">Criteria</th>
                                        <th class="pb-2 text-right font-semibold">Result</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr class="border-b border-gray-200"><td class="py-2">Score Obtained</td><td class="text-right py-2">${data.score}</td></tr>
                                    <tr class="border-b border-gray-200"><td class="py-2">Total Marks</td><td class="text-right py-2">${data.totalMarks}</td></tr>
                                    <tr class="font-bold"><td class="py-3">Percentage</td><td class="text-right py-3">${percentage}%</td></tr>
                                    <tr class="font-bold bg-gray-200"><td class="py-3 px-3 rounded-l-lg">Final Grade</td><td class="text-right py-3 px-3 rounded-r-lg text-xl">${grade}</td></tr>
                                </tbody>
                            </table>
                        </div>
                        <div class="text-sm">
                            <p><strong class="text-gray-600">Remarks:</strong> ${remarks}</p>
                            <p><strong class="text-gray-600">Test Date:</strong> ${new Date(data.resultDate).toLocaleDateString('en-GB')}</p>
                        </div>
                        <div class="mt-12 flex justify-between items-center text-xs text-gray-500">
                            <p>Issued on: ${issueDate}</p>
                            <div class="border-t-2 border-gray-400 border-dotted pt-1 w-40 text-center">Authorized Signature</div>
                        </div>
                    </div>
                </div>`;
            };

            const showResultCard = (data) => {
                const container = document.getElementById('cardModalContainer');
                container.innerHTML = createProfessionalCardHTML(data) + `
                    <div class="flex flex-col sm:flex-row justify-end gap-3 mt-6">
                        <button id="shareCardBtn" class="bg-green-500 text-white font-semibold py-2 px-4 rounded-lg hover:bg-green-600 flex items-center justify-center gap-2 transition"><i data-lucide="share-2"></i> Share on WhatsApp</button>
                        <button id="downloadCardPdfBtn" class="bg-red-500 text-white font-semibold py-2 px-4 rounded-lg hover:bg-red-600 flex items-center justify-center gap-2 transition"><i data-lucide="download"></i> Download PDF</button>
                        <button onclick="document.getElementById('cardModal').classList.remove('active')" class="bg-gray-200 text-gray-800 font-semibold py-2 px-4 rounded-lg hover:bg-gray-300 transition">Close</button>
                    </div>`;
                lucide.createIcons();
                document.getElementById('shareCardBtn').addEventListener('click', () => handleShareCard(data, true));
                document.getElementById('downloadCardPdfBtn').addEventListener('click', () => downloadCardAsPDF(data.studentName));
                showModal('cardModal');
            };

            const handleShareCard = (data, direct = false) => {
                const percentage = data.totalMarks > 0 ? ((data.score / data.totalMarks) * 100).toFixed(2) : 0;
                const grade = calculateGrade(percentage);
                const message = `*IBAGRADS XI-XII Result*\n\n*Student:* ${data.studentName}\n*Gender:* ${data.gender}\n*Class:* ${data.studentClass}\n*Subject:* ${data.subject}\n*Test:* ${data.testType} - ${data.topicName}\n\n*Score:* ${data.score}/${data.totalMarks}\n*Percentage:* ${percentage}%\n*Grade:* ${grade}\n\nDate: ${new Date(data.resultDate).toLocaleDateString('en-GB')}`;
                
                let whatsappUrl;
                if (direct && data.contactNumber) {
                    let phone = data.contactNumber.replace(/[^0-9]/g, '');
                    if (phone.startsWith('03')) {
                        phone = '92' + phone.substring(1);
                    }
                    whatsappUrl = `https://wa.me/${phone}?text=${encodeURIComponent(message)}`;
                } else {
                    whatsappUrl = `https://api.whatsapp.com/send?text=${encodeURIComponent(message)}`;
                }
                window.open(whatsappUrl, '_blank');
            };
            
            const downloadCardAsPDF = (studentName) => {
                const { jsPDF } = window.jspdf;
                const card = document.getElementById('printableCard');
                html2canvas(card, { scale: 2 }).then(canvas => {
                    const imgData = canvas.toDataURL('image/png');
                    const pdf = new jsPDF({
                        orientation: 'portrait',
                        unit: 'px',
                        format: [canvas.width, canvas.height]
                    });
                    pdf.addImage(imgData, 'PNG', 0, 0, canvas.width, canvas.height);
                    pdf.save(`Result-Card-${studentName}.pdf`);
                });
            };

            const showEditModal = (data) => {
                const container = document.getElementById('editModalContainer');
                container.innerHTML = `
                    <h2 class="text-xl font-semibold mb-4">Edit Result</h2>
                    <form id="editForm" class="space-y-4">
                        <input type="hidden" id="editId" value="${data.id}">
                        <div>
                            <label for="editStudentName" class="block text-sm font-medium text-gray-600 mb-1">Student Name</label>
                            <input type="text" id="editStudentName" value="${data.studentName}" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500" required>
                        </div>
                         <div>
                            <label for="editContactNumber" class="block text-sm font-medium text-gray-600 mb-1">Contact</label>
                            <input type="tel" id="editContactNumber" value="${data.contactNumber || ''}" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500">
                        </div>
                        <div>
                            <label for="editGender" class="block text-sm font-medium text-gray-600 mb-1">Gender</label>
                            <select id="editGender" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500" required>
                                <option value="Male">Male</option>
                                <option value="Female">Female</option>
                            </select>
                        </div>
                        <div>
                            <label for="editSubject" class="block text-sm font-medium text-gray-600 mb-1">Subject</label>
                            <select id="editSubject" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500" required></select>
                        </div>
                         <div>
                            <label for="editScore" class="block text-sm font-medium text-gray-600 mb-1">Score</label>
                            <input type="number" id="editScore" value="${data.score}" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500" required>
                        </div>
                        <div>
                            <label for="editTotalMarks" class="block text-sm font-medium text-gray-600 mb-1">Total Marks</label>
                            <input type="number" id="editTotalMarks" value="${data.totalMarks}" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500" required>
                        </div>
                         <div>
                            <label for="editResultDate" class="block text-sm font-medium text-gray-600 mb-1">Date</label>
                            <input type="date" id="editResultDate" value="${data.resultDate}" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500" required>
                        </div>
                        <div class="flex justify-end gap-3 pt-4">
                            <button type="button" id="cancelEdit" class="bg-gray-200 text-gray-800 font-semibold py-2 px-4 rounded-lg hover:bg-gray-300">Cancel</button>
                            <button type="submit" class="bg-blue-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-blue-700">Save Changes</button>
                        </div>
                    </form>`;
                populateSubjectDropdowns();
                document.getElementById('editSubject').value = data.subject;
                document.getElementById('editGender').value = data.gender;
                showModal('editModal');

                document.getElementById('editForm').addEventListener('submit', (e) => {
                    e.preventDefault();
                    const id = document.getElementById('editId').value;
                    const index = currentResults.findIndex(r => r.id === id);
                    if (index > -1) {
                        currentResults[index] = {
                            ...currentResults[index],
                            studentName: document.getElementById('editStudentName').value,
                            contactNumber: document.getElementById('editContactNumber').value,
                            gender: document.getElementById('editGender').value,
                            subject: document.getElementById('editSubject').value,
                            score: parseInt(document.getElementById('editScore').value, 10),
                            totalMarks: parseInt(document.getElementById('editTotalMarks').value, 10),
                            resultDate: document.getElementById('editResultDate').value
                        };
                        saveResultsToLocal();
                        renderResults();
                        hideModal('editModal');
                    }
                });
                document.getElementById('cancelEdit').addEventListener('click', () => hideModal('editModal'));
            };

            // --- Exporting ---
             exportPdfBtn.addEventListener('click', () => {
                const { jsPDF } = window.jspdf;
                const doc = new jsPDF({ orientation: 'landscape' });
                const resultsToExport = getFilteredResults();
                 if (resultsToExport.length === 0) {
                    return showAlert('No results to export.');
                }

                doc.autoTable({
                    head: [['Student Name', 'Contact', 'Gender', 'Class', 'Program', 'Test Type', 'Subject', 'Score', 'Date']],
                    body: resultsToExport.map(r => [
                        r.studentName,
                        r.contactNumber || 'N/A',
                        r.gender,
                        r.studentClass,
                        r.degree,
                        r.testType,
                        r.subject,
                        `${r.score}/${r.totalMarks}`,
                        new Date(r.resultDate).toLocaleDateString()
                    ]),
                    startY: 20,
                    theme: 'grid',
                    headStyles: { fillColor: [22, 160, 133] },
                    styles: { fontSize: 8, cellPadding: 2 },
                    didDrawPage: (data) => {
                        doc.setFontSize(18);
                        doc.text("Student Results Export", data.settings.margin.left, 15);
                    }
                });
                doc.save('student-results.pdf');
            });

            exportExcelBtn.addEventListener('click', () => {
                const resultsToExport = getFilteredResults().map(r => ({
                    "Student Name": r.studentName,
                    "Contact": r.contactNumber,
                    "Gender": r.gender,
                    "Class": r.studentClass,
                    "Program": r.degree,
                    "Test Type": r.testType,
                    "Subject": r.subject,
                    "Topic": r.topicName,
                    "Score": r.score,
                    "Total Marks": r.totalMarks,
                    "Date": new Date(r.resultDate).toLocaleDateString()
                }));
                 if (resultsToExport.length === 0) {
                    return showAlert('No results to export.');
                }
                const ws = XLSX.utils.json_to_sheet(resultsToExport);
                const wb = XLSX.utils.book_new();
                XLSX.utils.book_append_sheet(wb, ws, "Results");
                XLSX.writeFile(wb, "student-results.xlsx");
            });

             // --- Filtering ---
            testTypeFilters.addEventListener('click', (e) => {
                if (e.target.tagName === 'BUTTON') {
                    currentFilter = e.target.dataset.filter;
                    document.querySelectorAll('#testTypeFilters .filter-btn').forEach(btn => btn.classList.remove('active'));
                    e.target.classList.add('active');
                    renderResults();
                }
            });

            // --- Show Final Results ---
            showFinalResultBtn.addEventListener('click', () => {
                 const finalResults = currentResults.reduce((acc, result) => {
                    if (!acc[result.studentName]) {
                        acc[result.studentName] = { totalScore: 0, totalMarks: 0, count: 0, studentClass: result.studentClass, degree: result.degree, gender: result.gender };
                    }
                    acc[result.studentName].totalScore += result.score;
                    acc[result.studentName].totalMarks += result.totalMarks;
                    acc[result.studentName].count++;
                    return acc;
                }, {});

                const gradeDistribution = { 'A+': 0, 'A': 0, 'B': 0, 'C': 0, 'D': 0, 'F': 0 };
                let tableRows = '';
                
                Object.entries(finalResults).forEach(([name, data]) => {
                    const percentage = data.totalMarks > 0 ? (data.totalScore / data.totalMarks * 100).toFixed(2) : 0;
                    const grade = calculateGrade(percentage);
                    if (gradeDistribution.hasOwnProperty(grade)) {
                        gradeDistribution[grade]++;
                    }
                    tableRows += `
                        <tr class="border-b">
                            <td class="py-2 px-3 font-medium">${name}</td>
                            <td class="py-2 px-3">${data.gender}</td>
                            <td class="py-2 px-3">${data.studentClass}</td>
                            <td class="py-2 px-3">${data.degree}</td>
                            <td class="py-2 px-3">${data.totalScore} / ${data.totalMarks}</td>
                            <td class="py-2 px-3 font-semibold">${percentage}%</td>
                            <td class="py-2 px-3 font-bold">${grade}</td>
                        </tr>`;
                });

                const container = document.getElementById('finalResultModalContainer');
                container.innerHTML = `
                    <div id="final-printable">
                         <h2 class="text-2xl font-bold mb-6 text-center">Final Consolidated Results & Performance</h2>
                         <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
                            <div class="bg-gray-50 p-4 rounded-lg">
                                 <h3 class="text-xl font-semibold mb-2 text-center">Grade Distribution</h3>
                                 <canvas id="gradeChart"></canvas>
                            </div>
                             <div class="max-h-[50vh] overflow-y-auto">
                                <h3 class="text-xl font-semibold mb-2 text-center">Summary Table</h3>
                                <table class="w-full text-left bg-white text-sm">
                                    <thead class="bg-gray-100 sticky top-0">
                                        <tr>
                                            <th class="py-2 px-3">Student Name</th>
                                            <th class="py-2 px-3">Gender</th>
                                            <th class="py-2 px-3">Class</th>
                                            <th class="py-2 px-3">Program</th>
                                            <th class="py-2 px-3">Total Score</th>
                                            <th class="py-2 px-3">Percentage</th>
                                            <th class="py-2 px-3">Overall Grade</th>
                                        </tr>
                                    </thead>
                                    <tbody>${tableRows}</tbody>
                                </table>
                            </div>
                         </div>
                    </div>
                    <div class="flex justify-end gap-3 mt-6 no-print">
                        <button onclick="window.print()" class="bg-blue-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-blue-700 flex items-center gap-2"><i data-lucide="printer"></i> Print</button>
                        <button onclick="document.getElementById('finalResultModal').classList.remove('active')" class="bg-gray-200 text-gray-800 font-semibold py-2 px-4 rounded-lg hover:bg-gray-300">Close</button>
                    </div>`;
                lucide.createIcons();
                showModal('finalResultModal');

                const ctx = document.getElementById('gradeChart').getContext('2d');
                new Chart(ctx, {
                    type: 'bar',
                    data: {
                        labels: Object.keys(gradeDistribution),
                        datasets: [{
                            label: '# of Students',
                            data: Object.values(gradeDistribution),
                            backgroundColor: [
                                'rgba(22, 160, 133, 0.6)',
                                'rgba(46, 204, 113, 0.6)',
                                'rgba(52, 152, 219, 0.6)',
                                'rgba(241, 196, 15, 0.6)',
                                'rgba(230, 126, 34, 0.6)',
                                'rgba(231, 76, 60, 0.6)'
                            ],
                            borderColor: [
                                'rgba(22, 160, 133, 1)',
                                'rgba(46, 204, 113, 1)',
                                'rgba(52, 152, 219, 1)',
                                'rgba(241, 196, 15, 1)',
                                'rgba(230, 126, 34, 1)',
                                'rgba(231, 76, 60, 1)'
                            ],
                            borderWidth: 1
                        }]
                    },
                    options: {
                        scales: {
                            y: {
                                beginAtZero: true,
                                ticks: {
                                    stepSize: 1
                                }
                            }
                        },
                        plugins: {
                            legend: {
                                display: false
                            }
                        }
                    }
                });
            });


            // --- Modal Close Listeners ---
            document.getElementById('alertOkBtn').addEventListener('click', () => hideModal('alertModal'));
            document.querySelectorAll('.modal-overlay').forEach(overlay => {
                overlay.addEventListener('click', (e) => {
                    if (e.target === overlay) {
                        hideModal(overlay.id);
                    }
                });
            });


            // --- Initialization ---
            const init = () => {
                loadSubjectsFromLocal();
                populateSubjectDropdowns();
                loadResultsFromLocal();
                renderResults();
                searchInput.addEventListener('input', renderResults);
            };

            init();
        });
    </script>
</body>
</html>


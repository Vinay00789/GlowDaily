<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Daily Target Tracker</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        .task-item.completed .task-text,
        .task-item.completed .task-time,
        .task-item.completed .task-remarks {
            text-decoration: line-through;
            color: #9ca3af;
        }
        .progress-bar-fill {
            transition: width 0.5s ease-in-out;
        }
        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #f1f5f9; }
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
        ::-webkit-scrollbar-thumb:hover { background: #94a3b8; }
        #loading-overlay {
            z-index: 9999;
        }
    </style>
</head>
<body class="bg-slate-100 text-slate-800 antialiased">

    <!-- Loading Overlay -->
    <div id="loading-overlay" class="fixed inset-0 bg-white flex flex-col items-center justify-center transition-opacity duration-500">
        <svg class="animate-spin h-10 w-10 text-indigo-600" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
            <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
        </svg>
        <p class="mt-4 text-lg text-slate-600 font-semibold">Loading Your Profile, BOSS...</p>
    </div>

    <!-- Main Container -->
    <div class="container mx-auto max-w-4xl p-4 sm:p-6 lg:p-8 opacity-0" id="main-content">
        
        <header class="text-center mb-8">
            <h1 class="text-4xl sm:text-5xl font-bold text-slate-900">Daily Target Tracker</h1>
            <p id="current-date" class="text-slate-500 mt-2 text-lg">Seize the day, BOSS!</p>
        </header>

        <!-- Player Stats -->
        <div class="bg-white rounded-xl shadow-lg p-6 mb-8">
             <h2 class="text-xl font-semibold mb-4 text-center">Your Profile</h2>
             <div class="flex justify-around items-center text-center">
                <div>
                    <p class="text-slate-500 text-sm font-medium">LEVEL</p>
                    <p id="player-level" class="text-3xl font-bold text-indigo-600">1</p>
                </div>
                <div>
                    <p class="text-slate-500 text-sm font-medium">STREAK</p>
                    <p id="player-streak" class="text-3xl font-bold text-amber-500">0 Days</p>
                </div>
                <div>
                    <p class="text-slate-500 text-sm font-medium">USER ID</p>
                    <p id="user-id-display" class="text-xs text-slate-400 font-mono truncate w-24" title="Your unique User ID"></p>
                </div>
             </div>
        </div>

        <!-- Add Task Form -->
        <div class="bg-white rounded-xl shadow-lg p-6 mb-8">
            <h2 class="text-2xl font-semibold mb-4">Add a New Target</h2>
            <form id="task-form" class="grid grid-cols-1 md:grid-cols-2 gap-4 items-end">
                <div class="col-span-1 md:col-span-2">
                    <label for="task-input" class="block text-sm font-medium text-slate-600 mb-1">Target Description</label>
                    <input type="text" id="task-input" placeholder="What's your main objective?" class="w-full px-4 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition" required>
                </div>
                <div>
                    <label for="task-time" class="block text-sm font-medium text-slate-600 mb-1">Time</label>
                    <input type="time" id="task-time" class="w-full px-4 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition" required>
                </div>
                <div>
                    <label for="task-remarks" class="block text-sm font-medium text-slate-600 mb-1">Remarks</label>
                    <input type="text" id="task-remarks" placeholder="Any notes or details?" class="w-full px-4 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition">
                </div>
                <div class="col-span-1 md:col-span-2">
                    <button type="submit" class="w-full bg-indigo-600 text-white font-semibold py-3 px-4 rounded-lg hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 transition duration-300 transform hover:scale-105">
                        Add Target
                    </button>
                </div>
            </form>
        </div>

        <!-- Progress Bar -->
        <div class="bg-white rounded-xl shadow-lg p-6 mb-8">
            <h2 class="text-xl font-semibold mb-3">Today's Progress</h2>
            <div class="w-full bg-slate-200 rounded-full h-6">
                <div id="progress-bar-fill" class="bg-green-500 h-6 rounded-full flex items-center justify-center text-white font-bold text-sm progress-bar-fill" style="width: 0%;">
                    <span id="progress-text">0%</span>
                </div>
            </div>
             <div id="completion-message" class="text-center mt-4 text-lg font-semibold text-green-600 hidden">
                🎉 All targets completed for today! Great work, BOSS! 🎉
            </div>
        </div>

        <!-- Task List -->
        <div id="task-list-container">
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-2xl font-semibold">Your Targets</h2>
                <button id="clear-all-btn" class="text-sm text-red-500 hover:text-red-700 font-medium transition">Clear All</button>
            </div>
            <div id="task-list" class="space-y-4">
                 <p id="empty-state" class="text-center text-slate-500 py-8 bg-white rounded-xl shadow-md">Your target list is empty. Add a new target to get started!</p>
            </div>
        </div>
    </div>

    <!-- Edit Modal -->
    <div id="edit-modal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 hidden z-50">
        <div class="bg-white rounded-xl shadow-2xl p-8 w-full max-w-md">
            <h2 class="text-2xl font-semibold mb-6">Edit Target</h2>
            <form id="edit-task-form">
                <input type="hidden" id="edit-task-id">
                <div class="mb-4">
                    <label for="edit-task-input" class="block text-sm font-medium text-slate-600 mb-1">Target Description</label>
                    <input type="text" id="edit-task-input" class="w-full px-4 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition" required>
                </div>
                <div class="mb-4">
                    <label for="edit-task-time" class="block text-sm font-medium text-slate-600 mb-1">Time</label>
                    <input type="time" id="edit-task-time" class="w-full px-4 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition" required>
                </div>
                <div class="mb-6">
                    <label for="edit-task-remarks" class="block text-sm font-medium text-slate-600 mb-1">Remarks</label>
                    <input type="text" id="edit-task-remarks" class="w-full px-4 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition">
                </div>
                <div class="flex justify-end gap-4">
                    <button type="button" id="cancel-edit-btn" class="bg-slate-200 text-slate-800 font-semibold py-2 px-6 rounded-lg hover:bg-slate-300 transition">Cancel</button>
                    <button type="submit" class="bg-indigo-600 text-white font-semibold py-2 px-6 rounded-lg hover:bg-indigo-700 transition">Save Changes</button>
                </div>
            </form>
        </div>
    </div>
    
    <!-- Notification Modal -->
    <div id="notification-modal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 hidden z-50">
        <div class="bg-white rounded-xl shadow-2xl p-8 w-full max-w-sm text-center">
            <h2 id="notification-title" class="text-2xl font-bold mb-4"></h2>
            <p id="notification-message" class="text-slate-600 mb-6"></p>
            <button id="notification-close-btn" class="bg-indigo-600 text-white font-semibold py-2 px-8 rounded-lg hover:bg-indigo-700 transition">Got it</button>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
        import { getAuth, signInWithCustomToken, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js";
        import { getFirestore, doc, onSnapshot, setDoc, getDoc, updateDoc, enableIndexedDbPersistence } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";
        
        // --- PRE-DEFINED GLOBAL VARIABLES (DO NOT MODIFY) ---
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'daily-target-tracker';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : { apiKey: "your-fallback-api-key", authDomain: "...", projectId: "..." };
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : undefined;

        // --- FIREBASE & APP INITIALIZATION ---
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);

        // Enable Firestore offline persistence
        enableIndexedDbPersistence(db)
            .catch((err) => {
                if (err.code == 'failed-precondition') {
                    console.warn('Firestore persistence failed: Multiple tabs open, persistence can only be enabled in one tab at a time.');
                } else if (err.code == 'unimplemented') {
                    console.warn('Firestore persistence failed: The current browser does not support all of the features required to enable persistence.');
                }
            });
        
        let userId;
        let currentTasks = [];
        let unsubscribeUser, unsubscribeDailyData;

        // --- UI ELEMENTS ---
        const loadingOverlay = document.getElementById('loading-overlay');
        const mainContent = document.getElementById('main-content');
        const taskForm = document.getElementById('task-form');
        const taskInput = document.getElementById('task-input');
        const taskTime = document.getElementById('task-time');
        const taskRemarks = document.getElementById('task-remarks');
        const taskList = document.getElementById('task-list');
        const progressBarFill = document.getElementById('progress-bar-fill');
        const progressText = document.getElementById('progress-text');
        const emptyState = document.getElementById('empty-state');
        const completionMessage = document.getElementById('completion-message');
        const clearAllBtn = document.getElementById('clear-all-btn');
        const editModal = document.getElementById('edit-modal');
        const editTaskForm = document.getElementById('edit-task-form');
        const editTaskId = document.getElementById('edit-task-id');
        const editTaskInput = document.getElementById('edit-task-input');
        const editTaskTime = document.getElementById('edit-task-time');
        const editTaskRemarks = document.getElementById('edit-task-remarks');
        const cancelEditBtn = document.getElementById('cancel-edit-btn');
        const playerLevelEl = document.getElementById('player-level');
        const playerStreakEl = document.getElementById('player-streak');
        const userIdDisplayEl = document.getElementById('user-id-display');
        const notificationModal = document.getElementById('notification-modal');
        const notificationTitle = document.getElementById('notification-title');
        const notificationMessage = document.getElementById('notification-message');
        const notificationCloseBtn = document.getElementById('notification-close-btn');

        // --- HELPER FUNCTIONS ---
        const getTodaysDateString = () => new Date().toISOString().slice(0, 10); // YYYY-MM-DD
        const getYesterdaysDateString = () => {
            const yesterday = new Date();
            yesterday.setDate(yesterday.getDate() - 1);
            return yesterday.toISOString().slice(0, 10);
        };

        // --- UI LOGIC ---
        const updateProgress = () => {
            const completedTasks = currentTasks.filter(task => task.completed).length;
            const totalTasks = currentTasks.length;
            const progress = totalTasks > 0 ? (completedTasks / totalTasks) * 100 : 0;
            
            progressBarFill.style.width = `${progress}%`;
            progressText.textContent = `${Math.round(progress)}%`;

            completionMessage.classList.toggle('hidden', !(totalTasks > 0 && progress === 100));
        };

        const renderTasks = () => {
            taskList.innerHTML = '';
            emptyState.classList.toggle('hidden', currentTasks.length > 0);

            currentTasks.sort((a,b) => a.time.localeCompare(b.time)).forEach(task => {
                const taskItem = document.createElement('div');
                taskItem.className = `task-item bg-white rounded-xl shadow-md p-4 flex items-center gap-4 transition-all duration-300 ${task.completed ? 'completed' : ''}`;
                taskItem.dataset.id = task.id;
                taskItem.innerHTML = `
                    <div class="flex-shrink-0">
                        <input type="checkbox" class="h-6 w-6 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500 cursor-pointer" ${task.completed ? 'checked' : ''}>
                    </div>
                    <div class="flex-grow">
                        <p class="task-text font-semibold text-slate-800">${task.text}</p>
                        <div class="flex items-center gap-4 text-sm text-slate-500 mt-1">
                            <span class="task-time flex items-center gap-1">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" /></svg>
                                ${new Date('1970-01-01T' + task.time).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})}
                            </span>
                            <span class="task-remarks flex items-center gap-1 ${task.remarks ? '' : 'hidden'}">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 8h10M7 12h4m1 8l-4-4H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-3l-4 4z" /></svg>
                                ${task.remarks}
                            </span>
                        </div>
                    </div>
                    <div class="flex-shrink-0 flex gap-2">
                        <button class="edit-btn p-2 text-slate-500 hover:text-indigo-600 rounded-full hover:bg-slate-100 transition">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.232 5.232l3.536 3.536m-2.036-5.036a2.5 2.5 0 113.536 3.536L6.5 21.036H3v-3.5L15.232 5.232z" /></svg>
                        </button>
                        <button class="delete-btn p-2 text-slate-500 hover:text-red-600 rounded-full hover:bg-slate-100 transition">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" /></svg>
                        </button>
                    </div>
                `;
                taskList.appendChild(taskItem);
            });
            updateProgress();
        };
        
        const showNotification = (title, message) => {
            notificationTitle.textContent = title;
            notificationMessage.textContent = message;
            notificationModal.classList.remove('hidden');
        };

        // --- FIRESTORE LOGIC ---
        const getRefs = () => {
            const userDocRef = doc(db, 'artifacts', appId, 'users', userId);
            const dailyDataDocRef = doc(db, 'artifacts', appId, 'users', userId, 'dailyData', getTodaysDateString());
            return { userDocRef, dailyDataDocRef };
        };

        const updateRemoteTasks = async () => {
            const { dailyDataDocRef } = getRefs();
            const completedPercentage = currentTasks.length > 0 ? (currentTasks.filter(t => t.completed).length / currentTasks.length) * 100 : 0;
            await setDoc(dailyDataDocRef, { tasks: currentTasks, completedPercentage }, { merge: true });
        };
        
        async function checkAndProcessYesterday() {
            const { userDocRef } = getRefs();
            const yesterdayRef = doc(db, 'artifacts', appId, 'users', userId, 'dailyData', getYesterdaysDateString());

            const userSnap = await getDoc(userDocRef);
            const yesterdaySnap = await getDoc(yesterdayRef);

            if (!userSnap.exists() || !yesterdaySnap.exists()) return;

            const userData = userSnap.data();
            const yesterdayData = yesterdaySnap.data();

            if (yesterdayData.processed) return;

            const { completedPercentage = 0 } = yesterdayData;
            let { level, streak } = userData;
            
            if (completedPercentage < 80) {
                level = Math.max(1, level - 1);
                streak = 0;
                showNotification("Level Down!", `Your productivity was below 80% yesterday. You've been demoted to Level ${level}. Keep pushing!`);
            } else {
                streak++;
                if (streak > 0 && streak % 7 === 0) {
                    level++;
                     showNotification("Level Up! 🎉", `Incredible consistency! You've been promoted to Level ${level}. Well done, BOSS!`);
                }
            }
            
            await updateDoc(userDocRef, { level, streak });
            await updateDoc(yesterdayRef, { processed: true });
        }


        // --- EVENT LISTENERS ---
        taskForm.addEventListener('submit', async (e) => {
            e.preventDefault();
            const text = taskInput.value.trim();
            const time = taskTime.value;
            const remarks = taskRemarks.value.trim();
            if (text === '' || time === '') return;

            const newTask = { id: Date.now(), text, time, remarks, completed: false };
            currentTasks.push(newTask);
            await updateRemoteTasks();
            taskForm.reset();
        });

        taskList.addEventListener('click', async (e) => {
            const taskItem = e.target.closest('.task-item');
            if (!taskItem) return;
            const taskId = Number(taskItem.dataset.id);

            if (e.target.type === 'checkbox') {
                const task = currentTasks.find(t => t.id === taskId);
                if (task) task.completed = e.target.checked;
            } else if (e.target.closest('.delete-btn')) {
                currentTasks = currentTasks.filter(t => t.id !== taskId);
            } else if (e.target.closest('.edit-btn')) {
                const task = currentTasks.find(t => t.id === taskId);
                editTaskId.value = task.id;
                editTaskInput.value = task.text;
                editTaskTime.value = task.time;
                editTaskRemarks.value = task.remarks;
                editModal.classList.remove('hidden');
                return; // Don't update remote tasks yet
            }
            await updateRemoteTasks();
        });

        editTaskForm.addEventListener('submit', async (e) => {
            e.preventDefault();
            const taskId = Number(editTaskId.value);
            const task = currentTasks.find(t => t.id === taskId);
            if(task) {
                task.text = editTaskInput.value.trim();
                task.time = editTaskTime.value;
                task.remarks = editTaskRemarks.value.trim();
            }
            await updateRemoteTasks();
            editModal.classList.add('hidden');
        });
        
        clearAllBtn.addEventListener('click', async () => {
            currentTasks = [];
            await updateRemoteTasks();
        });

        cancelEditBtn.addEventListener('click', () => editModal.classList.add('hidden'));
        notificationCloseBtn.addEventListener('click', () => notificationModal.classList.add('hidden'));


        // --- APP STARTUP ---
        let hasProcessedInitialUser = false;

        onAuthStateChanged(auth, user => {
            if (user) {
                userId = user.uid;
                if(unsubscribeUser) unsubscribeUser();
                if(unsubscribeDailyData) unsubscribeDailyData();
                hasProcessedInitialUser = false; // Reset on user change

                const { userDocRef, dailyDataDocRef } = getRefs();

                // Set up today's tasks listener
                unsubscribeDailyData = onSnapshot(dailyDataDocRef, (docSnap) => {
                    currentTasks = docSnap.exists() ? docSnap.data().tasks || [] : [];
                    renderTasks();
                });
                
                // Set up user profile listener
                unsubscribeUser = onSnapshot(userDocRef, (docSnap) => {
                    if (docSnap.exists()) {
                        const { level = 1, streak = 0 } = docSnap.data();
                        playerLevelEl.textContent = level;
                        playerStreakEl.textContent = `${streak} Days`;
                    } else {
                        // Create user profile if it doesn't exist
                        setDoc(userDocRef, { level: 1, streak: 0, createdAt: new Date() });
                    }
                    userIdDisplayEl.textContent = userId;
                    userIdDisplayEl.title = userId;

                    if (!hasProcessedInitialUser) {
                        hasProcessedInitialUser = true;

                        // Check yesterday's progress for leveling
                        checkAndProcessYesterday();

                        // Show the main content
                        loadingOverlay.classList.add('opacity-0');
                        mainContent.classList.remove('opacity-0');
                        setTimeout(() => loadingOverlay.classList.add('hidden'), 500);
                    }
                });

            } else {
                 // No user, sign them in
                if (initialAuthToken) {
                    signInWithCustomToken(auth, initialAuthToken).catch(err => signInAnonymously(auth));
                } else {
                    signInAnonymously(auth);
                }
            }
        });
        
        // Set current date on header
        const dateElement = document.getElementById('current-date');
        const today = new Date();
        const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
        dateElement.textContent = `It's ${today.toLocaleDateString(undefined, options)}. Let's make it productive, BOSS!`;
    </script>
</body>
</html>


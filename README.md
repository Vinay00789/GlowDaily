Daily Target Tracker
🚀 Overview
The Daily Target Tracker is a modern, full-fledged web application designed to help users manage their daily tasks in a gamified and engaging way. It's not just a to-do list; it's a productivity system that tracks your consistency, rewards you for achieving your goals, and helps you build a long-term productive streak.

The application features a sleek, dark-themed "glow" UI inspired by modern web designs, providing a visually appealing and motivating user experience.

✨ Features
Task Management: Add, edit, delete, and mark daily targets as complete.

Real-time Progress: A dynamic progress bar provides immediate visual feedback on your daily achievements.

Gamified Leveling System:

Start at Level 1 and advance by staying consistent.

Level Up: Maintain a 7-day streak of high productivity to get promoted to the next level.

Level Down: If your daily completion rate drops below 80%, you get demoted, adding a challenging twist to keep you motivated.

Streak Tracking: A daily streak counter helps you visualize and build your consistency.

Secure User Profiles: Automatically creates a unique, anonymous user profile to securely store your progress and history using Firebase Authentication.

Offline First: Thanks to Firestore's IndexedDB persistence, you can add and manage your tasks even without an internet connection. Your changes will sync automatically when you're back online.

Modern & Responsive UI: The "glow" aesthetic with a dark theme and vibrant gradients looks great on all devices, from mobile phones to desktops.

Persistent Data: All your progress, levels, and task history are saved to your user profile in the cloud.

🛠️ Tech Stack
This project is built as a powerful single-file web application, demonstrating modern web capabilities without the need for a complex setup.

Frontend:

HTML5

Tailwind CSS: For a utility-first, responsive design.

JavaScript (ES6 Modules): For all the application logic and interactivity.

Backend & Database:

Google Firebase: Used as the backend-as-a-service (BaaS).

Firestore: A NoSQL database for real-time data storage and synchronization of user profiles and daily tasks.

Firebase Authentication: For handling secure, anonymous user sign-ins.

⚙️ How It Works
Anonymous Authentication: On the first visit, a new user account is created silently and anonymously. A unique userId is assigned and stored, ensuring all subsequent sessions are linked to the same profile.

Data Structure:

A users collection stores persistent data like level and streak.

A nested dailyData collection stores the task list and completion percentage for each specific day.

Leveling Logic: At the start of each new day, the app checks the previous day's completedPercentage.

If it's 80% or higher, the streak is incremented. If the streak is a multiple of 7, the level increases.

If it's below 80%, the streak is reset to 0, and the level decreases by one (to a minimum of Level 1).

Offline Sync: All data modifications are first written to a local, offline cache. When a network connection is available, the Firebase SDK automatically syncs the changes with the cloud database.

🚀 Getting Started
This is a single-file application. To run it, you just need to open the daily_target_tracker.html file in any modern web browser like Chrome, Firefox, or Edge.

To deploy it or use your own backend, you will need to:

Create a new project on Firebase.

Set up Firestore and Authentication.

In the daily_target_tracker.html file, replace the placeholder firebaseConfig object with your own project's configuration keys.

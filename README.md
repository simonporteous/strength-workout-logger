Strength Workout Logger
A simple, responsive web application to log your strength training workouts, featuring a dynamic exercise list and real-time data storage using Google Firestore.

🚀 Features
Workout Logging: Easily record your workout details including date, exercise, sets, reps, weight, and notes.

Dynamic Exercise List: Select exercises from a pre-populated dropdown list.

Add New Exercises: Option to add new custom exercises to your personal list, which are then saved and available for future use.

Real-time Data Display: View all your logged workouts in a table that updates instantly as you add new entries.

Cloud Storage: All workout data and custom exercises are securely stored in Google Firestore, accessible from any device.

User Isolation: Data is stored per user, ensuring your workout logs are private to you.

Responsive Design: Built with Tailwind CSS for a clean and mobile-friendly interface.

🛠️ Technologies Used
HTML5: Structure of the web application.

Tailwind CSS: Utility-first CSS framework for rapid styling and responsive design.

JavaScript (ES6+): Core logic for form handling, dynamic content, and Firebase integration.

Firebase:

Firestore: NoSQL cloud database for storing workout logs and exercise lists.

Firebase Authentication: Handles anonymous user authentication to secure individual user data.

⚙️ Setup and Installation
To get this application running and storing your data, you'll need to set up a Firebase project.

1. Firebase Project Setup
Create a Google Account: If you don't have one, create a free Google account.

Go to Firebase Console: Navigate to the Firebase Console.

Add Project: Click "Add project" and follow the prompts to create a new Firebase project. You can name it something like "Strength Workout Logger" or similar.

Add a Web App:

Once your project is created, click the </> (web) icon to "Add an app to get started."

Register your app. You'll be given a Firebase configuration object (e.g., firebaseConfig = { apiKey: "...", authDomain: "...", ... }). Keep this configuration handy!

Enable Firestore Database:

In the Firebase Console, go to "Build" > "Firestore Database" from the left navigation panel.

Click "Create database."

Choose "Start in production mode." (You'll define security rules next).

Select a Cloud Firestore location that is geographically close to you or your users.

Set up Firestore Security Rules:

In the Firestore Database section, go to the "Rules" tab.

Replace the default rules with the following to ensure each user can only read/write their own data:

rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Allow authenticated users to read and write their own data
    // Data is stored in paths like: artifacts/{appId}/users/{userId}/workouts
    match /artifacts/{appId}/users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}

Click "Publish" to apply the rules.

2. Update index.html with Firebase Config
The provided index.html expects Firebase configuration to be passed via global variables (__app_id, __firebase_config, __initial_auth_token) which are available in specific environments (like Google's Canvas). For self-hosting, you need to replace these with your actual Firebase project details.

Find this section in your index.html:

        // Global variables for Firebase
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');

        let app;
        let db;
        let auth;
        let userId = 'anonymous'; // Default to anonymous until authenticated

        // ... (inside onAuthStateChanged)
        try {
            if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                await signInWithCustomToken(auth, __initial_auth_token);
            } else {
                await signInAnonymously(auth);
            }
        } catch (error) {
            console.error("Error during authentication:", error);
            showMessage('Error authenticating. Data may not save.', 'red');
        }

Replace it with your actual Firebase config:

        // Your actual Firebase configuration from the Firebase Console
        const firebaseConfig = {
            apiKey: "YOUR_API_KEY",
            authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
            projectId: "YOUR_PROJECT_ID",
            storageBucket: "YOUR_PROJECT_ID.appspot.com",
            messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
            appId: "YOUR_APP_ID" // This is the 'appId' from your Firebase web app config
        };

        let app;
        let db;
        let auth;
        let userId = 'anonymous'; // Default to anonymous until authenticated

        // Initialize Firebase
        app = initializeApp(firebaseConfig);
        db = getFirestore(app);
        auth = getAuth(app);

        // Sign in anonymously to get a user ID
        signInAnonymously(auth).then((userCredential) => {
            userId = userCredential.user.uid;
            document.getElementById('userIdDisplay').textContent = userId;
            console.log("User signed in anonymously:", userId);
            setupFirestoreListeners(); // Setup listeners after auth is ready
        }).catch((error) => {
            console.error("Error during anonymous authentication:", error);
            showMessage('Error authenticating. Data may not save.', 'red');
        });

Important: Replace YOUR_API_KEY, YOUR_PROJECT_ID, etc., with the values from your Firebase project settings.

3. Hosting on GitHub Pages
Create a GitHub Repository: If you haven't already, create a new public repository on GitHub (e.g., strength-workout-logger).

Upload index.html: Upload your modified index.html file to the root of this repository.

Enable GitHub Pages:

Go to your repository's "Settings" tab.

In the left sidebar, click on "Pages."

Under "Build and deployment," select "Deploy from a branch."

Choose the main branch (or master) and / (root) folder, then click "Save."

GitHub will then provide you with a URL (e.g., https://yourusername.github.io/strength-workout-logger/) where your app will be live. It might take a few minutes for the site to deploy.

🚀 How to Use
Access the App: Open the hosted URL (e.g., your GitHub Pages URL) in your web browser on any device.

Log a Workout:

Select the Date.

Choose an Exercise from the dropdown.

If your exercise isn't listed, select -- Add New Exercise --, and an input field will appear for you to type in the new exercise name.

Enter Sets, Reps, and Weight.

Add any Notes.

Click "Log Workout."

View Workouts: Your newly logged workout will immediately appear in the "Your Logged Workouts" table below the form.

💡 Future Enhancements
Edit/Delete Workouts: Add functionality to modify or remove existing workout entries.

Filtering/Sorting: Allow users to filter workouts by date, exercise, or sort the table.

Progress Tracking: Implement charts or graphs to visualize progress over time (e.g., weight lifted per exercise, total volume).

User Accounts: Implement more robust user authentication (e.g., email/password, Google Sign-In) instead of anonymous auth.

Offline Support: Use service workers to allow the app to function offline.

📄 License
This project is open-sourced under the MIT License.

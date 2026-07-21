# To-Do List — synced across devices (no server to manage)

Same to-do app as before, but tasks now sync live across every device using the same **List Code** — powered by Firebase (Google's free cloud database). You don't host or manage any server yourself.

## 1. Create your free Firebase project (5 minutes, one-time)

1. Go to **console.firebase.google.com** and sign in with any Google account.
2. Click **Add project**, give it any name, skip Google Analytics (not needed).
3. Once created, click the **Web (`</>`)** icon to register a web app. Give it a nickname, click Register.
4. Firebase shows you a `firebaseConfig` object with keys like `apiKey`, `projectId`, etc. **Copy this whole block.**
5. In `www/index.html`, find this section near the top of the `<script>` tag:
   ```javascript
   const firebaseConfig = {
     apiKey: "YOUR_API_KEY",
     ...
   };
   ```
   Replace it with the config Firebase gave you.

## 2. Turn on Firestore (the actual database)

1. In the Firebase console sidebar, click **Firestore Database** → **Create database**.
2. Choose **Start in test mode** (allows read/write for 30 days, no login required — good enough to get started; see security note below).
3. Pick any region, click Enable.

That's it — no further backend code to write.

## 3. Try it in a browser first

Open `www/index.html` with Live Server (or any local server — Firebase's SDK needs `http://` not `file://` to work correctly). Enter any List Code (e.g. `FAMILY1`), add a task. Open the same page in a second browser tab or on your phone's browser, enter the *same* code — you'll see the task appear there too, live.

## 4. Build the APK (same as before)

```bash
npm install
npx cap init "To-Do List" "com.yourname.todoapp" --web-dir=www
npx cap add android
cd android
./gradlew assembleDebug
```

APK will be at `android/app/build/outputs/apk/debug/app-debug.apk`.

## How the syncing works

- Each List Code maps to one document in Firestore, holding the full tasks array.
- `listRef.onSnapshot(...)` is a live listener — it fires immediately with the current data, and again automatically every time *any* device changes that document. This is what makes updates appear on other devices without you writing any polling/refresh logic.
- `listRef.set({ tasks })` overwrites the document with the current tasks array whenever you add, check off, or delete a task.

## Important security note

Test-mode Firestore rules allow anyone who knows (or guesses) a List Code to read and write that list, and test mode itself expires after 30 days. This is fine for trying it out with family/friends you share a code with, but don't put sensitive data in it. Before relying on this long-term, you'd want to add proper Firebase Authentication (sign-in) and tighten the Firestore security rules — happy to help with that when you're ready.

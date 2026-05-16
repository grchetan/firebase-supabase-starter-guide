# 🔥 How to Connect Firebase + Supabase to Any Web Project
### A Complete Beginner's Guide (From Zero to Working App)

> **Who is this for?**  
> If you have never used Firebase or Supabase before — this guide is for you.  
> Every single step is explained. No experience needed.

---

## 📋 What You Will Learn

- ✅ How to create a Firebase project
- ✅ How to enable Authentication (Email/Password + Google Login)
- ✅ How to set up Firestore Database
- ✅ How to create a Supabase project
- ✅ How to create a database table in Supabase
- ✅ How to connect everything to your project using a `.env` file
- ✅ How to deploy Supabase Edge Functions
- ✅ Common errors and how to fix them

---

## 🧰 What You Need Before Starting

| Tool | Why You Need It |
|---|---|
| A Google Account | To use Firebase |
| A GitHub Account | To sign up for Supabase (easiest way) |
| Node.js installed | To run your project and Supabase CLI |
| VS Code (or any editor) | To edit your code |
| A terminal / command prompt | To run commands |

---

## 🏗️ How This Setup Works

Before we start, understand the big picture:

```
Your Web App (React / Vue / etc.)
        |
        |── Firebase ──→ Handles LOGIN (who is the user?)
        |
        └── Supabase ──→ Handles DATABASE (stores user data)
                |
                └── Edge Function ──→ Verifies Firebase login
                                      before accessing database
```

**In simple words:**
- Firebase = your security guard (checks if user is logged in)
- Supabase = your storage room (keeps all the data)
- Edge Function = the bridge between the two

---

# PART 1 — FIREBASE SETUP 🔥

---

## Step 1 — Create a Firebase Project

1. Go to 👉 [console.firebase.google.com](https://console.firebase.google.com)
2. Click **"Add project"**

   ![Firebase homepage — click Add Project](./images/firebase-01-home.png)

3. Enter your **project name** (example: `my-app`)
4. Google Analytics — you can turn it **OFF** for now → Click **"Create project"**
5. Wait a few seconds → Click **"Continue"**

---

## Step 2 — Register Your Web App

> This step gives you the "keys" to connect Firebase with your code.

1. On the Project Overview page, click the **`</>`** (Web) icon

   ![Click the web icon](./images/firebase-02-web-icon.png)

2. Enter an **App nickname** (example: `my-app-web`)
3. **Do NOT check** "Firebase Hosting"
4. Click **"Register app"**
5. You will see a `firebaseConfig` object like this:

```js
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "my-app.firebaseapp.com",
  projectId: "my-app",
  storageBucket: "my-app.firebasestorage.app",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

6. **Copy these values** — you will need them in your `.env` file
7. Click **"Continue to console"**

---

## Step 3 — Enable Authentication

> This lets users sign up and log in to your app.

1. In the left sidebar, click **"Authentication"**
2. Click **"Get started"**
3. Click the **"Sign-in method"** tab

   ![Sign-in method tab](./images/firebase-03-auth.png)

4. Click **"Email/Password"** → Toggle to **Enable** → Click **"Save"**

   ✅ Status should now show "Enabled"

5. (Optional) Click **"Google"** → Toggle to **Enable** → Select your email → Click **"Save"**

   ✅ Now users can also sign in with Google

---

## Step 4 — Create Firestore Database

> Firestore is Firebase's database. Even if you use Supabase as your main database, you may still need Firestore for some features.

1. In the left sidebar, click **"Firestore Database"**
2. Click **"Create database"**

   ![Create Firestore Database](./images/firebase-04-firestore.png)

3. Select **"Standard edition"** (it's free) → Click **"Next"**
4. Choose your **Location**:
   - If your users are in India → select **`asia-south1` (Mumbai)**
   - If your users are in USA → select `us-east1`
   - Pick the region **closest to your users** for best speed
5. Click **"Create database"**

   ✅ You will see "Your database is ready to go"

---

## Step 5 — Set Firestore Security Rules

> Rules decide who can read and write data. By default, nobody can access the database. We need to change that.

1. Click the **"Rules"** tab inside Firestore

   ![Firestore Rules tab](./images/firebase-05-rules.png)

2. Delete everything that is already written there
3. Paste this:

```js
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

4. Click **"Publish"**

   ✅ This rule means: only logged-in users can read and write data

---

# PART 2 — SUPABASE SETUP 🟢

---

## Step 6 — Create a Supabase Account

1. Go to 👉 [supabase.com](https://supabase.com)
2. Click **"Start your project"**
3. Click **"Continue with GitHub"** — this is the easiest option

   ![Supabase signup page](./images/supabase-01-signup.png)

4. Allow the permissions GitHub asks for

---

## Step 7 — Create a New Project

1. Click **"New Project"**

   ![New Project button](./images/supabase-02-new-project.png)

2. Fill in the details:
   - **Project name** → anything you like (example: `my-app`)
   - **Database password** → Click **"Generate a password"**
   
   > ⚠️ **IMPORTANT: Copy this password and save it somewhere safe (Notepad, etc.).**  
   > You will need it later. If you lose it, you will have to reset it.

   - **Region** → Pick the one closest to your users
     - India → **South Asia (Mumbai) — ap-south-1**
     - USA → us-east-1

3. Click **"Create new project"**
4. Wait 2-3 minutes for the project to be ready

   ✅ You will see your project dashboard

---

## Step 8 — Get Your Supabase API Keys

> These keys connect your code to Supabase.

1. In the left sidebar, click **"Project Settings"** (⚙️ gear icon at the bottom)
2. Click **"API Keys"**
3. Click the **"Legacy anon, service_role API keys"** tab

   ![API Keys page](./images/supabase-03-api-keys.png)

You will see two keys:

| Key Name | What it is | Where it goes |
|---|---|---|
| `anon public` | Safe to use in browser | Your `.env` file |
| `service_role` (secret) | Very powerful — never expose publicly | Supabase CLI secrets only |

4. Copy the **`anon public`** key → save it
5. Click **"Reveal"** on `service_role` → copy it → save it in a safe place

Also note your **Project URL** — it looks like:
```
https://abcdefghijklmno.supabase.co
```

---

## Step 9 — Create a Database Table

> This is where your app's data will be stored.

1. In the left sidebar, click **"Table Editor"**
2. Click **"Create a table"**

   ![Create a table](./images/supabase-04-table.png)

3. Enter the **table name** (example: `vaults`, `posts`, `todos` — whatever your app needs)

4. **Uncheck "Enable Row Level Security (RLS)"**

   > Why? Because we are using an Edge Function with a service role key to control access.  
   > The Edge Function itself checks if the user is allowed — so we don't need RLS here.

   ![Disable RLS](./images/supabase-05-rls.png)

5. Add your columns based on what your app needs. Here is an example for a password vault app:

| Column Name | Type | Nullable | Notes |
|---|---|---|---|
| `id` | `uuid` | No | Default: `gen_random_uuid()`, set as Primary Key |
| `created_at` | `int8` | No | Store as timestamp in milliseconds |
| `user_id` | `text` | No | Firebase user's UID |
| `name` | `text` | No | Name of the item |
| `reason` | `text` | Yes | Optional description |
| `unlock_at` | `int8` | No | When the item unlocks (timestamp) |
| `duration_label` | `text` | Yes | Human readable duration |
| `passwords` | `jsonb` | Yes | Store complex data as JSON |

6. Click **"Save"**

   ✅ Your table is ready. You will see "RLS disabled" badge on it.

---

# PART 3 — CONNECT EVERYTHING 🔗

---

## Step 10 — Set Up Your `.env` File

> The `.env` file stores all your secret keys. Your code reads from here.

Create a `.env` file in the root of your project:

```dotenv
# ─── FIREBASE ─────────────────────────────────
VITE_FIREBASE_API_KEY=paste_your_firebase_api_key
VITE_FIREBASE_AUTH_DOMAIN=your_project_id.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=your_project_id
VITE_FIREBASE_STORAGE_BUCKET=your_project_id.firebasestorage.app
VITE_FIREBASE_MESSAGING_SENDER_ID=your_sender_id
VITE_FIREBASE_APP_ID=your_app_id

# ─── SUPABASE ─────────────────────────────────
VITE_SUPABASE_URL=https://your_project_ref.supabase.co
VITE_SUPABASE_ANON_KEY=paste_your_anon_key_here
```

### ⚠️ Common Mistake — Wrong Variable Name

Make sure your `.env` variable name matches exactly what your code reads.

For example, if your `supabase.js` file has:
```js
const supabaseAnonKey = import.meta.env.VITE_SUPABASE_ANON_KEY
```

Then your `.env` MUST have `VITE_SUPABASE_ANON_KEY` — not `VITE_SUPABASE_PUBLISHABLE_KEY` or anything else.

### ⚠️ Add `.env` to `.gitignore`

Never upload your `.env` file to GitHub! Make sure `.gitignore` has:
```
.env
.env.local
```

---

## Step 11 — Initialize Firebase in Your Code

Create a file `src/lib/firebase.js`:

```js
import { initializeApp, getApps } from 'firebase/app'
import { getAuth } from 'firebase/auth'
import { getFirestore } from 'firebase/firestore'

const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
  authDomain: import.meta.env.VITE_FIREBASE_AUTH_DOMAIN,
  projectId: import.meta.env.VITE_FIREBASE_PROJECT_ID,
  storageBucket: import.meta.env.VITE_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: import.meta.env.VITE_FIREBASE_MESSAGING_SENDER_ID,
  appId: import.meta.env.VITE_FIREBASE_APP_ID,
}

// Prevent initializing twice
const app = getApps().length === 0 ? initializeApp(firebaseConfig) : getApps()[0]

export const auth = getAuth(app)
export const db = getFirestore(app)
```

---

## Step 12 — Initialize Supabase in Your Code

Create a file `src/lib/supabase.js`:

```js
import { createClient } from '@supabase/supabase-js'

const supabaseUrl = import.meta.env.VITE_SUPABASE_URL
const supabaseAnonKey = import.meta.env.VITE_SUPABASE_ANON_KEY

export const supabase = createClient(supabaseUrl, supabaseAnonKey)
```

---

# PART 4 — SUPABASE CLI & EDGE FUNCTIONS ⚡

> Edge Functions are server-side functions that run on Supabase's servers.  
> Use them when you need to do something securely that users should not be able to bypass.

---

## Step 13 — Install Supabase CLI

The Supabase CLI is a tool you use in your terminal to deploy Edge Functions.

**On Windows:**
```bash
# Option 1: Using Scoop (recommended)
scoop install supabase

# Option 2: Download the .exe directly from:
# https://github.com/supabase/cli/releases
```

> ⚠️ **Do NOT use `npm install -g supabase` on Windows**  
> It will give this error:  
> `"Installing Supabase CLI as a global module is not supported"`  
> Use Scoop or direct download instead.

**On Mac:**
```bash
brew install supabase/tap/supabase
```

**On Linux:**
```bash
# Using npm (works on Linux)
npm install -g supabase
```

**Check if installed:**
```bash
supabase --version
```

---

## Step 14 — Login to Supabase CLI

```bash
supabase login
```

This will:
1. Open your browser automatically
2. Show a Supabase login page
3. Click **"Allow"**
4. Come back to terminal — you will see: `"You are now logged in. Happy coding!"` ✅

---

## Step 15 — Link Your Project

This connects your local project folder to your Supabase project.

```bash
supabase link --project-ref your_project_ref
```

> Your `project_ref` is in your Supabase URL:  
> `https://abcdefghijklmno.supabase.co` → ref is `abcdefghijklmno`

It may ask for your **database password** — enter the one you saved in Step 7.

✅ You will see: `"Finished supabase link."`

---

## Step 16 — Set Secrets for Your Edge Function

Secrets are environment variables for your Edge Function. They are stored securely on Supabase's servers — not in your code.

```bash
# Your Firebase API key (same as VITE_FIREBASE_API_KEY in .env)
supabase secrets set FIREBASE_API_KEY=your_firebase_api_key

# Your Supabase service role key (the secret one from Step 8)
supabase secrets set SB_SERVICE_ROLE_KEY=your_service_role_key
```

✅ Each command should say: `"Finished supabase secrets set."`

---

## Step 17 — Deploy Your Edge Function

Your Edge Function code should be in:
```
supabase/functions/your_function_name/index.ts
```

Deploy it with:

```bash
supabase functions deploy your_function_name --no-verify-jwt
```

> ⚠️ **The `--no-verify-jwt` flag is very important!**  
> Without it, Supabase will try to verify its own JWT format.  
> But if your function uses Firebase tokens, this will fail with:  
> `"UNAUTHORIZED_ASYMMETRIC_JWT"` error.  
> Always add `--no-verify-jwt` when using Firebase tokens with Supabase Edge Functions.

✅ You will see:
```
Uploading asset: supabase/functions/your_function_name/index.ts
Deployed Functions on project your_ref: your_function_name
```

---

# PART 5 — COMMON ERRORS & FIXES 🐛

---

### ❌ Error: `UNAUTHORIZED_ASYMMETRIC_JWT` / `Invalid JWT`

**When:** After login, data doesn't load — error shows in the app

**Why:** Edge Function was deployed without `--no-verify-jwt`

**Fix:**
```bash
supabase functions deploy your_function_name --no-verify-jwt
```

---

### ❌ Error: `npm error — Installing Supabase CLI as a global module is not supported`

**When:** Running `npm install -g supabase` on Windows

**Why:** Supabase CLI is not an npm package on Windows

**Fix:** Use Scoop instead:
```bash
scoop install supabase
```

---

### ❌ Error: Data not loading, no error message shown

**When:** App runs but database returns nothing

**Why:** Variable name mismatch in `.env` file

**Fix:** Check your `.env` file. The variable name must match exactly what your code reads.

Example — if your code reads `VITE_SUPABASE_ANON_KEY`:
```dotenv
# ❌ Wrong
VITE_SUPABASE_PUBLISHABLE_KEY=eyJ...

# ✅ Correct
VITE_SUPABASE_ANON_KEY=eyJ...
```

Then restart your dev server:
```bash
# Stop server: Ctrl + C
npm run dev
```

---

### ❌ Error: Firebase — `auth/configuration-not-found`

**When:** Trying to login but Firebase throws this error

**Why:** Authentication was not enabled in Firebase console

**Fix:**
1. Go to Firebase Console → Authentication → Sign-in method
2. Enable Email/Password (and Google if needed)

---

### ❌ Error: Firestore — `permission-denied`

**When:** Trying to read/write Firestore and getting permission error

**Why:** Firestore security rules are blocking access

**Fix:** Go to Firestore → Rules and make sure you have published these rules:
```js
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

---

### ❌ Error: `supabase link` asks for password and fails

**When:** Running `supabase link --project-ref ...`

**Why:** Wrong database password entered

**Fix:**
1. Go to Supabase Dashboard → Project Settings → Database
2. Click **"Reset database password"**
3. Copy the new password
4. Run `supabase link` again with the new password

---

# PART 6 — UNDERSTANDING YOUR KEYS 🔑

This is important. Different keys go to different places.

| Key | Where to Put It | Why |
|---|---|---|
| Firebase `apiKey` | `.env` file | Used in browser to connect Firebase |
| Firebase `authDomain` | `.env` file | Used for login popup/redirect |
| Supabase `anon key` | `.env` file | Safe to use in browser, respects RLS |
| Supabase `service_role key` | Supabase CLI secret ONLY | Bypasses RLS — NEVER put in frontend |
| Firebase `apiKey` (again) | Supabase CLI secret | Edge Function uses it to verify Firebase tokens |

### 🚨 Golden Rules for Keys

1. **Never commit `.env` to GitHub** — add it to `.gitignore`
2. **Never put `service_role` key in your frontend code** — it can bypass all security
3. **Never share your keys publicly** — rotate (regenerate) them immediately if exposed
4. **Use environment variables** — never hardcode keys directly in your `.js` files

---

# PART 7 — FINAL CHECKLIST ✅

Use this every time you set up a new project:

### Firebase
- [ ] Created Firebase project
- [ ] Registered web app and copied `firebaseConfig`
- [ ] Enabled Email/Password authentication
- [ ] (Optional) Enabled Google authentication
- [ ] Created Firestore database (correct region)
- [ ] Published Firestore security rules

### .env File
- [ ] Added all Firebase keys with correct variable names
- [ ] Added `VITE_SUPABASE_URL`
- [ ] Added `VITE_SUPABASE_ANON_KEY` (not publishable key!)
- [ ] Added `.env` to `.gitignore`

### Supabase
- [ ] Created account and new project
- [ ] Saved database password somewhere safe
- [ ] Copied `anon public` key → `.env`
- [ ] Copied `service_role` key → saved safely (for CLI use)
- [ ] Created database table with correct columns
- [ ] Disabled RLS on the table (if using Edge Functions for auth)

### Supabase CLI
- [ ] Installed CLI (`scoop install supabase` on Windows)
- [ ] Logged in: `supabase login`
- [ ] Linked project: `supabase link --project-ref your_ref`
- [ ] Set `FIREBASE_API_KEY` secret
- [ ] Set `SB_SERVICE_ROLE_KEY` secret
- [ ] Deployed edge function with `--no-verify-jwt` flag

### Test
- [ ] `npm run dev` runs without errors
- [ ] Can sign up / log in
- [ ] Data loads correctly from database
- [ ] Creating new records works

---

## 🙌 You Did It!

If you followed all the steps above, your project is now connected to Firebase and Supabase.

**Stuck somewhere?** Check Part 5 (Common Errors) first. Most problems are either:
- A wrong variable name in `.env`
- Missing `--no-verify-jwt` flag
- Authentication not enabled in Firebase
- Wrong key in wrong place

---

*Guide written by: grchetan*  
*Last updated: May 2026*  
*Stack used: React + Vite + Firebase Auth + Firestore + Supabase (PostgreSQL + Edge Functions)*

---

> 💡 **Tip for beginners:** The first time is always the hardest. The second time takes 15 minutes. Save this guide, come back to it whenever you start a new project.

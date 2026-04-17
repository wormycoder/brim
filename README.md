# brim. — buy and sell things you love

**Live URL:** https://wormycoder.github.io/brim/

A clean, community-first marketplace. Sellers list free, buyers message them directly, payments go through Ko-fi.

---

## Pages

| File | Status | Description |
|------|--------|-------------|
| `index.html` | ✅ Done | Homepage — hero, categories, featured listings |
| `browse.html` | ✅ Done | Full browse with search, filters, category pills |
| `product.html` | ✅ Done | Product detail — gallery, buy via Ko-fi, message seller |
| `login.html` | ✅ Done | Sign in / sign up (email + Google) |
| `profile.html` | ✅ Done | Seller profile — listings, reviews, stats |
| `sell.html` | ✅ Done | List an item — photo upload, live preview |
| `messages.html` | ✅ Done | Inbox + real-time chat UI |

---

## Deploying to GitHub Pages

All files go directly into your repo root (or a folder). Since you're using `github.com/wormycoder/brim`:

1. Push all HTML files to the `main` branch of your repo
2. Go to **Settings → Pages**
3. Source: **Deploy from a branch** → `main` → `/ (root)`
4. Click Save — your site will be live at `https://wormycoder.github.io/brim/` in ~1 minute

```bash
# If starting fresh
git clone https://github.com/wormycoder/brim
cd brim
# copy all HTML files here
git add .
git commit -m "add all brim pages"
git push
```

---

## Firebase Setup (enables login, listings, messages)

### Step 1 — Create project
1. Go to [console.firebase.google.com](https://console.firebase.google.com)
2. Create project → name it **brim**
3. Add a **Web App** → copy the config object

### Step 2 — Enable services
- **Authentication** → Sign-in methods → enable **Email/Password** and **Google**
- **Firestore Database** → Create database → start in **test mode**
- **Storage** → Get started → start in **test mode**

### Step 3 — Add config to each page

At the bottom of each HTML file, replace the `// TODO: Firebase` comment with:

```html
<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/10.0.0/firebase-app.js";
  import { getAuth, onAuthStateChanged, signInWithEmailAndPassword, createUserWithEmailAndPassword, GoogleAuthProvider, signInWithPopup } from "https://www.gstatic.com/firebasejs/10.0.0/firebase-auth.js";
  import { getFirestore, collection, addDoc, getDocs, getDoc, doc, setDoc, query, where, orderBy, onSnapshot, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.0.0/firebase-firestore.js";
  import { getStorage, ref, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.0.0/firebase-storage.js";

  const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "your-app.firebaseapp.com",
    projectId: "your-app",
    storageBucket: "your-app.appspot.com",
    messagingSenderId: "123456789",
    appId: "1:123456789:web:abc123"
  };

  const app = initializeApp(firebaseConfig);
  const auth = getAuth(app);
  const db = getFirestore(app);
  const storage = getStorage(app);
</script>
```

---

## Firestore Rules (when ready to go public)

Replace test mode rules with:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Anyone can read listings
    match /listings/{id} {
      allow read: if true;
      allow write: if request.auth != null && request.auth.uid == resource.data.sellerId;
      allow create: if request.auth != null;
    }
    // Users can only edit their own profile
    match /users/{userId} {
      allow read: if true;
      allow write: if request.auth != null && request.auth.uid == userId;
    }
    // Messages: only buyer and seller can read/write
    match /messages/{convId} {
      allow read, write: if request.auth != null &&
        (request.auth.uid == resource.data.buyerId || request.auth.uid == resource.data.sellerId);
      match /thread/{msgId} {
        allow read, write: if request.auth != null;
      }
    }
    // Reviews: anyone can read, must be logged in to write
    match /reviews/{id} {
      allow read: if true;
      allow create: if request.auth != null;
    }
  }
}
```

---

## Tech Stack

- **HTML/CSS/JS** — no build tools, works directly on GitHub Pages
- **Firebase** (free Spark plan) — Auth + Firestore + Storage
- **Ko-fi** (free) — handles all payments, sellers keep control
- **Fonts** — DM Sans + Instrument Serif (Google Fonts)

---

## Design tokens

```css
--blue: #378ADD;
--blue-light: #E6F1FB;
--blue-border: #B5D4F4;
--blue-dark: #185FA5;
```

Style: clean, editorial, minimal. 0.5px borders everywhere. No gradients. Instrument Serif for headings, DM Sans for everything else.

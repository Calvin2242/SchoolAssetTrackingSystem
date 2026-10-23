# School Asset Tracker

A simple web app for recording school assets and where they are kept. Staff log in with an email and password, then add, edit, search and delete assets. Each asset has a **code**, a **name** and a **location**.

- **Frontend:** a single `index.html` file, hosted on GitHub Pages
- **Login:** Firebase Authentication (email and password)
- **Database:** Cloud Firestore

## Features

- Login required before any data is shown
- Add, edit and delete assets
- Search by name, code or location
- Live updates: changes appear on every open device without refreshing
- Duplicate asset codes are blocked
- Records who created or last changed each asset, and when
- Works on phones, tablets and desktops, with light and dark mode

## Repository contents

```
index.html   The whole app (HTML, CSS and JavaScript)
README.md    This file
```

## Setup

### 1. Create a Firebase project

1. Go to the [Firebase console](https://console.firebase.google.com) and select **Add project**.
2. Open **Project settings**, scroll to **Your apps**, and select the web icon (`</>`) to register a web app.
3. Copy the `firebaseConfig` values shown.

### 2. Add your config to the app

Open `index.html` and replace the placeholder values in the `firebaseConfig` block near the top of the `<script>` section:

```js
const firebaseConfig = {
  apiKey: "...",
  authDomain: "...",
  projectId: "...",
  storageBucket: "...",
  messagingSenderId: "...",
  appId: "..."
};
```

These values identify your project and are safe to commit to a public repository. Your data is protected by the security rules in step 4, not by keeping this config secret.

### 3. Turn on login

1. In the Firebase console, go to **Build → Authentication → Sign-in method**.
2. Enable **Email/Password**.
3. Go to the **Users** tab and select **Add user** to create an account for each staff member.

There is no public sign-up page. Only people you add here can log in.

### 4. Create the database and security rules

1. Go to **Build → Firestore Database** and select **Create database**. Choose a location close to your school.
2. Open the **Rules** tab, paste the following, and select **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /assets/{id} {
      allow read, write: if request.auth != null;
    }
  }
}
```

This allows only logged-in users to read or change assets. Do not leave the database in "test mode", which lets anyone read and write.

### 5. Deploy to GitHub Pages

1. Create a GitHub repository and upload `index.html` and `README.md`.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, set the source to **Deploy from a branch**, choose `main` and the `/ (root)` folder, then select **Save**.
4. After a minute or two, your site is live at `https://YOUR-USERNAME.github.io/REPO-NAME/`.

### 6. Authorise your site's domain

Firebase blocks logins from unknown domains.

1. Go to **Authentication → Settings → Authorized domains**.
2. Select **Add domain** and enter `YOUR-USERNAME.github.io`.

## Using the app

| Task | How |
| --- | --- |
| Log in | Enter the email and password created for you in Firebase |
| Add an asset | Select **Add asset**, then enter the code, name and location |
| Edit an asset | Select **Edit** on its row. You can change the name and location |
| Delete an asset | Select **Delete** on its row and confirm |
| Find an asset | Type in the search box. It matches the code, name and location |
| Log out | Select **Log out** at the top right |

**About asset codes:** Each code must be unique. Codes are saved in uppercase, spaces become hyphens, and a code cannot contain `/`. A code can't be changed after the asset is created. To change one, delete the asset and add it again.

## Data structure

Each asset is stored in the `assets` collection, and the document ID is the asset code.

| Field | Description |
| --- | --- |
| `code` | Unique asset code, for example `LAP-0042` |
| `name` | Asset name, for example `Dell Latitude laptop` |
| `location` | Where the asset is kept, for example `Computer Lab 2` |
| `createdAt`, `createdBy` | When the asset was added, and by whom |
| `updatedAt`, `updatedBy` | Set when the asset is edited |

## Managing users

- **Add a user:** Authentication → Users → Add user
- **Reset a password:** select the three dots beside the user, then **Reset password**
- **Remove access:** select the three dots beside the user, then **Delete account** or **Disable account**

## Running locally

Firebase login does not work when opening the file directly (`file://`), so use a local server:

```
python3 -m http.server 8000
```

Then open `http://localhost:8000`. `localhost` is already an authorised domain in Firebase by default.

## Troubleshooting

| Problem | Likely cause and fix |
| --- | --- |
| "Email or password is incorrect" | Check the user exists under Authentication → Users |
| Login fails with an unauthorized-domain error | Add your `github.io` domain to Authorized domains (step 6) |
| "Missing or insufficient permissions" | The Firestore rules were not published, or the user is not logged in (step 4) |
| Page shows the login box but nothing happens | The `firebaseConfig` values are still placeholders (step 2) |
| Changes to the site don't appear | GitHub Pages can take a few minutes to update. Hard refresh with Ctrl+Shift+R |

## Limits and notes

- Every logged-in user can add, edit and delete. There are no read-only roles.
- Firebase's free tier is generally enough for a school's asset list, but check [Firebase pricing](https://firebase.google.com/pricing) for current limits.
- Deleted assets are removed permanently. Consider exporting your data regularly from the Firebase console.
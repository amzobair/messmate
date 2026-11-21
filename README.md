# MessMate

**Flats, mess seats and private tuition around the IIUC campus, posted by students for students.**

MessMate is a frontend web application built for **CSE-2340: Software Development 1** (International Islamic University Chittagong, Department of CSE). Students who come to Chattogram from other districts often cannot find a flat or a mess seat, and students who want to teach (or need a tutor for a relative) have no place to meet. MessMate puts all of it in one place: post a vacant flat or seat, post a tuition opening, send an interest request, and talk to the owner in a built-in chat.

| | |
|---|---|
| **Live site** | https://messmate-iiuc.web.app |
| **GitHub** | https://github.com/amzobair/messmate |
| **Team** | Team G-11 |

## Team and module ownership

| Member | Student ID | Name | Module |
|---|---|---|---|
| Member 1 | C253149 | Abdullah Mohammad Sajid | Module 1: Housing & Mess Listings |
| Member 2 | C2531451 | Emam Hosaine Emon | Module 2: Tutor Board |
| Member 3 | C253150 | Abdullah Mohammad Zobair | Module 3: Dashboard & Connect Hub (including chat) |

The three modules are one application, not three mini-projects. They share the signed-in user, the Firestore collections and a few reusable components (see [How the modules connect](#how-the-modules-connect)).

## Features

### Module 1: Housing & Mess Listings (`/housing`)
- Create, edit, delete and view listings: flat / mess seat / sublet, with area, address, rent, seats, who it suits (anyone / male / female / family), facilities, description and contact phone.
- Search, filter by type, area, gender and rent range, and sort (newest, rent low to high, rent high to low).
- **Optional photos:** add up to 3 photos to a listing, or none at all. Photos are resized and compressed in the browser, shown as a cover image with a photo count on the card, and as a small gallery in the details dialog.
- Owners can mark a listing **Available / Taken**.
- Details dialog, **I am interested** button and **Save** (bookmark) for other users.
- Last used filters are remembered in `localStorage`.

### Module 2: Tutor Board (`/tutors`)
- Create, edit, delete and view tuition posts: **Need a tutor** or **Offering tutoring**, with class level, subject, salary, days per week, area and contact.
- Tabs for the post type, plus search and filters for area, class level and minimum salary.
- Owners can mark a post **Open / Filled**.
- Same interest and save actions as housing.

### Module 3: Dashboard & Connect Hub (`/`)
- Overview cards (my listings, my tuition posts, pending requests, saved items) with an animated count.
- **Requests** tab: requests I received (accept or decline) and requests I sent (withdraw; the owner's phone is shown once accepted).
- **Messages** tab: a messenger-style chat between the sender and the post owner. Send text and links, **share my phone** with one click, or **share a location** (opens Google Maps for the place). Chat opens as soon as an interest request is sent and becomes read-only if the request is declined.
- **My posts**, **Saved** and **Profile** tabs.
- **Weather in Chattogram** from a public API, and a **rent share calculator**.

### Across the app
- Email/password sign up and sign in, protected routes, sign out.
- English and Bangla interface (language button), light and dark theme.
- Toast notifications, shimmer loading placeholders, loading / error / empty states.
- Phone-first layout: a bottom navigation bar on phones, foldable filters, thumb-sized buttons and 16px form fields (no zoom on iOS), a compact login page, and notch-safe spacing. From tablet width up the navigation moves to the top bar.
- Keyboard-friendly, accessible forms.
- Motion on buttons, cards, dialogs and pages (disabled automatically for users who prefer reduced motion).

## Pages / routes

| Route | Page | Access |
|---|---|---|
| `/login` | Sign in and registration | Public |
| `/` | Dashboard & Connect Hub (Requests, Messages, My posts, Saved, Profile) | Protected |
| `/housing` | Housing & Mess Listings | Protected |
| `/tutors` | Tutor Board | Protected |
| `*` | Not-found page | Public |

Visitors who are not signed in are redirected to `/login`. Forms, dialogs and chat do not need their own routes. `/?chat=<requestId>` opens the Messages tab on a conversation.

## Tech stack

- **React 19** and **Vite**, **React Router** (Routes, NavLink, Outlet, protected routes, `useSearchParams`)
- **Tailwind CSS v4** and **ShadCN/ui-style components** (Button, Card, Dialog, Badge, Tabs, Table) built on Radix UI
- **Firebase Authentication** (email/password) and **Cloud Firestore**
- **Open-Meteo** public API through `fetch()` for the weather card
- **localStorage** for theme, language and last used filters
- `lucide-react` icons, Fraunces / Plus Jakarta Sans / Noto Serif Bengali / Hind Siliguri fonts

### Syllabus concepts used

| Concept | Where |
|---|---|
| Components, props, children, lists, conditional rendering | everywhere (`PostCard`, `StatusBadge`, tables, dialogs) |
| State, effects, immutable updates, lifting state up | filters, forms, dashboard tab and selected chat |
| Controlled forms and validation | `src/lib/validators.js`, all forms |
| Context | auth, preferences (theme / language), requests, toasts |
| Array methods, destructuring, spread, optional chaining | filtering and sorting in the pages, `useMemo` summaries |
| `async` / `await`, `try` / `catch` / `finally`, `fetch()` | `WeatherCard`, Firestore writes |
| Routing | `src/App.jsx`, `ProtectedRoute`, `AppLayout` with `Outlet` |
| Firestore reads and writes, security rules | `src/services/db.js`, `firestore.rules` |

## How the modules connect

All modules use the signed-in Firebase user and these Firestore collections:

| Collection | Written by | Read by |
|---|---|---|
| `listings` | Module 1 | Module 1, Module 3 |
| `tuitions` | Module 2 | Module 2, Module 3 |
| `requests` | the **I am interested** button in Modules 1 and 2 | Module 3 (inbox, outbox, chat list) |
| `requests/{id}/messages` | chat in Module 3 | only the two people on the request |
| `users` | registration and the Profile tab | all modules (name, phone, area, saved items) |

Shared pieces: `PostCard`, `InterestButton`, `SaveButton`, `StatusBadge`, `DetailsDialog`, `ConfirmDeleteDialog`, `FormField`, and the `RequestsContext` that keeps the user's requests live for every page.

## Project structure

```
src/
  components/
    ui/          ShadCN-style Button, Card, Dialog, Badge, Tabs, Table, inputs
    layout/      AppLayout (header, nav, footer), ProtectedRoute
    shared/      reusable pieces used by several modules
  context/       Auth, Preferences (theme/language), Requests, Toast
  features/
    housing/     Module 1
    tutors/      Module 2
    dashboard/   Module 3
    chat/        messenger inside the dashboard
  hooks/         useCollection, useMessages
  lib/           firebase.js, constants (areas), i18n, validators, formatting
  pages/         LoginPage, NotFoundPage
  services/      db.js (Firestore create / update / delete / chat helpers)
firestore.rules  Firestore security rules (paste into the Firebase console)
```

## Getting started

### Requirements
- Node.js 20 or newer and npm
- A free Firebase project

### 1. Install

```bash
npm install
```

### 2. Create the Firebase project

1. Open the [Firebase console](https://console.firebase.google.com/) and create a project.
2. **Authentication**: click *Get started*, open *Sign-in method*, enable **Email/Password**.
3. **Firestore Database**: click *Create database*, choose a location near you, and start in **production mode**.
4. **Rules**: open *Firestore Database, Rules*, paste the contents of [`firestore.rules`](firestore.rules) and click **Publish**.
5. **Web app**: in *Project settings, Your apps*, add a **Web app** and copy the config values.

### 3. Add the Firebase configuration

Copy the example file and fill in the values from the step above:

```bash
cp .env.example .env
```

```env
VITE_FIREBASE_API_KEY=
VITE_FIREBASE_AUTH_DOMAIN=
VITE_FIREBASE_PROJECT_ID=
VITE_FIREBASE_STORAGE_BUCKET=
VITE_FIREBASE_MESSAGING_SENDER_ID=
VITE_FIREBASE_APP_ID=
```

`.env` is listed in `.gitignore` and must never be committed. These are standard Firebase web-app values, not service-account secrets. Do not create or commit a service-account key; this project does not need one. If the values are missing, the app shows a "Firebase is not configured yet" notice instead of crashing.

### 4. Run

```bash
npm run dev
```

Open the address shown in the terminal (usually `http://localhost:5173`) and create an account on the login page.

### Other scripts

```bash
npm run build     # production build in dist/
npm run preview   # serve the production build locally
npm run lint      # oxlint
```

## Firestore security rules (summary)

The full rules are in [`firestore.rules`](firestore.rules).

- Everything requires a signed-in user.
- A **profile** can only be read and written by its owner.
- **Listings** and **tuition posts** can be read by any signed-in user; only the owner can edit or delete them, and the owner cannot change `ownerId`.
- A **request** can only be read by its sender and its receiver. The receiver can change the status; the sender can withdraw it.
- **Chat messages** can only be read and written by the two people on the request. Messages cannot be edited or deleted, and nothing can be sent once a request is declined.

## Deployment

The app is a static frontend, so any static host works.

**Vercel or Netlify**
1. Push the repository to GitHub and import it in Vercel or Netlify.
2. Build command `npm run build`, output directory `dist`.
3. Add the six `VITE_FIREBASE_*` values as **environment variables** in the host's settings.
4. Add a rewrite so deep links such as `/housing` open the app: on Netlify create `public/_redirects` with `/*  /index.html  200`; on Vercel add a `vercel.json` rewrite of every path to `/index.html`.
5. In Firebase, *Authentication, Settings, Authorized domains*, add the deployed domain.

**Firebase Hosting** (used for the live site; `firebase.json` and `.firebaserc` are already in the repository)
```bash
npm install -g firebase-tools
firebase login
npm run build
firebase deploy --only hosting
```
The live site uses its own Hosting site (`messmate-iiuc`, set in `firebase.json`). Create your own with `firebase hosting:sites:create <name>` and put that name in `firebase.json`, or remove the `"site"` line to use the project's default site. Hosting rewrites every path to `index.html`, so deep links such as `/housing` work. A custom Hosting domain must be added in *Authentication, Settings, Authorized domains* (the project's default `.web.app` and `.firebaseapp.com` domains are already authorized).

## Testing

Core flows were tested by hand with two accounts in the browser:

- register, sign out, sign in, wrong password message, redirect of signed-out visitors to `/login`
- create, filter, edit status of, and delete listings and tuition posts; form validation errors
- send an interest request, see **Request sent**, receive it, accept, and see the owner's phone number
- chat: send messages, preview in the conversation list
- weather card loading, error and retry states; the rest of the app works if the weather API is down
- English / Bangla, light / dark theme, mobile width (375px)

## Accessibility

Semantic landmarks (`header`, `nav`, `main`, `aside`, `footer`), a skip link, labelled form fields with linked error messages, visible keyboard focus, `aria-live` regions for chat and notifications, icon buttons with text labels, heading levels in order, adequate contrast in both themes, and reduced-motion support.

## Known limitations

- A listing's contact phone is visible to every signed-in user.
- Anyone can register; there is no IIUC-email check or email verification yet.
- Listing photos are compressed and stored inside the Firestore post (at most 3 per listing) because Firebase Storage needs a paid plan. Tuition posts have no photos, and chat shares text, links, phone numbers and map links but not images.
- No password reset screen yet.

## Screenshots

**Sign in and registration** (dark theme)

![Login page](screenshots/01-login.jpg)

**Dashboard & Connect Hub** (light theme): overview cards, tabs, weather and rent calculator

![Dashboard](screenshots/02-dashboard.jpg)

**Housing & Mess** with search and filters

![Housing and mess listings](screenshots/03-housing.jpg)

**Tutor Board**

![Tutor board](screenshots/04-tutor-board.jpg)

**Chat** between the sender and the post owner, with a shared location

![Chat](screenshots/05-chat.jpg)

## License

Coursework project for CSE-2340, IIUC. © Team G-11, IIUC.

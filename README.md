# Creator Pay

A full-stack digital marketplace where creators can sell and buyers can purchase digital resources (PDF notes, Excel templates, exam prep materials, and freelance services). Built with Node.js/Express backend, Supabase for auth and database, Razorpay for payments, and vanilla HTML/CSS/JS frontend. Deployed on Vercel.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Pages & Features](#pages--features)
- [Backend API](#backend-api)
- [Database Schema](#database-schema)
- [Authentication Flow](#authentication-flow)
- [Payment Flow](#payment-flow)
- [Admin Panel](#admin-panel)
- [Super Admin Panel (Creator Pay Admin)](#super-admin-panel-super-admin)
- [Environment Variables](#environment-variables)
- [Running Locally](#running-locally)
- [Deployment](#deployment)
- [Platform Fee Model](#platform-fee-model)

---

## Project Overview

Creator Pay (internally also called "Creator Pay") is a peer-to-peer digital resource marketplace:

- **Sellers** sign up for free, upload digital files (PDF, Excel, exam papers, service descriptions), set their own price, and earn money when buyers purchase.
- **Buyers** browse resources, pay securely via Razorpay, and get instant download access.
- **Platform fee**: A flat 5% is deducted only at the time of withdrawal — no listing fees, no monthly charges.
- **Stats**: 500+ resources, 1,200+ buyers, 300+ active sellers (as shown on the landing page).

---

## Tech Stack

| Layer           | Technology                                    |
| --------------- | --------------------------------------------- |
| Runtime         | Node.js                                       |
| Framework       | Express.js v5                                 |
| Database & Auth | Supabase (PostgreSQL + Supabase Auth)         |
| File Storage    | Supabase Storage                              |
| Payments        | Razorpay                                      |
| Email           | Nodemailer (Gmail SMTP)                       |
| File Uploads    | Multer (memory storage)                       |
| Frontend        | Vanilla HTML, CSS, JavaScript                 |
| CSS Utilities   | Tailwind CSS (CDN), Font Awesome              |
| Fonts           | Google Fonts (Inter), Google Material Symbols |
| Animations      | Lottie Web                                    |
| Deployment      | Vercel (`@vercel/node` + `@vercel/static`)    |
| Environment     | dotenv                                        |

---

## Project Structure

```
Creator Pay/
├── server.js                  # Main Express server — all API routes
├── package.json               # Dependencies & npm scripts
├── vercel.json                # Vercel build & routing config
├── Logo.png                   # Brand logo
├── file/                      # Root-level static assets (images, dummy files)
└── public/                    # All frontend files served statically
    ├── index.html             # Landing page (home)
    ├── landing.css            # Global landing page styles
    ├── landing.js             # Landing page JS (auth, resources, search, pagination)
    ├── api-config.js          # Centralised API URL config (auto-detects local vs prod)
    ├── auth-modal.css         # Auth modal styles
    ├── auth-guard.js          # Route protection for authenticated pages
    ├── dark-theme.css         # Dark mode styles
    ├── dark-theme.js          # Dark mode toggle logic
    ├── toast.js               # Toast notification utility
    ├── file/                  # Static assets (images, dummy files, Lottie JSON, logos)
    ├── About/                 # About Us page
    ├── Blog/                  # Blog listing + individual post page
    ├── Contact/               # Contact Us page + contact form
    ├── Dashboard/             # Buyer dashboard (purchased resources)
    ├── Detail/                # Resource detail & buy page
    ├── FAQ/                   # FAQ page
    ├── Payment/               # Payment processing page
    ├── Privacy/               # Privacy Policy page
    ├── Resources/             # Public resources listing page
    ├── Terms/                 # Terms & Conditions page
    ├── admin/                 # Seller/user admin panel
    │   ├── index.html         # Admin dashboard (main)
    │   ├── app.js             # Admin panel JS (resources, stats, users)
    │   ├── auth-guard.js      # Admin auth protection
    │   ├── api-config.js      # Admin API config
    │   ├── style.css          # Admin panel styles
    │   ├── dark-theme.css/js  # Admin dark mode
    │   ├── mobile-responsive.css
    │   ├── admin-bottom-nav.css
    │   ├── toast.js
    │   ├── super-admin-btn.js # Button to navigate to super-admin
    │   ├── login.html         # Admin login page
    │   ├── Profile/           # Seller profile page
    │   ├── Resources/         # Seller resource management (add/edit/delete)
    │   ├── Statistics/        # Seller sales statistics
    │   └── Withdrawal/        # Seller withdrawal request page
    └── super-admin/         # Super admin panel (platform-wide)
        ├── Dashboard/         # Platform dashboard (revenue, withdrawals, fees)
        ├── statistic/         # Platform-wide statistics
        ├── payments/          # All payments across platform
        └── Blog/              # Blog post management (create/edit/delete)
```

---

## Pages & Features

### Public Pages

| Page               | Path                         | Description                                                                                  |
| ------------------ | ---------------------------- | -------------------------------------------------------------------------------------------- |
| Home / Landing     | `/`                          | Hero section, stats bar, how-it-works, categories, resources grid, blog preview, FAQ, footer |
| Resources          | `/Resources/`                | Full resource listing with filter & search                                                   |
| Resource Detail    | `/Detail/?title=slug`        | Individual resource detail with buy button                                                   |
| About              | `/About/`                    | About the platform                                                                           |
| Blog               | `/Blog/`                     | Blog post listing (fetched from Supabase `blogs` table)                                      |
| Blog Post          | `/Blog/post/?permalink=slug` | Individual blog post                                                                         |
| FAQ                | `/FAQ/`                      | Frequently asked questions                                                                   |
| Contact            | `/Contact/`                  | Contact form (saves to Supabase + sends email via Gmail)                                     |
| Privacy Policy     | `/Privacy/`                  | Privacy policy                                                                               |
| Terms & Conditions | `/Terms/`                    | Terms of service                                                                             |

### Authenticated User Pages

| Page      | Path          | Description                                     |
| --------- | ------------- | ----------------------------------------------- |
| Dashboard | `/Dashboard/` | Buyer's purchased resources with download links |
| Payment   | `/Payment/`   | Razorpay payment processing page                |

### Seller Admin Pages (`/admin/`)

| Page                 | Path                 | Description                                                       |
| -------------------- | -------------------- | ----------------------------------------------------------------- |
| Admin Dashboard      | `/admin/`            | Overview: total users, active/inactive counts                     |
| Add/Manage Resources | `/admin/Resources/`  | Upload, edit, delete own resources (PDF/Excel/Exam/Freelance)     |
| My Statistics        | `/admin/Statistics/` | Sales stats: total sales, revenue, customers, per-buyer breakdown |
| Profile              | `/admin/Profile/`    | Seller profile view                                               |
| Withdrawal           | `/admin/Withdrawal/` | Submit withdrawal requests                                        |

### Super Admin Pages (`/super-admin/`)

| Page                | Path                      | Description                                                                      |
| ------------------- | ------------------------- | -------------------------------------------------------------------------------- |
| Platform Dashboard  | `/super-admin/Dashboard/` | Total revenue, withdrawn amount, platform fees, pending amount, recent purchases |
| Platform Statistics | `/super-admin/statistic/` | Platform-wide statistics                                                         |
| All Payments        | `/super-admin/payments/`  | All payment records across all users                                             |
| Blog Management     | `/super-admin/Blog/`      | Create, edit, publish/unpublish blog posts                                       |

---

## Backend API

All API routes are defined in `server.js` and served under `/api/`.

### Auth

| Method | Endpoint               | Description                                                    |
| ------ | ---------------------- | -------------------------------------------------------------- |
| POST   | `/api/auth/signup`     | Create user in Supabase, send branded Gmail confirmation email |
| POST   | `/api/auth/login`      | Sign in with Supabase Auth, return session token               |
| POST   | `/api/admin/signup`    | Admin registration via Supabase Auth                           |
| POST   | `/api/admin/login`     | Admin login via Supabase Auth                                  |
| GET    | `/api/auth/debug-link` | Debug: generate and inspect confirmation link                  |

### Users

| Method | Endpoint         | Description                                   |
| ------ | ---------------- | --------------------------------------------- |
| GET    | `/api/users`     | List all users from Supabase Auth (admin use) |
| DELETE | `/api/users/:id` | Delete a user from Supabase Auth              |

### Resources

| Method | Endpoint             | Description                                              |
| ------ | -------------------- | -------------------------------------------------------- |
| GET    | `/api/resources`     | Fetch all resources from Supabase                        |
| POST   | `/api/resources`     | Upload file to Supabase Storage + insert resource record |
| PUT    | `/api/resources/:id` | Update resource (optionally replace file)                |
| DELETE | `/api/resources/:id` | Delete resource record                                   |

### Payments

| Method | Endpoint                    | Description                                                |
| ------ | --------------------------- | ---------------------------------------------------------- |
| GET    | `/api/payment/key`          | Return Razorpay public key                                 |
| POST   | `/api/payment/create-order` | Create Razorpay order (amount in INR)                      |
| POST   | `/api/payment/verify`       | Verify Razorpay signature, save payment record to Supabase |
| GET    | `/api/payments/:userId`     | Get all payments for a user (with resource details joined) |

### Statistics

| Method | Endpoint                               | Description                                                  |
| ------ | -------------------------------------- | ------------------------------------------------------------ |
| GET    | `/api/statistics/purchases/:userEmail` | Seller-specific stats: sales count, revenue, fees, customers |

### Withdrawals

| Method | Endpoint                      | Description                         |
| ------ | ----------------------------- | ----------------------------------- |
| GET    | `/api/withdrawals/:userEmail` | Get withdrawal history for a seller |
| POST   | `/api/withdrawals`            | Submit a new withdrawal request     |
| GET    | `/api/admin/withdrawals`      | Get all withdrawals (super admin)   |

### Admin Dashboard

| Method | Endpoint                     | Description                                                          |
| ------ | ---------------------------- | -------------------------------------------------------------------- |
| GET    | `/api/admin/dashboard-stats` | Platform totals: revenue, withdrawn, fees, pending, recent purchases |
| GET    | `/api/admin/purchases`       | All purchase records enriched with buyer/seller/resource info        |

### Contact

| Method | Endpoint       | Description                                                |
| ------ | -------------- | ---------------------------------------------------------- |
| POST   | `/api/contact` | Save contact message to Supabase + send email notification |

---

## Database Schema

Tables used in Supabase (PostgreSQL):

### `resources`

| Column      | Type         | Description                         |
| ----------- | ------------ | ----------------------------------- |
| id          | integer (PK) | Auto-increment                      |
| type        | text         | `pdf`, `excel`, `exam`, `freelance` |
| title       | text         | Resource title                      |
| description | text         | Resource description                |
| price       | numeric      | Price in INR                        |
| fileurl     | text         | Public URL from Supabase Storage    |
| user_email  | text         | Email of the seller who uploaded it |

### `payments`

| Column      | Type         | Description                   |
| ----------- | ------------ | ----------------------------- |
| id          | integer (PK) | Auto-increment                |
| user_id     | uuid         | Buyer's Supabase Auth user ID |
| resource_id | integer      | FK → resources.id             |
| payment_id  | text         | Razorpay payment ID           |
| order_id    | text         | Razorpay order ID             |
| status      | text         | `completed`                   |
| created_at  | timestamptz  | Auto                          |

### `withdrawals`

| Column     | Type         | Description                        |
| ---------- | ------------ | ---------------------------------- |
| id         | integer (PK) | Auto-increment                     |
| user_email | text         | Seller's email                     |
| amount     | numeric      | Requested withdrawal amount (INR)  |
| method     | text         | Payment method (UPI, bank, etc.)   |
| account    | text         | Account details                    |
| note       | text         | Optional note                      |
| status     | text         | `pending`, `approved`, `completed` |
| created_at | timestamptz  | Auto                               |

### `contact_messages`

| Column     | Type         | Description     |
| ---------- | ------------ | --------------- |
| id         | integer (PK) | Auto-increment  |
| name       | text         | Sender name     |
| email      | text         | Sender email    |
| subject    | text         | Message subject |
| message    | text         | Message body    |
| created_at | timestamptz  | Submission time |

### `blogs`

| Column       | Type         | Description            |
| ------------ | ------------ | ---------------------- |
| id           | integer (PK) | Auto-increment         |
| title        | text         | Post title             |
| slug         | text         | URL-friendly permalink |
| content      | text         | HTML content           |
| excerpt      | text         | Short summary          |
| image_url    | text         | Cover image URL        |
| category     | text         | Post category          |
| is_published | boolean      | Visibility flag        |
| created_at   | timestamptz  | Auto                   |

---

## Authentication Flow

1. **Signup**: User submits name/email/password → POST `/api/auth/signup` → server creates user via `supabase.auth.admin.createUser` (email_confirm: false) → generates confirmation link via `supabase.auth.admin.generateLink` → sends branded HTML email via Gmail SMTP (Nodemailer).
2. **Email Confirmation**: User clicks link → Supabase verifies token → redirects to `https://creatorpay.in//?verified=1` → frontend shows login modal with success message.
3. **Login**: User submits email/password → Supabase `signInWithPassword` → checks `email_confirmed_at` → stores `userLoggedIn`, `currentUser`, `adminToken` in `localStorage`.
4. **Auth State**: On every page load, `localStorage` is checked synchronously (inline script before DOM paint) to prevent flash of wrong UI state.
5. **Logout**: Calls `supabaseClient.auth.signOut()` + clears `localStorage` keys.
6. **Route Protection**: `auth-guard.js` redirects unauthenticated users away from protected pages.

---

## Payment Flow

1. Buyer clicks "Buy Now" on a resource → navigated to `/Detail/` page.
2. Detail page calls GET `/api/payment/key` to get Razorpay public key.
3. On confirm, POST `/api/payment/create-order` with amount → server creates Razorpay order → returns `order_id`.
4. Razorpay checkout modal opens in browser.
5. On payment success, Razorpay returns `razorpay_order_id`, `razorpay_payment_id`, `razorpay_signature`.
6. Frontend POSTs these + `resourceId` + `userId` to `/api/payment/verify`.
7. Server verifies HMAC-SHA256 signature using `RAZORPAY_KEY_SECRET`.
8. On valid signature, inserts record into `payments` table.
9. Buyer can now access the resource from their Dashboard.

---

## Admin Panel

The seller admin panel (`/admin/`) is a single-page-style multi-section panel:

- **Resources tab**: Lists only the logged-in seller's resources. Supports add (file upload via FormData), edit (title/description/price + optional file replace), delete. Uses client-side caching (`window._resourceCache`) to avoid redundant fetches on tab switch. Renders as a table on desktop and card grid on mobile.
- **Statistics tab**: Fetches `/api/statistics/purchases/:userEmail` — shows total sales, revenue (after 5% fee), and a per-customer breakdown table.
- **Withdrawal tab**: Form to submit withdrawal requests. Shows history of past requests with status badges.
- **Profile tab**: Displays seller profile info from Supabase Auth user metadata.
- **Dark mode**: Persisted in `localStorage` (`creatorpay_dark_mode`), applied instantly on page load via inline script to prevent flash.
- **Mobile bottom nav**: Fixed bottom navigation bar for mobile users.

---

## Super Admin Panel (Creator Pay Admin)

Accessible at `/super-admin/` — platform-wide management:

- **Dashboard**: Shows 4 KPI cards — Total Revenue (gross sales), Withdrawn Amount (net after 5% fee), Platform Fees (5% of all withdrawal requests), Pending Amount (net earnings minus net withdrawn). Also shows a recent purchases table with buyer/seller/resource details.
- **Payments**: Full table of all payment records across all users.
- **Statistics**: Platform-wide aggregated stats.
- **Blog**: Full CRUD for blog posts — create with title, slug, content (rich text), image URL, category, published/draft toggle.

---

## Environment Variables

Create a `.env` file in the project root:

```env
# Supabase
SUPABASE_URL=https://<your-project>.supabase.co
SUPABASE_SERVICE_ROLE_KEY=<your-service-role-key>

# Razorpay
RAZORPAY_KEY_ID=<your-razorpay-key-id>
RAZORPAY_KEY_SECRET=<your-razorpay-key-secret>

# Gmail SMTP (for confirmation & contact emails)
GMAIL_USER=<your-gmail-address>
GMAIL_PASS=<your-gmail-app-password>

# Server
PORT=5000
```

> The Supabase anon key is also hardcoded in frontend HTML files for direct client-side Supabase calls (auth session management, blog fetching).

---

## Running Locally

```bash
# Install dependencies
npm install

# Start server
npm start
# or for development
npm run dev
```

Server runs on `http://localhost:5000`. The `api-config.js` file auto-detects `localhost` and routes API calls to `http://localhost:5000`.

---

## Deployment

Deployed on **Vercel** using `vercel.json`:

- `server.js` is built with `@vercel/node` and handles all `/api/*` routes.
- `public/**/*` is served as static files via `@vercel/static`.
- All page routes (e.g. `/About/`, `/admin/Resources/`, `/super-admin/Dashboard/`) are explicitly mapped to their `index.html` files.
- Static asset routes (`.js`, `.css`, images) are mapped with correct `Content-Type` headers.
- Production URL: `https://creatorpay.in/`

CORS is configured to allow only `https://creatorpay.in/` and `localhost` origins.

---

## Platform Fee Model

| Action               | Fee                                    |
| -------------------- | -------------------------------------- |
| Listing a resource   | Free                                   |
| Buying a resource    | No extra fee (buyer pays listed price) |
| Withdrawing earnings | 5% deducted from withdrawal amount     |

Example: Seller withdraws ₹1,000 → receives ₹950 net (₹50 platform fee).

Fee rates by resource type used internally for statistics calculations:

- PDF: 5%
- Excel: 4%
- Exam: 5%
- Service/Freelance: 6%

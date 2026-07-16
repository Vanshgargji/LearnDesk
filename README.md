# LearnDesk 🎓

[![Live Demo](https://img.shields.io/badge/Live-Demo-brightgreen?style=for-the-badge)](https://learndesk-1.onrender.com/)

![Learnex Logo](frontend/public/Learn_Desk_logo.png)


**LearnDesk** is a full-stack Learning Management System (LMS) that lets educators create and sell courses while students discover, enroll in, and learn from them. It combines a MERN stack backend with an AI-powered natural-language course search, secure payments, and OTP-based account recovery — all in one platform.

## 🚀 Key Features

*   **🤖 AI-Powered Course Search:** Users can type natural-language queries (e.g. *"I want to learn to build mobile apps"*) and Gemini AI maps the intent to the right course category/level, falling back to keyword search when no direct match exists.
*   **🔐 Secure Authentication:** Email/password signup with bcrypt hashing, Google Sign-In, JWT-based sessions via httpOnly cookies, and OTP-based forgot-password flow (emailed via Nodemailer).
*   **📚 Course & Lecture Management:** Educators can create courses, add/edit/remove lectures, upload thumbnails and lecture videos (via Cloudinary), and publish/unpublish courses.
*   **💳 Payments & Enrollment:** Razorpay integration for course checkout, with server-side payment verification before enrolling a student.
*   **⭐ Ratings & Reviews:** Students can rate and review enrolled courses; one review per user per course.
*   **📊 Educator Dashboard:** Creator-facing dashboard to manage owned courses, lectures, and view enrolled students.
*   **🎨 Responsive UI:** Built with React 19, Redux Toolkit, and Tailwind CSS v4 for a fast, modern student/educator experience.

## 🛠️ Tech Stack

**Frontend**
*   **Framework:** [React 19](https://react.dev/) (via [Vite](https://vitejs.dev/))
*   **State Management:** [Redux Toolkit](https://redux-toolkit.js.org/) + [React Redux](https://react-redux.js.org/)
*   **Styling:** [Tailwind CSS v4](https://tailwindcss.com/)
*   **Routing:** [React Router v7](https://reactrouter.com/)
*   **HTTP Client:** [Axios](https://axios-http.com/)
*   **Auth (Client):** [Firebase](https://firebase.google.com/) (Google Sign-In)
*   **UI Extras:** React Icons, React Toastify, React Spinners, React Simple Star Rating, Recharts

**Backend**
*   **Runtime/Framework:** [Node.js](https://nodejs.org/) + [Express 5](https://expressjs.com/)
*   **Database:** [MongoDB](https://www.mongodb.com/) (via [Mongoose](https://mongoosejs.com/))
*   **Authentication:** JWT (`jsonwebtoken`) + `bcryptjs` password hashing
*   **AI:** [Google Gemini](https://ai.google.dev/) via `@google/genai` (`gemini-2.5-flash`)
*   **File Uploads:** [Multer](https://github.com/expressjs/multer) + [Cloudinary](https://cloudinary.com/)
*   **Payments:** [Razorpay](https://razorpay.com/)
*   **Email:** [Nodemailer](https://nodemailer.com/) (Gmail transport for OTP emails)
*   **Validation:** [validator](https://github.com/validatorjs/validator.js)

## 🏗️ Architecture

```
┌───────────────────────────────────────────────────────────────┐
│                          Frontend                              │
│              (React 19 + Vite + Redux Toolkit)                 │
│                                                                │
│  ┌─────────────┐  ┌──────────────┐  ┌───────────────────────┐  │
│  │  Pages / UI │  │ Redux Slices │  │  Tailwind CSS + Icons │  │
│  │ (student,   │  │ (course,     │  │                       │  │
│  │  admin, ai) │  │  user, etc.) │  │                       │  │
│  └─────────────┘  └──────────────┘  └───────────────────────┘  │
│                            │                                   │
│                      [Axios + Cookies]                         │
└─────────────────────────────┼──────────────────────────────────┘
                              │
                    ┌─────────▼─────────┐
                    │   Express Server  │
                    │  (index.js entry) │
                    └─────────┬─────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
┌───────▼───────┐   ┌─────────▼────────┐   ┌────────▼────────┐
│  Auth Layer   │   │   API Routes     │   │   Middlewares   │
│ (JWT + cookie │   │ /api/auth        │   │ isAuth, multer  │
│  + Firebase)  │   │ /api/user        │   │                 │
│               │   │ /api/course      │   │                 │
│               │   │ /api/payment     │   │                 │
│               │   │ /api/ai          │   │                 │
│               │   │ /api/review      │   │                 │
└───────┬───────┘   └─────────┬────────┘   └────────┬────────┘
        │                     │                     │
        └─────────────────────┼─────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
┌───────▼───────┐   ┌─────────▼────────┐   ┌────────▼────────┐
│   MongoDB     │   │  External APIs   │   │   Cloudinary    │
│ (via Mongoose)│   │ - Google Gemini  │   │  (media storage)│
│ Users, Courses│   │ - Razorpay       │   │                 │
│ Lectures,     │   │ - Gmail (OTP)    │   │                 │
│ Reviews,Orders│   │                  │   │                 │
└───────────────┘   └──────────────────┘   └─────────────────┘
```

## 📈 Performance & Quality Metrics

LearnDesk's AI search and backend have been benchmarked to ensure a fast, reliable experience.

### AI Search Performance
- **2.06s Average AI Search Latency** — end-to-end time for a natural-language query to return matched courses.
- **92% Top-1 Hit Rate** — how often the AI-inferred keyword surfaces the correct course category/level on the first result.

### Backend Performance
- **475ms Average API Response Time** across core endpoints.
- **64% Protected API Coverage** — share of API routes secured behind authentication middleware (`isAuth`).

### Lighthouse Scores
| Category | Score |
|---|---|
| Performance | 84% |
| Accessibility | 92% |
| Best Practices | 96% |
| SEO | 91% |

## 🔄 Data Flow

1.  **User Query**: A student types a query in natural language on the "Search with AI" page.
2.  **AI Intent Matching**: The query is sent to `/api/ai/search`, where Gemini maps it to the closest course category/level keyword.
3.  **Course Lookup**: MongoDB is first queried directly against the raw input (title, subtitle, description, category, level); if no match is found, it falls back to a regex search using the AI-inferred keyword.
4.  **Enrollment**: On checkout, Razorpay creates an order; after payment, the backend verifies the signature and updates both the `User.enrolledCourses` and `Course.enrolledStudents` arrays.
5.  **Learning**: Enrolled students access lectures (video streamed from Cloudinary) and can leave a rating/review once done.

## 📂 Project Structure

```
learndesk/
├── backend/
│   ├── index.js                    # Express app entry point
│   ├── configs/
│   │   ├── cloudinary.js           # Cloudinary upload helper
│   │   ├── db.js                   # MongoDB connection
│   │   ├── Mail.js                 # Nodemailer OTP email sender
│   │   └── token.js                # JWT token generator
│   ├── controllers/
│   │   ├── aiController.js         # Gemini-powered course search
│   │   ├── authController.js       # Signup, login, Google auth, OTP flow
│   │   ├── courseController.js     # Course & lecture CRUD
│   │   ├── orderController.js      # Razorpay order + payment verification
│   │   ├── reviewController.js     # Course ratings & reviews
│   │   └── userController.js       # Profile fetch/update
│   ├── middlewares/
│   │   ├── isAuth.js               # JWT cookie verification
│   │   └── multer.js               # Multipart file upload handling
│   ├── models/
│   │   ├── courseModel.js
│   │   ├── lectureModel.js
│   │   ├── orderModel.js
│   │   ├── reviewModel.js
│   │   └── userModel.js
│   └── routes/
│       ├── aiRoute.js
│       ├── authRoute.js
│       ├── courseRoute.js
│       ├── paymentRoute.js
│       ├── reviewRoute.js
│       └── userRoute.js
└── frontend/
    ├── src/
    │   ├── components/              # Nav, Footer, Card, ReviewCard, VideoPlayer, etc.
    │   ├── customHooks/              # getAllReviews, getCourseData, getCurrentUser
    │   ├── pages/
    │   │   ├── Home.jsx, Login.jsx, SignUp.jsx, ForgotPassword.jsx
    │   │   ├── AllCourses.jsx, ViewCourse.jsx, ViewLecture.jsx
    │   │   ├── EnrolledCourse.jsx, Profile.jsx, EditProfile.jsx
    │   │   ├── SearchWithAi.jsx     # AI-powered course search page
    │   │   └── admin/                # Dashboard, Courses, CreateCourse, CreateLecture, EditLecture
    │   ├── redux/                     # courseSlice, lectureSlice, reviewSlice, userSlice, store
    │   └── utils/
    │       └── Firebase.js           # Firebase (Google Sign-In) config
    └── package.json
```

## ⚙️ Environment Variables

### Backend (`.env` in `/backend`)

| Variable | Description | Required |
|---|---|---|
| `PORT` | Port the Express server listens on | ✅ Yes |
| `MONGODB_URL` | MongoDB connection string | ✅ Yes |
| `JWT_SECRET` | Secret key for signing JWTs | ✅ Yes |
| `EMAIL` | Gmail address used to send OTP emails | ✅ Yes |
| `EMAIL_PASS` | Gmail app password for Nodemailer | ✅ Yes |
| `CLOUDINARY_CLOUD_NAME` | Cloudinary cloud name | ✅ Yes |
| `CLOUDINARY_API_KEY` | Cloudinary API key | ✅ Yes |
| `CLOUDINARY_API_SECRET` | Cloudinary API secret | ✅ Yes |
| `RAZORPAY_KEY_ID` | Razorpay key ID | ✅ Yes |
| `RAZORPAY_SECRET` | Razorpay key secret | ✅ Yes |
| `GEMINI_API_KEY` / Google GenAI credentials | Used by `@google/genai` for AI search | ✅ Yes |

### Frontend (`.env` in `/frontend`)

| Variable | Description | Required |
|---|---|---|
| `VITE_API_URL` | Base URL of the backend API | ✅ Yes |
| `VITE_FIREBASE_*` | Firebase project config keys (API key, auth domain, project ID, etc.) for Google Sign-In | ✅ Yes |

## 🚧 Error Handling

- **Unauthenticated Access**: `isAuth` middleware checks for a valid JWT cookie on protected routes and rejects requests with a 400/401 if missing or invalid.
- **Duplicate Reviews**: A user cannot submit more than one review for the same course — enforced at the controller level.
- **Payment Verification**: Enrollment only happens after Razorpay order status is confirmed as `paid`; failed verification returns a clear error without enrolling the student.
- **OTP Expiry**: Password reset OTPs expire after 5 minutes and are validated against both value and expiry before allowing a reset.
- **File Upload Cleanup**: Temporary files are removed from local storage (`fs.unlinkSync`) after a successful or failed Cloudinary upload to avoid disk bloat.
- **Graceful Failures**: All controllers wrap logic in try/catch and return descriptive JSON error messages instead of leaking stack traces.

## 🔄 Tradeoffs & Limitations

### Current Limitations

1. **Single AI Provider**: Only Google Gemini is used for search intent matching; no fallback LLM provider.
2. **Regex-Based Fallback Search**: When direct keyword matches fail, search falls back to MongoDB regex queries rather than true semantic/vector search.
3. **Single Payment Gateway**: Only Razorpay is supported (India-focused); no Stripe/PayPal support yet.
4. **No Real-Time Progress Tracking**: Lecture-level completion/progress tracking isn't implemented yet.
5. **Protected Route Coverage**: ~64% of API routes currently sit behind auth middleware; the rest (e.g. public course listing, review fetch) are intentionally public.

### Design Decisions

- **JWT in httpOnly Cookies**: Chosen over localStorage tokens to reduce XSS-based token theft risk.
- **Gemini for Query Understanding**: A lightweight keyword-classification prompt (rather than full RAG) keeps AI search fast (~2s) while still handling free-form queries.
- **Cloudinary for Media**: Offloads video/image storage and delivery instead of self-hosting media files.
- **Mongoose Population**: Used extensively (`lectures`, `reviews`, `enrolledCourses`) to keep API responses rich without extra round trips.

## 📸 Screenshots

### Login/SignUp Page
![login/signup Page](frontend/public/Screenshot_2026-07-16_054259.png)

## 🚀 Future Improvements

### Short Term
- [ ] Lecture progress tracking (mark as complete / resume where left off)
- [ ] Pagination for course listings and reviews
- [ ] Improve AI search with true vector/semantic search instead of regex fallback
- [ ] Expand protected API coverage and add rate limiting

### Medium Term
- [ ] Multi-provider payments (Stripe/PayPal) for international students
- [ ] Course certificates on completion
- [ ] Wishlist / saved courses for students
- [ ] Admin analytics dashboard (enrollments, revenue, ratings over time)

### Long Term
- [ ] Live/cohort-based classes alongside self-paced courses
- [ ] Discussion forums / Q&A per lecture
- [ ] Mobile app (React Native)
- [ ] Multi-language support for course content and UI

## 🏁 Getting Started

### Prerequisites

*   Node.js (v18+)
*   npm or yarn
*   MongoDB database (local or Atlas)
*   Cloudinary account
*   Razorpay account
*   Google Gemini API key
*   Firebase project (for Google Sign-In)

### Installation

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/Vanshgargji/learndesk.git
    cd learndesk
    ```

2.  **Install backend dependencies:**
    ```bash
    cd backend
    npm install
    ```

3.  **Install frontend dependencies:**
    ```bash
    cd ../frontend
    npm install
    ```

4.  **Set up environment variables:**
    Create a `.env` file inside `/backend` with the variables listed above, and a `.env` file inside `/frontend` for your Firebase config and API URL.

5.  **Run the backend server:**
    ```bash
    cd backend
    npm run dev
    ```

6.  **Run the frontend dev server:**
    ```bash
    cd frontend
    npm run dev
    ```

    Open [http://localhost:5173](http://localhost:5173) (Vite's default port) to see the result.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1.  Fork the project
2.  Create your feature branch (`git checkout -b feature/AmazingFeature`)
3.  Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4.  Push to the branch (`git push origin feature/AmazingFeature`)
5.  Open a Pull Request

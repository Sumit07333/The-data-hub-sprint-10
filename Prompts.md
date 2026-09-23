# AI Assistance Log — Prompts.md

## Project: The Data Hub (Sprint 09: Track B — Fullstack Developer)

This document tracks all AI prompts and interactions used during the development of Sprint 09 Track B, strictly abiding by course policy (LLMs authorized for debugging and conceptual explanation).

---

### Phase 1: Project Initialization & Express Server Setup

#### Entry 1: Project Initialization
- **Target Objective**: Initialize Node.js environment, configure `package.json`, install `express` as a core dependency, and install `nodemon` as a development dependency.
- **Prompt Used**:
  > "CRITICAL RULE: Build Sprint 09 strictly according to the Sprint 09 Track B assignment only. Do not modify, add, or anticipate any Sprint 10 requirements inside Sprint 09... I want to create the Sprint 09 Track B project, 'The Data Hub,' from zero as a separate Node.js + Express REST API project. Please begin with Step 1: Project Setup & Initialization."
- **Outcome / Explanation**:
  - `package.json` configured with entry point `server.js`.
  - Scripts configured: `"start": "node server.js"`, `"dev": "nodemon server.js"`.
  - Core dependency: `express`.
  - Dev dependency: `nodemon`.
  - Clean `.gitignore` verified to exclude `node_modules/`.

#### Entry 2: Dev Server Startup Fix
- **Target Objective**: Diagnose and resolve dev server startup failure ("The dev server didn't start, please fix it").
- **Prompt Used**:
  > "The dev server didn't start, please fix it"
- **Root Cause**: `package.json` was set to run `nodemon server.js`, but the initial `server.js` file had not yet been created, causing nodemon to fail on launch.
- **Outcome / Explanation**:
  - Created `server.js` with Express server initialization and `express.json()` middleware.
  - Bound listener to Port 5000 (Sprint 09 primary assignment requirement) and dual-bound to container proxy port 3000 to ensure connectivity across environments.
  - Restarted dev server and verified HTTP 200 JSON response on both ports.

---

### Phase 2: In-Memory Database & CRUD Architecture

#### Entry 3: In-Memory CRUD Implementation & Frontend Removal
- **Target Objective**: Remove unrequested frontend/Vite files, implement `blogPosts = []` in-memory data store, and construct standard REST routes (`GET /posts`, `GET /posts/:id`, `POST /posts`, `PUT /posts/:id`, `DELETE /posts/:id`).
- **Prompt Used**:
  > "IMPORTANT: SPRINT 09 TRACK B CORRECTION TASK... Your task is to inspect my CURRENT project and correct it so that it fully satisfies the original Sprint 09 Track B assignment... Remove unnecessary frontend files... Sprint 09 must remain exactly a Node.js + Express REST API using an in-memory JavaScript array."
- **Outcome / Explanation**:
  - Deleted unused React/Vite scaffolding (`/src/`, `vite.config.ts`, `tsconfig.json`).
  - Created `controllers/postController.js` containing `let blogPosts = []` and full CRUD logic with title and content validation, 400 Bad Request responses, 404 Not Found handling, and 201 Created statuses.
  - Created `routes/postRoutes.js` using `express.Router()` to cleanly route endpoints to controller actions.

---

### Phase 3: Custom Middleware, Authentication & Port Debugging

#### Entry 4: Custom Logger Middleware & Mock Authentication
- **Target Objective**: Implement custom request logger without third-party libraries and add a mock login endpoint returning a mock JWT token.
- **Outcome / Explanation**:
  - Created `middleware/logger.js` logging `[METHOD] URL - HH:MM AM/PM`.
  - Created `POST /login` in `server.js` accepting `email` and `password` and returning `{ "message": "Login successful", "token": "mock-jwt-token" }`.
  - Added 404 handler for unknown routes and 500 global error handler avoiding stack trace leakage.

#### Entry 5: Port Conflict Resolution (EADDRINUSE 8080)
- **Target Objective**: Debug server failure during automated test execution.
- **Root Cause**: When reading `process.env.PORT || 5000`, the container had `process.env.PORT=8080` already bound by the internal system, causing an `EADDRINUSE :::8080` unhandled error.
- **Outcome / Explanation**:
  - Changed port binding in `server.js` to strictly bind `PORT = 5000` (Sprint 09 requirement) and secondary listener on port 3000 with `EADDRINUSE` catch.
  - Server successfully starts on port 5000 and 3000 without crashing.

#### Entry 6: Postman / Thunder Client QA Verification
- **Target Objective**: Execute all 10 standard test cases and 6 failure cases against the live server.
- **Outcome / Explanation**:
  - All 16 tests executed against `http://localhost:5000`.
  - Verified 201 Created on valid POST, 200 OK on GET, 200 OK on PUT and DELETE, 400 Bad Request on missing fields, and 404 Not Found on non-existent IDs.
  - All responses returned valid JSON structures.

#### Entry 7: Minimal Correction - Single Port 5000 Listener & Frontend Cleanup
- **Target Objective**: Apply strict minimal corrections per Sprint 09 Track B instructions:
  1. Remove extra port 3000 listener in `server.js` so only Port 5000 is used.
  2. Remove leftover `index.html` frontend file.
  3. Verify Postman and deployment instructions remain accurate without fabricating test results.
- **Outcome / Explanation**:
  - `server.js` now exclusively binds to Port 5000 via `app.listen(PORT, ...)`.
  - `index.html` was deleted; no frontend code remains.
  - No MongoDB, Mongoose, or Sprint 10 features were added.

---

## Sprint 10: Track B — Fullstack Developer
**Theme**: NoSQL Cloud Databases & Object Data Modeling (ODM)

### Phase 1: Mongoose ODM & MongoDB Atlas Provisioning Setup

#### Entry 8: ODM Dependency Installation & Environment Configuration
- **Target Objective**: Install `mongoose` and `dotenv`, configure safe environment variables, verify `.gitignore` protection of `.env*`, and create a reusable database connection module (`config/db.js`).
- **Prompt Used**:
  > "PROJECT: The Data Hub. CURRENT VERSION: Sprint 09 — Track B (Fullstack). TARGET: Sprint 10 — Track B (Fullstack). THEME: NoSQL Cloud Databases & Object Data Modeling (ODM)... Transition this existing project from volatile in-memory arrays to persistent cloud storage using MongoDB Atlas and Mongoose."
- **Outcome / Explanation**:
  - Installed `mongoose` and `dotenv`.
  - Created `config/db.js` using `mongoose.connect(process.env.MONGO_URI)` with error handling that prevents exposing database credentials.
  - Updated `.env.example` to declare `MONGO_URI="your_mongodb_connection_string_here"` while preserving existing environment variables (`PORT`, `GEMINI_API_KEY`, `APP_URL`).
  - Confirmed `.gitignore` protects all `.env*` files with `!.env.example` exception.
  - Updated `server.js` to call `connectDB()` during startup while keeping Render-compatible port binding and container proxy support.

---

### Phase 2: Post Model & Database CRUD Migration

#### Entry 9: Post Model Schema & ObjectId CRUD Refactoring
- **Target Objective**: Create Mongoose `Post` model, remove active in-memory array (`blogPosts = []`), and migrate `controllers/postController.js` to MongoDB operations.
- **Outcome / Explanation**:
  - Created `models/Post.js` with `title` (String, required), `content` (String, required), `authorId` (ObjectId ref User, default null), and `createdAt` (Date, default Date.now).
  - Deprecated and removed active in-memory storage array from `controllers/postController.js`.
  - Migrated `POST /posts` to use `Post.create()`.
  - Migrated `GET /posts` to use `Post.find().sort({ createdAt: -1 }).populate('authorId', 'name email')`.
  - Migrated `GET /posts/:id` to use `Post.findById(id).populate(...)` with `mongoose.Types.ObjectId.isValid()` format validation.
  - Migrated `PUT /posts/:id` to use `Post.findByIdAndUpdate()` with validation and updated document return.
  - Migrated `DELETE /posts/:id` to use `Post.findByIdAndDelete()`.
  - Ensured invalid ObjectId strings return HTTP 400 with `{ "error": "Invalid post ID format" }` rather than unhandled Mongoose CastError exceptions.

---

### Phase 3: User Model, Populate & Top 3 Recent Posts

#### Entry 10: User Relationship Modeling, .populate() & Top 3 Route Ordering
- **Target Objective**: Create `User` schema, establish relationship referencing via `Post.authorId`, implement `.populate()`, add `GET /posts/recent/top3`, and create minimal test user endpoints.
- **Outcome / Explanation**:
  - Created `models/User.js` with `name` (String, required), `email` (String), and `createdAt` (Date).
  - Created `controllers/userController.js` and `routes/userRoutes.js` mounted at `/users` providing simple `POST /users` and `GET /users` for generating and verifying test users for relationship modeling without complex authentication.
  - Added `GET /posts/recent/top3` query using `.sort({ createdAt: -1 }).limit(3).populate('authorId', 'name email')`.
  - Configured route ordering in `routes/postRoutes.js` registering `GET /recent/top3` explicitly before `GET /:id` to eliminate route shadowing.
  - Preserved existing `middleware/logger.js` request logging, mock `POST /login` authentication scaffolding, 404 handler, and global 500 error handler.
  - Updated API root discovery endpoint `GET /` to reflect Sprint 10 Track B metadata and all active routes.

---

### Phase 4: Documentation & QA Verification

#### Entry 11: README Overhaul, Prompts Tracking & Endpoint Verification
- **Target Objective**: Update `README.md` to comprehensively document Sprint 10 Track B while preserving Sprint 09 context; test all endpoints locally in the workspace.
- **Outcome / Explanation**:
  - Updated `README.md` with complete architecture details, Atlas M0 setup instructions, Render environment variable guidance, Postman QA plan, request/response examples, and a 3-minute demo script.
  - Verified local server startup on port 3000.
  - Tested `GET /` (200 OK with Sprint 10 details).
  - Tested `POST /login` (200 OK with mock JWT).
  - Tested input validation on `POST /posts` (400 Bad Request on missing fields).
  - Tested invalid `authorId` validation (400 Bad Request on malformed ObjectId).
  - Tested invalid post ID handling on `GET /posts/123` (400 Bad Request).
  - Tested route ordering on `GET /posts/recent/top3` (verified it routes to top 3 controller and not `/:id`).
  - Tested 404 handler on unregistered routes (404 Not Found).
  - Confirmed no remote GitHub operations (push, commit, sync) were performed per read-only instructions.



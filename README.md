# ClassCompass

## Introduction

### What is ClassCompass?

ClassCompass is a course recommendation and bookmarking platform designed for UIUC students. The application aggregates course data—including GPA statistics, instructor ratings, and workload information—to help students make informed academic decisions. Students can:

- View personalized course recommendations based on their academic profile
- Search and filter courses by department, credits, GPA range, and workload
- Bookmark courses for later reference
- Manage their academic profile (major, year, completed courses)
- Export bookmarked courses as CSV for easy integration with planning tools


---

## Technical Architecture

ClassCompass is built as a full-stack web application with a Django REST backend and a Next.js frontend, connected by API endpoints.

**Backend:**
- Django REST Framework API serving course recommendations, profiles, and bookmarks
- SQLite database (local dev) with support for PostgreSQL/Supabase in production
- Supabase JWT authentication for secure user sessions
- CSV data import scripts for GPA statistics and instructor signals

**Frontend:**
- Next.js 15.5.6 with React 19 and TypeScript
- TailwindCSS for styling
- React Query for server state management
- Client-side authentication via Supabase JWT tokens

**Data Flow:**
1. Backend exposes `/api/` endpoints for courses, recommendations, profiles, and bookmarks
2. Frontend calls backend APIs with authentication headers
3. User profiles and bookmarks are persisted in the database
4. Course data (GPA, instructor ratings, workload) is imported via management commands

---

## Developers

**Team 013 (FA25):**
- **Shivam**: Frontend development (pages, components, etc.)
- **Darsh**: Frontend development (hooks, client state management, etc.)
- **Aditya**: Django API, authentication, course recommendations
- **Adit**: CSV import scripts, GPA/instructor signals data loading

---

## Environment Setup

### Backend Setup

#### Initial venv Installation

Navigate to the `backend/` directory and create a virtual environment:

```bash
cd backend
python -m venv .venv
```

#### Activate venv

**macOS/Linux:**
```bash
source .venv/bin/activate
```

**Windows (PowerShell):**
```powershell
.venv\Scripts\Activate.ps1
```

**Windows (Command Prompt):**
```cmd
.venv\Scripts\activate
```

#### Package Management

Install required packages:

```bash
pip install -r requirements.txt
```

#### Database Setup

Apply Django migrations to create the database schema:

```bash
python manage.py migrate
```

#### Create a Superuser (Optional)

For admin access and testing:

```bash
python manage.py createsuperuser
```

Follow the prompts to set a username and password.

#### Start the Backend Server

```bash
python manage.py runserver
```

The backend will be available at `http://localhost:8000`

Verify it's running by visiting `http://localhost:8000/api/health/` (should return `{"status":"ok"}`)

---

### Frontend Setup

#### Navigate to frontend directory

```bash
cd frontend
```

#### Install Dependencies

```bash
npm install
```

#### Start the Development Server

```bash
npm run dev
```

The frontend will be available at `http://localhost:3000` (or `:3001` if port 3000 is in use)

---

## Project Instructions

### 1. Load Initial Data

To populate the database with course data, GPA statistics, and instructor signals:

**Import GPA data:**
```bash
cd backend
python manage.py import_gpa scripts/uiuc-gpa-dataset.csv
```

**Import instructor signals and workload data:**
```bash
python manage.py import_instructor_signals scripts/instructor_signals.csv
```

### 2. Access the Web Interface

1. Open `http://localhost:3000` in your browser
2. Navigate to the **Recommendations** tab to see personalized course suggestions
3. Use filters (department, credits, GPA range) to refine results
4. Click the **star icon** on a course to bookmark it
5. Visit the **Bookmarks** tab to view and manage your saved courses
6. Update your **Profile** with your major, year, and completed courses

### 3. Backend API Usage

**List all courses:**
```bash
curl http://localhost:8000/api/courses/
```

**Filter courses by department:**
```bash
curl "http://localhost:8000/api/courses/?dept=CS&min_avg_gpa=3.5"
```

**Get personalized recommendations (requires authentication):**
```bash
curl -H "Authorization: Bearer <your-jwt-token>" \
  http://localhost:8000/api/recommendations/?limit=10
```

**Interactive API Docs:**
- Swagger UI: `http://localhost:8000/api/schema/swagger-ui/`
- ReDoc: `http://localhost:8000/api/schema/redoc/`

### 4. Export Bookmarks

From the frontend, click the **Export** button on the Bookmarks page to download your saved courses as a CSV file. This CSV can be imported into planning tools like Notion or Todoist.

**Backend CSV export endpoint (requires authentication):**
```bash
curl -H "Authorization: Bearer <your-jwt-token>" \
  http://localhost:8000/api/bookmarks/export/ -o bookmarks.csv
```

---

## Development

### Running Tests

```bash
cd backend
pytest
```

For verbose output:
```bash
pytest -v
```

### Code Quality

**Lint check:**
```bash
ruff check .
```

**Format code:**
```bash
black .
```

**Check formatting without applying changes:**
```bash
black --check .
```

---

## Project Structure

```
FA25-Team013/
├── README.md
├── backend/
│   ├── api/
│   │   ├── models.py          # Profile, Course, Bookmark models
│   │   ├── views.py           # API endpoints
│   │   ├── serializers.py     # DRF serializers
│   │   ├── urls.py            # URL routing
│   │   ├── authentication.py  # Supabase JWT auth
│   │   └── management/commands/
│   │       ├── import_gpa.py
│   │       └── import_instructor_signals.py
│   ├── server/
│   │   ├── settings.py        # Django configuration
│   │   ├── urls.py            # Root URL config
│   │   ├── asgi.py
│   │   └── wsgi.py
│   ├── scripts/               # Data import scripts and CSV files
│   ├── requirements.txt
│   ├── manage.py
│   └── README.md              # Detailed backend documentation
├── frontend/
│   ├── src/
│   │   ├── app/               # Next.js pages (layout, bookmarks, profile, etc.)
│   │   ├── components/        # React components (CourseCard, Banner, etc.)
│   │   ├── lib/               # Utilities (API client, React Query hooks)
│   │   └── global.css         # Tailwind CSS
│   ├── package.json
│   ├── tsconfig.json
│   ├── next.config.ts
│   └── README.md              # Frontend-specific documentation
└── docs/
    └── activity.md            # Project activity log
```

---

## Troubleshooting

### Backend not responding / API calls fail with 404

- Ensure the backend server is running: `python manage.py runserver`
- Check that the URL is correct: `http://localhost:8000/api/...`
- Verify database migrations have been applied: `python manage.py migrate`

### Profile/Bookmarks not saving

- Ensure authentication is set up (Supabase JWT or Django session)
- Backend requires valid user authentication for protected endpoints
- Frontend must include credentials in API requests (`credentials: "include"`)

### Port conflicts

- If port 3000 is in use, Next.js will automatically use 3001
- If port 8000 is in use, change it: `python manage.py runserver 8001`

### Database issues

- Reset the database: `rm backend/db.sqlite3` and re-run `python manage.py migrate`
- Check database file location in `backend/server/settings.py`

---

## License

This project is part of CS 222 (Software Design Lab) at UIUC.

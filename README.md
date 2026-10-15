# Employee Attendance & Analytics API

FastAPI + MongoDB service for employee attendance. All code is in `app/main.py`.

## Implemented
- `GET /health` (returns 503 if MongoDB is unreachable)
- `POST /employees`, `GET /employees`
- `POST /attendance/punch-in`, `POST /attendance/punch-out`
- `GET /attendance`

## Not implemented (ran out of time)
- `PATCH /attendance/{emp_code}/{date}`
- The four `/analytics/...` endpoints
- `GET /admin/explain/{endpoint}`

## Run locally
1. Create a virtual environment and install the requirements:
   `pip install -r requirements.txt`
2. Create a `.env` file with `MONGO_URI` and `MONGO_DB`.
3. Start the server:
   `uvicorn app.main:app --port 8000`
4. Open http://127.0.0.1:8000/docs

Indexes are created at startup. See `REVIEW.md` for the defects I fixed and `DECISIONS.md` for design decisions.
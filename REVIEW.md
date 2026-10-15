# REVIEW.md

Defects I found in the starter `app/main.py`.

| # | Where (function / line) | What is wrong | How you'd notice it (test, input, or symptom) | How you fixed it |
|---|---|---|---|---|
| 1 | list_employees | Offset was page * page_size, so page 1 skipped the first rows | Page 1 started at EMP0003 instead of EMP0001 | Changed to (page - 1) * page_size |
| 2 | list_employees | total counted all employees, no sort order, no limits on page and page_size | Filtering by department still gave the full total; page=0 or page_size=1000 was accepted | count_documents(q), sort by emp_code, Query(ge, le) |
| 3 | Time handling | Datetimes were stored without a timezone and returned as strings | created_at came back as a date string, not epoch milliseconds | tz_aware=True and to_ms / from_ms helpers |
| 4 | create_employee | No validation on any field | emp_code "ABC" and email "not-an-email" were accepted | Field patterns and lengths on EmployeeIn, joined_on as a date |
| 5 | create_employee | Check-then-insert, so two requests at once could both pass | Two simultaneous POSTs with the same emp_code | Unique index on emp_code, catch DuplicateKeyError and return 409 |
| 6 | punch_in | Unknown employee crashed with a 500 | emp_code "EMP9999" gave a server error instead of 404 | Return 404 when the employee is not found |
| 7 | punch_in | Date came from the server clock, not IST, and ignored overnight shifts | A punch near midnight, or an overnight shift, landed on the wrong date | attendance_date() uses IST and handles overnight shifts (R1) |
| 8 | compute_late_minutes | Late check was minutes > 10 after flooring | 09:40:00 to 09:40:59 gave the wrong result; R2 says 09:40:00 is not late | Compare seconds > 600, then floor to minutes |
| 9 | punch_in | Same race as create_employee | Two simultaneous punch-ins for the same day | Unique index on (emp_code, date), DuplicateKeyError returns 409 |
| 10 | list_attendance | Loaded every record into memory and sorted in Python, no validation | Slow on large data, page=0 accepted, bad dates accepted | DB-side sort, skip and limit, an index, and validation |
| 11 | health | Always returned ok even when the database was down | With the database unreachable, /health still said ok | Ping the database, return 503 on failure |

Looked at and decided not a defect: status defaulting to PRESENT on punch-in, because the contract says so.
# DECISIONS.md

1. **Indexes.** I made three: a unique index on `employees.emp_code` (no duplicate codes), a unique index on `attendance_logs (emp_code, date)` (one record per employee per day), and an index on `attendance_logs (date descending, emp_code)` (the sorted listing is done by the database). I rejected an index on `status`: it has only five values, so it barely narrows a search.

2. **Punch-in race.** Both requests reach `insert_one` together. MongoDB accepts the first. The second breaks the unique `(emp_code, date)` index and raises `DuplicateKeyError`, which I turn into a 409. I do no check before inserting, so there is no gap.

3. **Ties.** Not built; I ran out of time. I would sort by late minutes descending, then `emp_code` ascending, so ties always come out in the same order.

4. **Headcount.** Not built. I would count from the `employees` collection by `joined_on`, not from the logs, so people with no logs are still counted.

5. **One change for 100x data.** I would replace skip paging with a cursor on `(date, emp_code)`, because a big skip makes MongoDB walk past every skipped record.
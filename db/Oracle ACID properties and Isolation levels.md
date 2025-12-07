Good idea. Here’s a table of common **anomalies** (dirty read, non-repeatable read, phantom read) and how they map to **isolation levels** — plus concrete behaviour for Oracle Database. I also give **small examples** (“what might go wrong” or “what you see”) in Oracle under different isolation assumptions.

---

## 🔎 Anomalies (Read Phenomena) — definitions

* **Dirty Read**: Transaction T1 reads data written by T2, but T2 has not yet committed. If T2 rolls back later, T1 saw “data that never existed.” ([Oracle Docs][1])
* **Non-repeatable Read** (also “fuzzy read”): T1 reads a row; then T2 modifies or deletes that row and commits; when T1 reads the same row again, it sees a different value (or no row). ([Oracle Docs][1])
* **Phantom Read**: T1 runs a query (e.g. `SELECT * FROM employees WHERE salary > 5000`) and sees N rows; then T2 inserts (or deletes) rows that satisfy (or used to satisfy) that condition and commits; when T1 reruns the same query, it gets a different set of rows (new rows appear — “phantoms”). ([Oracle Docs][1])

---

## 📊 Isolation Levels vs Anomalies — Standard (ANSI/ISO) vs Oracle

Here’s the “standard” isolation-level definition vs what Oracle actually supports / behaves like. ([Oracle Docs][2])

| Isolation Level (ANSI)          | Dirty Read                      | Non-repeatable Read | Phantom Read   | Notes / Oracle Reality                                                                                                                                                                                                                                                                                    |
| ------------------------------- | ------------------------------- | ------------------- | -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **READ UNCOMMITTED**            | Possible                        | Possible            | Possible       | Not supported by Oracle — Oracle never allows dirty reads. ([Oracle Docs][3])                                                                                                                                                                                                                             |
| **READ COMMITTED**              | Not possible                    | Possible            | Possible       | Default in Oracle. Oracle provides read-consistent queries at statement-level. ([Oracle Docs][4])                                                                                                                                                                                                         |
| **REPEATABLE READ**             | Not possible                    | Not possible        | Possible       | *Not explicitly supported* by Oracle — there is no separate “REPEATABLE READ” mode; behavior is covered by Oracle’s MVCC + SNAPSHOT-style reads under READ COMMITTED for individual statements, but repeated statements in same transaction may still see changes (phantoms possible). ([Oracle Docs][5]) |
| **SERIALIZABLE**                | Not possible                    | Not possible        | Not possible   | Supported by Oracle — prevents all three phenomena during the transaction. ([Oracle Docs][4])                                                                                                                                                                                                             |
| **READ-ONLY** (Oracle-specific) | Not applicable (since no write) | Not applicable      | Not applicable | A read-only transaction sees only data committed at transaction start; cannot change data. Equivalent to a “snapshot” for reads. ([Oracle Docs][4])                                                                                                                                                       |

---

## 🛠 Examples in Oracle

Here are some explicit scenarios to illustrate what a user might see under different isolation levels (in Oracle):

### Example 1 — Non-repeatable Read under READ COMMITTED

Suppose:

* Transaction T1:

  ```sql
  SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
  -- First read:
  SELECT balance FROM accounts WHERE account_id = 42;
  -- returns: 1000
  ```
* Meanwhile, Transaction T2 does:

  ```sql
  UPDATE accounts SET balance = 1200 WHERE account_id = 42;
  COMMIT;
  ```
* Now T1 runs the same query again:

  ```sql
  SELECT balance FROM accounts WHERE account_id = 42;
  -- returns: 1200  ← changed value!
  ```

Because Oracle’s default READ COMMITTED gives each SELECT a fresh snapshot at execution time, T1 sees the updated, committed value the second time. That is a **non-repeatable read** — allowed under READ COMMITTED. ([Oracle Docs][4])

---

### Example 2 — Phantom Read under READ COMMITTED

Suppose:

* Table `employees (id, dept, salary)`.

Transaction T1:

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
-- First run:
SELECT COUNT(*) FROM employees WHERE dept = 'SALES';
-- returns: 10
```

Meanwhile, T2 inserts a new employee:

```sql
INSERT INTO employees (id, dept, salary) VALUES (101, 'SALES', 6000);
COMMIT;
```

Back in T1:

```sql
SELECT COUNT(*) FROM employees WHERE dept = 'SALES';
-- returns: 11  ← the “phantom” row appears.
```

This is a **phantom read**: the second query returns a different set because of a new committed row from another transaction. Oracle’s READ COMMITTED allows this. ([Oracle Docs][4])

---

### Example 3 — No Dirty Read ever in Oracle

In Oracle, you cannot see uncommitted changes. For instance:

* T2 does:

  ```sql
  UPDATE accounts SET balance = 0 WHERE account_id = 5;
  -- but does NOT commit yet
  ```
* T1 does:

  ```sql
  SELECT balance FROM accounts WHERE account_id = 5;
  ```
* T1 will see the **old committed** value, not the tentative zero. Even though SQL standard defines a READ UNCOMMITTED level that allows “dirty reads,” Oracle does **not** support that; dirty reads are never permitted. ([Oracle Docs][6])

Thus a classic “dirty-read anomaly” can’t happen in Oracle.

---

### Example 4 — Full Consistency under SERIALIZABLE

Suppose we start a SERIALIZABLE transaction:

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

In this mode:

* T1’s reads — once taken — will not see changes made by other transactions after T1 started.
* T1 will not observe non-repeatable reads or phantom reads.
* If T2 tries to insert or update rows that would affect data T1 reads or will read, T2 may be blocked or cause T1 to fail, depending on conflict. ([Oracle Docs][7])

For example:

* T1: `SELECT * FROM orders WHERE customer_id = 123;` returns 5 orders.
* Meanwhile T2 inserts a new order for the same customer and commits.
* If T1 reruns the same query in SERIALIZABLE mode, it will still see only the original 5 orders — the new order inserted by T2 will not appear for T1.

Thus the “phantom” is avoided, and all reads are consistent across the transaction lifespan.

---

## ✅ What this means in practice (for Oracle developers / DBAs)

* In Oracle, you don’t get “dirty reads,” even if theoretically SQL standard supports `READ UNCOMMITTED`. Oracle chooses to prevent that always. ([Oracle Docs][3])
* The default mode — `READ COMMITTED` — offers good concurrency and performance. But because it allows non-repeatable reads and phantoms, you must be careful: repeated SELECTs within the same transaction may see different data, or new rows may appear.
* If you need strong consistency across a transaction — e.g. for complex business logic, financial operations, or data integrity — choose `SERIALIZABLE`. That avoids all read phenomena, at the cost of possible concurrency conflicts.
* If you only need to read data and don’t plan to write — you can use Oracle’s `READ ONLY` mode, which gives you a stable snapshot of the database as of the transaction start.

---

[1]: https://docs.oracle.com/database/121/CNCPT/consist.htm?utm_source=chatgpt.com "Data Concurrency and Consistency"
[2]: https://docs.oracle.com/cd/B13789_01/server.101/b10743/consist.htm?utm_source=chatgpt.com "13 Data Concurrency and Consistency"
[3]: https://docs.oracle.com/cd/E36909_01/server.1111/e25789.pdf?utm_source=chatgpt.com "Oracle® Database"
[4]: https://docs.oracle.com/cd/B28359_01/server.111/b28318/consist.htm?utm_source=chatgpt.com "Data Concurrency and Consistency"
[5]: https://docs.oracle.com/database/121/ADFNS/E41452-07.pdf?utm_source=chatgpt.com "[1] Oracle® Database"
[6]: https://docs.oracle.com/cd/A83908_02/NT816EE/DOC/server.816/a76965/c23cnsis.htm?utm_source=chatgpt.com "Data Concurrency and Consistency"
[7]: https://docs.oracle.com/cd/E18283_01/server.112/e16508.pdf?utm_source=chatgpt.com "Oracle® Database"

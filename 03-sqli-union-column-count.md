# SQL Injection — UNION Attack: Determining the Number of Columns

**Lab:** SQL injection UNION attack, determining the number of columns returned by the query
**Category:** SQL Injection · UNION attacks
**Difficulty:** Practitioner
**Source:** PortSwigger Web Security Academy

> ⚠️ Testing was done in PortSwigger's deliberately vulnerable training lab. This is a learning exercise, not a finding against any real-world system.

---

## The vulnerability

The shop's product filter takes a `category` parameter and drops it straight into a SQL query:

```
GET /filter?category=Tech%20gifts
```

The value isn't sanitised, so a single quote (`'`) breaks out of the string and lets me append my own SQL. Because the query's results are reflected in the page, this is exploitable with a **UNION** attack to pull data from other tables.

But a `UNION SELECT` only works if **both** queries return the **same number of columns**. So before extracting anything, I need to find out how many columns the original query returns.

## Exploitation

Method: inject a `UNION SELECT` with a growing list of `NULL`s, one more each time, until the error stops.

`NULL` is used because it's compatible with every column data type (text, int, date), so it isolates the column-count test from any type-mismatch errors.

| Payload (`category` value) | Status |
|---|---|
| `Tech gifts' UNION SELECT NULL--` | 500 |
| `Tech gifts' UNION SELECT NULL,NULL--` | 500 |
| `Tech gifts' UNION SELECT NULL,NULL,NULL--` | **200 OK** |

Three NULLs returns **200**, so the query returns **3 columns**. Lab solved.

### Notes
- Each payload **replaces** the previous one — stacking them into one value just produces one broken query that always errors.
- The comment `--` needs a trailing space (or use `--+`) so the rest of the original query is ignored.
- Using Burp's **Inspector → Request query parameters** panel to edit the value handles URL-encoding automatically.

## Reading the result

There's no visible data in the response because every injected column is `NULL` — the row is there, it's just blank. For this lab the **200-vs-500 flip is the signal**: 500 means the query is malformed (wrong column count), 200 means it's valid.

| Code | Meaning | In this context |
|---|---|---|
| **500** Internal Server Error | Server-side error — the query threw an exception | Column count is wrong / query is broken |
| **200** OK | Request succeeded, normal page returned | Query is valid — column count matches |

## Remediation

- **Parameterised queries / prepared statements** — bind `category` as data so it's treated as a literal value, never executed as SQL.
- **Allow-list** the input where the valid set is known (e.g. category names).
- **Don't leak 500 error detail / stack traces** to the client — verbose errors make injection easier to map.

## Key takeaway

Before a UNION attack can extract data, the injected query must match the original's column count. Incrementing `NULL`s until the error clears is the standard, type-agnostic way to find that number — and the HTTP status code (500 → 200) is the feedback channel that tells you when you've got it.

## Screenshots

![200 OK response for the three-NULL payload in Burp Repeater](images/03-repeater-200.png)
*Three NULLs → `200 OK` in Burp Repeater. The earlier one- and two-NULL payloads returned 500.*

![Lab marked as solved](images/03-lab-solved.png)
*"Congratulations, you solved the lab!"*

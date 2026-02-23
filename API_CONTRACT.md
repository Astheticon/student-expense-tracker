# Student Expense Tracker – API Specification

This document defines the exact data structure and logic rules that both Web and Mobile applications must follow.

Both implementations (even if using separate backends) must strictly adhere to this contract.

---

# 1️⃣ Authentication

## Login

POST /api/login

Request:
{
  "email": "string",
  "password": "string"
}

Response (Success):
{
  "token": "jwt-token",
  "user": {
    "id": "string",
    "email": "string",
    "name": "string"
  }
}

Response (Failure):
{
  "error": "Invalid credentials"
}

---

# 2️⃣ Transaction Model (FINAL STRUCTURE)

Every transaction MUST follow this exact structure:

{
  "id": "string",
  "type": "income | expense",
  "category": "string",
  "amount": number,
  "note": "string | null",
  "date": "ISO string",
  "createdAt": "ISO string"
}

Field Rules:

- id → unique identifier (UUID recommended)
- type → must be either "income" or "expense"
- category → free text OR predefined (must match across platforms)
- amount → positive number only
- note → optional string
- date → ISO 8601 format (e.g., 2026-02-23T00:00:00.000Z)
- createdAt → server timestamp

---

# 3️⃣ Add Transaction

POST /api/transactions

Request:
{
  "type": "income",
  "category": "Food",
  "amount": 200,
  "note": "Lunch",
  "date": "2026-02-23T00:00:00.000Z"
}

Response (Success):
{
  "message": "Transaction created"
}

Response (Failure):
{
  "error": "Invalid input"
}

Rules:
- amount must be greater than 0
- type must be "income" or "expense"

---

# 4️⃣ Get Transactions

GET /api/transactions

Response:
[
  {
    "id": "uuid",
    "type": "income",
    "category": "Food",
    "amount": 200,
    "note": "Lunch",
    "date": "2026-02-23T00:00:00.000Z",
    "createdAt": "2026-02-23T10:00:00.000Z"
  }
]

Rules:
- Only return transactions belonging to the authenticated user
- Order by newest first (createdAt DESC)

---

# 5️⃣ Summary Endpoint

GET /api/summary

Response:
{
  "totalIncome": number,
  "totalExpense": number,
  "balance": number
}

Calculation Rules:

totalIncome  = sum of all transactions where type = "income"
totalExpense = sum of all transactions where type = "expense"
balance      = totalIncome - totalExpense

Balance MUST always be calculated exactly as:

balance = totalIncome - totalExpense

No alternative formula allowed.

---

# 6️⃣ Currency Rules

- Currency: INR (₹)
- All values stored as decimal numbers
- Frontend displays up to 2 decimal places
- Backend does NOT store currency symbols

---

# 7️⃣ Date & Time Rules

- All dates must be stored in ISO format
- All timestamps should use UTC
- Frontend/mobile handles local formatting

---

# 8️⃣ Error Response Format (Standardized)

All error responses must follow:

{
  "error": "Error message here"
}

No HTML responses.
No plain text responses.

Always JSON.

---

# 9️⃣ Future Extension (Optional)

Possible future endpoints:
- DELETE /api/transactions/:id
- PUT /api/transactions/:id
- GET /api/transactions?month=02&year=2026

These are optional and not mandatory for MVP.

---

# FINAL RULE

If any field name or logic needs to change,
both teams must agree and update this document first.

This document is the source of truth.

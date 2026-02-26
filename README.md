# Messenger API — Regression Test Collection

![Postman](https://img.shields.io/badge/Postman-FF6C37?style=flat&logo=postman&logoColor=white)
![Tests](https://img.shields.io/badge/tests-120-brightgreen)
![API](https://img.shields.io/badge/API-FastAPI-009688?style=flat&logo=fastapi&logoColor=white)

Postman regression suite for [fastapi-messenger](https://github.com/Didroi/fastapi-messenger).  
120 requests across 17 folders, covering validation, business logic, pagination, and access control.

---

## Files

| File | Description |
|------|-------------|
| `messenger_regression_postman_collection.json` | Test collection |
| `messenger_local_postman_environment.json` | Local environment (`base_url = http://127.0.0.1:8000`) |

---

## Test folders

| # | Folder | Requests |
|---|--------|----------|
| 1 | Health | 1 |
| 2 | Register — missing fields | 3 |
| 3 | Register — wrong types | 4 |
| 4 | Register — boundary strings | 8 |
| 5 | Register — success | 3 |
| 6 | Login | 7 |
| 7 | Current user — GET /users/me | 3 |
| 8 | Search — GET /users/search | 12 |
| 9 | Update profile — PATCH /users/me | 7 |
| 10 | Prepare user2 and user3 | 3 |
| 11 | Messages — send | 15 |
| 12 | Outbox — GET /messages/outbox | 7 |
| 13 | Inbox — GET /messages/inbox | 12 |
| 14 | Read message — POST /messages/{id}/read | 9 |
| 15 | Delete message — DELETE /messages/{id} | 9 |
| 16 | Deactivation | 7 |
| 17 | Cleanup | 4 |

---

## Coverage

**Validation (corner cases)**
- Missing required fields, wrong types (int/bool instead of string)
- Boundary strings: empty, spaces-only, special characters, 1000-char length
- Invalid path parameters, non-existent IDs, malformed UUIDs

**Business logic**
- Case-insensitive username on register and login
- Multiple active tokens per user (register token ≠ login token, both valid)
- Token remains valid after username change
- Token invalidated after account deactivation
- Message isolation: sender sees outbox, receiver sees inbox, third party sees neither
- `sender_id`, `receiver_id`, `is_read` correctness on every message
- Sending a message to yourself
- Mark as read: idempotency, `updated_at` changes only once, `created_at` unchanged
- Access control on read and delete: sender, receiver, third party
- Inbox/outbox pagination: correct page size, no overlap between pages
- Deactivated user: login blocked, all endpoints return 4xx with old token

---

## Running

**Requirements:** server running at `http://127.0.0.1:8000`

1. **Import** both JSON files into Postman
2. Select **Messenger — Local** environment
3. Right-click collection → **Run collection**
4. Keep default order, click **Run**

> Run through Collection Runner only. Requests depend on variables set by previous steps.

**Re-runs:** each run generates a unique `run_suffix` — no conflicts with previous data.

> Users are deactivated but not deleted after the run (API limitation). For full cleanup, delete data directly via DB.

---

## Test markers

- **`→ 200` / `→ 4xx`** — hard status assertion
- **`→ ЗАФИКСИРОВАТЬ`** — behavior logged, no hard assertion (valid implementation variants: spaces in text, case handling, etc.)
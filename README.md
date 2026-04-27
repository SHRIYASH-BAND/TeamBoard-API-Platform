# TeamBoard API Platform

## 📌 Overview

TeamBoard is a B2B backend API platform that provides an AI-powered knowledge base service. Companies can integrate this API into their systems (helpdesks, chatbots, developer portals) to fetch answers to technical queries.

This backend enables:

* Company registration with API key generation
* Secure authentication using JWT
* Knowledge base querying with usage tracking
* Admin analytics dashboard for platform usage

---

## 🧱 Tech Stack

* Python 3.x
* Django
* Django REST Framework (DRF)
* SimpleJWT (Authentication)
* PostgreSQL (via Docker)
* python-dotenv

---

## 🚀 Features

* 🔐 JWT-based authentication
* 🔑 Auto-generated API key per company
* 🔍 Search knowledge base (case-insensitive)
* 📊 Query logging for usage tracking
* 🛡️ Admin-only analytics endpoint
* ⚙️ Transaction-safe operations

---

## 📁 Project Setup

### 1. Clone Repository

```bash
git clone https://github.com/SHRIYASH-BAND/TeamBoard-API-Platform.git
cd TeamBoard-API-Platform
```

---

### 2. Setup Environment Variables

Create a `.env` file in root directory:

```env
DB_NAME=teamboard
DB_USER=postgres
DB_PASSWORD=postgres
DB_HOST=localhost
DB_PORT=5432
SECRET_KEY=your_django_secret_key
DEBUG=True
```

---

### 3. Run PostgreSQL via Docker

```bash
docker-compose up -d
```

Ensure PostgreSQL container is running.

---

### 4. Install Dependencies

```bash
pip install -r requirements.txt
```

---

### 5. Apply Migrations

```bash
python manage.py makemigrations
python manage.py migrate
```

---

### 6. Run Server

```bash
python manage.py runserver
```

Server runs at:

```
http://127.0.0.1:8000/
```

---

## 🔐 Authentication Flow

1. Register → Receive:

   * JWT access token
   * API key

2. Use JWT token in headers:

```
Authorization: Bearer <access_token>
```

---

## 📡 API Endpoints

---

### 1. Register Company

**POST** `/api/auth/register/`

#### Request

```json
{
  "username": "acmecorp",
  "password": "securepass123",
  "company_name": "Acme Corp",
  "email": "dev@acmecorp.com"
}
```

#### Response (201)

```json
{
  "username": "acmecorp",
  "company_name": "Acme Corp",
  "api_key": "generated_api_key",
  "access": "jwt_token"
}
```

---

### 2. Login

**POST** `/api/auth/login/`

#### Request

```json
{
  "username": "acmecorp",
  "password": "securepass123"
}
```

#### Response (200)

```json
{
  "access": "jwt_token",
  "company_name": "Acme Corp",
  "api_key": "generated_api_key"
}
```

---

### 3. Query Knowledge Base

**POST** `/api/kb/query/`

🔐 Requires JWT Token

#### Request

```json
{
  "search": "select_related"
}
```

#### Response (200)

```json
{
  "search": "select_related",
  "count": 2,
  "results": [
    {
      "id": 1,
      "question": "What is select_related?",
      "answer": "It performs SQL JOIN...",
      "category": "database"
    }
  ]
}
```

---

### 4. Admin Usage Summary

**GET** `/api/admin/usage-summary/`

🔐 Requires Admin Role

#### Response (200)

```json
{
  "total_queries": 50,
  "active_companies": 3,
  "top_search_terms": [
    { "search_term": "select_related", "count": 10 }
  ]
}
```

---

## 🧠 Core Concepts Implemented

### 🔹 Signals

* Automatically creates `Company` profile on user creation
* Generates secure API key using:

```python
secrets.token_urlsafe(32)
```

---

### 🔹 Transactions

* Query + logging wrapped inside:

```python
transaction.atomic()
```

---

### 🔹 Permissions

Custom permission:

```python
IsAdminUser
```

* Allows only companies with role `admin`

---

### 🔹 Query Logging

Every search is logged:

* Even if zero results
* Used for analytics and billing

---

## 🗄️ Database Models

* **Company** → Stores API key & role
* **KBEntry** → Knowledge base Q&A
* **QueryLog** → Tracks usage

---

## 🌱 Seeding Knowledge Base

You must insert at least 10 records manually:

### Option 1: Django Admin

```bash
python manage.py createsuperuser
```

### Option 2: Shell

```bash
python manage.py shell
```

```python
from api.models import KBEntry

KBEntry.objects.create(
    question="What is JWT?",
    answer="JSON Web Token...",
    category="api"
)
```

---

## 🧪 Testing

Use Postman collection included:

```
TeamBoard.postman_collection.json
```

### Covered Scenarios:

* Register success / duplicate
* Login success / failure
* Query with/without token
* Query with no results
* Admin access control

---

## ⚠️ Important Notes

* API key is generated via signal — NOT manually
* All protected routes require JWT
* Admin endpoint checks `company.role`, NOT Django `is_staff`
* Query logs are always created (even for 0 results)

---

## 📌 Future Improvements (Optional)

* Rate limiting per API key
* Swagger documentation
* Unit tests
* Caching for frequent queries

---

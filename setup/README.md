<h1>
  <span class="headline">Edge Cases & Boundary Testing Lab</span>
  <span class="subhead">Setup</span>
</h1>

## Setup

Before starting the lab, let’s set up your project folder, install dependencies, and create the required files for both parts of the exercise.

## 1. Create Your Project Folder

In your terminal:

```bash
mkdir edge-cases-and-boundary-testing-lab
cd edge-cases-and-boundary-testing-lab
```

## 2. Set Up a Virtual Environment

Create and activate a virtual environment:

**macOS/Linux:**

```bash
python -m venv venv
source venv/bin/activate
```

**Windows:**

```bash
python -m venv venv
venv\Scripts\activate
```

## 3. Install Dependencies

```bash
pip install fastapi uvicorn pytest
```

Check installation:

```bash
pip show fastapi
pip show pytest
```

## 4. Create Starter Files

1. Create a new API file called `auth_routes.py`

   - Copy and paste the following code into this file:

```python
from fastapi import FastAPI, HTTPException, Query
from pydantic import BaseModel, EmailStr

app = FastAPI()

# Simulated database
users_db = {
  "existing@example.com": {
      "email": "existing@example.com",
      "password": "secure123",
      "name": "Suni Example"
  },
}

class RegisterRequest(BaseModel):
    email: EmailStr
    password: str
    name: str

class LoginRequest(BaseModel):
    email: EmailStr
    password: str

@app.post("/register")
def register_user(user: RegisterRequest):
    if user.email in users_db:
        raise HTTPException(status_code=400, detail="Email already registered")
    users_db[user.email] = {
        "email": user.email,
        "password": user.password,
        "name": user.name
    }
    return {"message": "User registered successfully"}

@app.post("/login")
def login_user(user: LoginRequest):
    stored_user = users_db.get(user.email)
    if not stored_user or stored_user["password"] != user.password:
        raise HTTPException(status_code=401, detail="Invalid credentials")
    return {"message": "Login successful"}

@app.get("/status")
def get_status():
    return {"status": "ok"}
```

This app includes only the core routes for the authentication and health check flow:

- `/register`: handles new user sign-up
- `/login`: checks credentials
- `/status`: confirms the app is running

1. Create a new test file `test_auth_routes.py`

   - Copy and paste the following code into this file:

   ```python
   from fastapi.testclient import TestClient
   from auth_routes import app  # adjust if your app file has a different name

   client = TestClient(app)

   # ---------- Registration Tests ----------

   def test_register_valid_user():
       """Test normal registration flow with valid data"""
       payload = {
           "email": "newuser@example.com",
           "password": "StrongPass123",
           "name": "New User"
       }
       response = client.post("/register", json=payload)
       assert response.status_code == 200
       assert response.json()["message"] == "User registered successfully"

   # TODO: Add test for duplicate email registration
   # TODO: Add test for short name or empty name
   # TODO: Add test for very long name
   # TODO: Add test for invalid email format
   # TODO: Add test for very short password

   # ---------- Login Tests ----------

   def test_login_valid_credentials():
       """Test login with correct credentials for existing user"""
       payload = {
           "email": "existing@example.com",
           "password": "secure123"
       }
       response = client.post("/login", json=payload)
       assert response.status_code == 200
       assert response.json()["message"] == "Login successful"

   # TODO: Add test for wrong password
   # TODO: Add test for non-existent user
   # TODO: Add test for empty email
   # TODO: Add test for invalid email format

   # ---------- Status Route Test ----------

   def test_get_status_route():
       """Test that status route returns 200 and correct message"""
       response = client.get("/status")
       assert response.status_code == 200
       assert response.json() == {"status": "ok"}
   ```

## 5. Run Tests with Pytest

From the root of your `edge-cases-and-boundary-testing-lab` folder, you can run tests by running:

```bash
pytest test_auth_routes.py
```

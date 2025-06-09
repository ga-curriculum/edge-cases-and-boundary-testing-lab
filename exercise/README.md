<h1>
  <span class="headline">Edge Cases & Boundary Testing Lab</span>
  <span class="subhead">Exercise</span>
</h1>

## What Are Edge Cases?

**Edge cases** are unusual or extreme inputs that occur at the fringes of expected behavior. These cases might not be typical user inputs, but they still need to be handled by your software.

**Examples:**

- Submitting an empty string to a text field.
- Requesting page `-1` from an API.
- Uploading a file that is exactly 100MB (the system limit).
- A username that is 255 characters long.

> These are cases where logic can break, systems crash, or strange bugs surface. Real users (and malicious actors) can encounter or exploit these.

## What Are Boundary Values?

**Boundary values** are specific inputs at the edge of acceptable ranges. Often, they are the **minimum** or **maximum** values that are still valid.

**Examples:**

- Minimum password length (ex: exactly `8` characters)
- Maximum input value (ex: a quantity of `100` in a shopping cart)
- Values just outside the range (ex: `101` when the max is `100`)
- Pagination inputs like `page=0`, `page=1`, or `page=9999`

### Why does this matter?

Most off-by-one errors, rounding issues, and unexpected behavior show up around **boundaries**, not in the middle of typical data.

- **Most bugs occur at the edges.** Your software may work fine in "happy path" situations but fail when things get weird.
- **Security & reliability.** Attackers often target edge behaviors. Strong boundary checks reduce this risk.
- **Production readiness.** Real-world inputs are messy. These tests prepare your app for unusual but possible situations.

## How to Spot Edge Cases

Ask yourself:

- What happens if this input is empty?
- What if the user enters something _just outside_ the acceptable range?
- What if the maximum allowed length is hit exactly?
- What if multiple edge conditions happen at once?

These questions help you _stress test_ your app like a QA engineer.

## Strategies for testing

- Use **parameterized tests** to check multiple boundary values in one place.
- Be proactive: don’t just test what you think users will do, test what they _might_ do.
- Start with unit tests for low-level logic and expand to integration tests for API validation.

## 🔬 Lab: Edge Case & Boundary Testing with FastAPI

### Overview

You’ve been given a working FastAPI app and a test file with basic functionality tests already written. Your job is to identify edge conditions, write tests for them, and ensure your application handles these scenarios correctly.

This lab focuses on:

- Handling edge cases like empty or invalid inputs
- Testing boundaries (e.g. max name length, minimum password length)
- Ensuring robustness through proper response codes and messages

### Lab Setup

You should already have:

- `auth_routes.py` (your FastAPI app)
- `test_auth_routes.py` (your test file with placeholders)

If not, review the setup lesson and ensure both files are in place.

To run your tests:

```bash
pytest test_auth_routes.py
```

### Your Tasks

Use the TODO comments in your test file as a guide. Your task is to implement each of the following test cases, based on the functionality in `auth_routes.py`.

### Registration Route Tests

1. **Duplicate email registration**

- Try registering with `"existing@example.com"` (already in `users_db`)
- Expect 400 with message "Email already registered"

2. **Short or empty name**

- Try a payload with `name=""` or `"A"` (too short for a real name)
- Expect a 422 validation error or a 400-level failure depending on implementation

3. **Very long name**

- Try a name string of 100+ characters
- Expect success or appropriate rejection depending on how you want to limit it

4. **Invalid email format**

- Try `"not-an-email"` as the email field
- Expect a 422 validation error from Pydantic

5. **Very short password**

- Try a password like `"123"` or `"pw"`
- Expect success or failure depending on your validation needs (note: current implementation does not validate password length, so this may pass)

### Login Route Tests

6. **Wrong password**

- Use `"existing@example.com"` but the wrong password
- Expect 401 with "Invalid credentials"

7. **Non-existent user**

- Login with an unregistered email like `"ghost@example.com"`
- Expect 401

9. **Empty email**

- Try `email=""` with a valid password
- Expect a 422 validation error

10. **Invalid email format**

- Use `"not-an-email"` as the email
- Expect a 422 validation error

### 💡 Hints

- Use descriptive assert statements so errors are easy to debug.
- Use the `TestClient` pattern shown in the starter file.
- You can use Python string operations to generate long names:

```python
"A" * 200  # Creates a 200-character string
```

### Success Criteria

By the end of this lab, your test file should contain:

- At least 10 total test functions covering edge and boundary inputs
- Clear assertions for status codes and expected JSON messages
- All tests should pass or fail meaningfully based on your FastAPI logic

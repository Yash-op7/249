## How to understand the API response

The API response tells you **whether tests passed or failed** and, if a test failed, **why it failed**.

---

## 1. Check the overall run status

Look at:

`run.report.result.status`

- `PASSED` → all tests passed
    
- `FAILED` → one or more tests failed

This gives you a quick summary of the run.

---

## 2. See which tests passed or failed

All individual tests are listed under:

`run.report.testCases`

For each test case, check:

`testCases[].scenarioName testCases[].result.status`

- `scenarioName` → name of the test
    
- `result.status`
    
    - `PASSED` → test passed
        
    - `FAILED` → test failed

---

## 3. If a test failed, see why it failed

For failed tests (`result.status = FAILED`), look at:

### a) Failure reason (main explanation)

`testCases[].analysis_issue`

This explains **why the test failed** in plain text.

### b) Failure category (optional)

`testCases[].analysis`

This tells you the **type of failure** (for example, app issue, data issue, etc.).

---

## 4. (Optional) Step-level failure message

If you want the exact error message from the failing step:

`testCases[].result.message`

or

`testCases[].steps[].result.message`

This usually contains messages like:

- “Element not found”
    
- “Text not found on page”
    
- “Login failed”
    

---

## Summary (what you really need)

- **Which tests passed/failed:**  
    `run.report.testCases[].result.status`
    
- **Why a test failed:**  
    `run.report.testCases[].analysis_issue`
# Day 25: TryHackMe — Burp Suite: Repeater (Complete Module Lab)

## 📌 Project Overview
This repository contains the technical walkthroughs, execution phases, and verification evidence completed during **Day 25 of my Vulnerability Assessment and Penetration Testing (VAPT) Internship with TriosCyber** (in partnership with Ernith).

The focus of this assignment was to complete the interactive **Burp Suite: Repeater** module on **TryHackMe**. The laboratory covered advanced manual HTTP request manipulation, custom HTTP header injections, endpoint input boundary fuzzing, and UNION-based SQL injection database schema enumeration using **Burp Suite Community Edition**.

---

## 🛠️ Lab Environment & Module Details
* **Training Platform:** TryHackMe
* **Target Environment Host IP:** `10.80.184.132`
* **Primary Analysis Tool:** Burp Suite Community Edition (Proxy & Repeater)
* **Room Completion Metrics:** 🏆 **100% Completed** (9/9 Tasks, 56 Points Earned)

---

## 🚀 Step-by-Step Test Sequence & Technical Findings

### 1. Custom Header Injection & Access Control Bypass (Task 6)
Intercepted a baseline `GET /` request targeting the web server and transferred it to Burp Repeater. Injected an arbitrary custom header entry to evaluate if the application relies on client-side properties for access control boundaries:

```http
GET / HTTP/1.1
Host: 10.80.184.132
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
FlagAuthorised: True
```

* **Observed Server Response:** `HTTP/1.1 200 OK`.
* **Technical Finding:** The backend web configuration parsed and validated the arbitrary `FlagAuthorised: True` string argument as a trusted administrative flag, bypassing access bounds to render the target challenge token flag inside the response body.

---

### 2. Endpoint Input Validation Boundary Testing (Task 7)
Intercepted a legitimate structural query targeting a numeric resource path identifier (`/products/1`) and transferred the request to Burp Repeater. Manipulated the integer path parameter to an unexpected negative boundary integer to evaluate exception-handling routines:

```http
GET /products/-1 HTTP/1.1
Host: 10.80.184.132
```

* **Observed Server Response:** `HTTP/1.1 500 Internal Server Error`.
* **Technical Finding:** The application logic failed to process negative integer boundary inputs gracefully. The backend engine crashed with an unhandled server exception, breaking runtime isolation to leak the hidden challenge token error flag within the raw HTML body.

---

### 3. UNION-Based SQL Injection Exploitation & Data Extraction (Task 8)
Identified a structural UNION-based SQLi vulnerability context mapping across the parameter boundaries of the `/about/{ID}` endpoint. Leveraged Burp Repeater to systematically craft multi-staged structured database extraction queries:

#### A. Column Schema Enumeration
Queried the system metadata table schemas (`information_schema.columns`) to map the architecture fields of the active `people` data table:
```http
GET /about/0 UNION ALL SELECT column_name,null,null,null,null FROM information_schema.columns WHERE table_name="people" HTTP/1.1
```


#### B. Schema Aggregation Mapping
Aggregated the exfiltrated database parameters using the inline `group_concat()` function to extract the full asset structure map:
* **Identified Table Columns:** `id`, `firstName`, `lastName`, `pfpLink`, `role`, `shortRole`, `bio`, `notes`.

#### C. Targeted Data Extraction & Flag Retrieval
Engineered a definitive string query targeting the discovered `notes` database storage parameter row corresponding strictly to User Index `1` (CEO Jameson Wolfe):
```http
GET /about/0 UNION ALL SELECT notes,null,null,null,null FROM people WHERE id = 1 HTTP/1.1
```

* **Observed Server Response:** `HTTP/1.1 200 OK`.
* **Exfiltrated Data Output:** The database backend processed the structural injection statement directly, extracting and rendering the targeted capture-the-flag (CTF) token flag hidden within the restricted database notes row.

---

## 📊 TryHackMe Room Completion Summary Matrix

| Evaluation Metric | Technical Assignment Details |
| :--- | :--- |
| **Room Module Name** | TryHackMe: Burp Suite: Repeater |
| **User Profile Account** | `Itzumaz` |
| **Lab Tasks Completed** | 9 / 9 Mandatory Tasks |
| **Total Points Earned** | 56 Points Total |
| **Core Penetration Techniques** | Custom Header Injection, Endpoint Exception Fuzzing, UNION SQLi Database Mapping |
| **Lab Completion Status** | ✅ **COMPLETED (100%)** |

---

## 🛡️ Key Takeaways & Remediation Guidelines

### 1. Enforce Robust Server-Side Session Validation Checks
Never implement authorization frameworks that rely on custom client-controlled HTTP header strings (e.g., `FlagAuthorised: True`) or presentational layer properties to verify user privilege rights. Access state mapping constraints must be tracked and validated out-of-band at the server level using cryptographically signed session tokens or server-side state session objects.

### 2. Implement Strict Input Validation & Graceful Exception Handling
Enforce rigid server-side input type validation policies on incoming path parameters. Validate numeric routes strictly against an integer schema white-list (e.g., ensuring `id > 0`). Furthermore, catch errors gracefully by disabling verbose server debug traces in production, replacing crash logs with generic error pages to stop information disclosure leaks.

### 3. Neutralize SQL Injection via Parameterized Queries
Applications interfacing with backend database layers must never construct raw SQL queries via simple parameter string concatenation. Secure your database operations using parameterized queries (Prepared Statements). This binds all user-supplied data boundaries strictly as literal string parameters, preventing payload inputs from altering backend logic commands.

---

## 📂 Repository Structure
```plaintext
.
├── README.md
├── screenshots/
│   ├── tryhackme_completion_badge.png
│   ├── header_injection_bypass.png
│   └── burp_union_sqli_extraction.png
└── 
```

---

## 👤 Author
**Azeez Umar Opeyemi**
* 💼 **Role:** VAPT Intern at TriosCyber
* 📧 **Email:** umaropeyemiazeez@gmail.com
* 🐙 **GitHub:** [itzumaz](https://github.com/itzumaz)
* 🔗 **LinkedIn:** [Azeez Umar Opeyemi](https://www.linkedin.com/in/azeez-umar-opeyemi-201a433a4/)

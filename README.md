# Codebuddy-
AI-powered C programming diagnostic tutor built with Streamlit, SQLite, and IBM Granite for beginner engineers.

# 🤖 CodeBuddy: AI C Programming Diagnostic Tutor

> Built for the **IBM BOB 2.0 Hackathon** on Lablab.ai by **The Amateurs**.

CodeBuddy is an interactive web-based developer tool designed specifically for first-year engineering students learning C. Instead of generic code replacements, CodeBuddy translates cryptic GCC compiler outputs and subtle logical bugs into structured pedagogical explanations with a persistent personal revision journal.

---

## 🌟 Key Features

- **🔍 3-Tier Pedagogical Diagnosis:**
  - **What Went Wrong:** Plain-English explanation of syntax and logic bugs (e.g., `=` vs `==`, rogue semicolons).
  - **Core C Concept:** Deconstructs the underlying language rules (statement termination, condition evaluation, memory models).
  - **Commented Working Fix:** Provides compilable code with explanatory inline comments.
- **📜 Persistent Mistake Journal:**
  - Stores all historical debugging sessions in a local SQLite database mapped to student Roll Number/Email.
  - Enables learners to review personal recurring mistakes before practical lab exams and vivas.
- **⚡ AI Pipeline Failover:**
  - Interfaces with **IBM Granite** (`granite-3.0-8b-instruct`) via Hugging Face Inference API, backed by automated fallback routing for uninterrupted sessions.

---

## 🛠️ Tech Stack

- **Frontend:** Streamlit
- **Backend & Logic:** Python
- **Database:** SQLite3
- **AI Engine:** IBM Granite (`granite-3.0-8b-instruct`) via `huggingface_hub`
- **Architectural Co-pilot:** IBM Bob 2.0

---

## 🚀 Getting Started

### 1. Clone the Repository
```bash
git clone [https://github.com/your-username/codebuddy.git](https://github.com/your-username/codebuddy.git)
cd codebuddy

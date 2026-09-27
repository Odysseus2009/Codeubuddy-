import streamlit as st
import sqlite3
from datetime import datetime
from huggingface_hub import InferenceClient

# --- APP SETUP ---
st.set_page_config(page_title="CodeBuddy | AI C Tutor", page_icon="🤖", layout="wide")

# --- DATABASE SETUP (Local SQLite) ---
def init_db():
    conn = sqlite3.connect("codebuddy_history.db")
    c = conn.cursor()
    c.execute("""
        CREATE TABLE IF NOT EXISTS error_history (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            user_email TEXT,
            c_code TEXT,
            compiler_error TEXT,
            ai_diagnosis TEXT,
            timestamp TEXT
        )
    """)
    conn.commit()
    conn.close()

def save_history(email, code, error, explanation):
    conn = sqlite3.connect("codebuddy_history.db")
    c = conn.cursor()
    c.execute(
        "INSERT INTO error_history (user_email, c_code, compiler_error, ai_diagnosis, timestamp) VALUES (?, ?, ?, ?, ?)",
        (email, code, error, explanation, datetime.now().strftime("%d %b %Y, %I:%M %p"))
    )
    conn.commit()
    conn.close()

def fetch_history(email):
    conn = sqlite3.connect("codebuddy_history.db")
    c = conn.cursor()
    c.execute(
        "SELECT c_code, compiler_error, ai_diagnosis, timestamp FROM error_history WHERE user_email = ? ORDER BY id DESC LIMIT 5",
        (email,)
    )
    records = c.fetchall()
    conn.close()
    return records

init_db()

# --- AI DIAGNOSIS ENGINE ---
def run_ai_diagnosis(code_str, raw_error, token):
    models = [
        "ibm-granite/granite-3.0-8b-instruct",
        "Qwen/Qwen2.5-Coder-7B-Instruct",
        "meta-llama/Llama-3.2-3B-Instruct"
    ]

    err_text = raw_error.strip() if raw_error.strip() else "None provided. Analyze logical errors, syntax flaws, and undefined behaviors directly."

    prompt = (
        "You are CodeBuddy, an AI C programming tutor for first-year engineering students.\n"
        "Analyze this broken C code and compiler error. Provide a deep logical analysis, explain the bug clearly, and give the working code.\n\n"
        "Student C Code:\n"
        "```c\n" + code_str + "\n```\n\n"
        "Compiler Error Log:\n" + err_text + "\n\n"
        "Format your response strictly using these Markdown sections:\n"
        "### 1. 🔍 What Went Wrong\n"
        "(Explain the exact mistake in plain, friendly English.)\n\n"
        "### 2. 🧠 The C Concept You Need\n"
        "(Explain why C behaves this way.)\n\n"
        "### 3. 🛠️ Fixed Code\n"
        "(Provide the complete, corrected, and clean C code block.)"
    )

    clean_token = token.strip()
    client = InferenceClient(api_key=clean_token)
    
    last_error = None
    for model_name in models:
        try:
            response = client.chat.completions.create(
                model=model_name,
                messages=[{"role": "user", "content": prompt}],
                max_tokens=900,
                temperature=0.2
            )
            return response.choices[0].message.content
        except Exception as e:
            last_error = e
            continue

    raise Exception(f"AI service request failed: {last_error}")

    raise Exception(f"AI service request failed: {last_error}")
    last_error = None
    for model_name in models_to_try:
        try:
            client = InferenceClient(model=model_name, token=token)
            response = client.chat_completion(
                messages=[{"role": "user", "content": prompt}],
                max_tokens=900,
                temperature=0.2
            )
            return response.choices[0].message.content
        except Exception as e:
            last_error = e
            continue

    raise Exception(f"AI service request failed: {last_error}")


# --- SIDEBAR (Settings & History) ---
with st.sidebar:
    st.title("👤 User Profile")
    user_email = st.text_input("Enter your Email / Roll No:", placeholder="e.g. student@college.edu")
    
    st.divider()
    st.subheader("🔑 AI Credentials")
    hf_token = st.text_input("Hugging Face Access Token:", type="password", placeholder="hf_xxxxxxxxxxxxxxxxx")
    st.caption("Paste the `hf_...` token you created.")

    st.divider()
    st.subheader("📜 Recent Solved Errors")
    if user_email.strip():
        history_items = fetch_history(user_email.strip())
        if history_items:
            for item in history_items:
                with st.expander(f"🕒 {item[3]}"):
                    st.markdown("**Submitted Code:**")
                    st.code(item[0], language="c")
                    st.markdown("**AI Tutor Output:**")
                    st.write(item[2])
        else:
            st.info("No prior debug logs found for this email.")
    else:
        st.caption("Enter your email above to load your session history.")


# --- MAIN INTERFACE ---
st.title("🤖 CodeBuddy: C Language AI Tutor")
st.write("Diagnose syntax mistakes, logical bugs, and confusing GCC error messages with clear, plain-English explanations.")

st.markdown("### **Paste your C code here:**")
c_code_input = st.text_area(
    label="C Code Input",
    label_visibility="collapsed",
    height=220,
    placeholder='#include <stdio.h>\n\nint main() {\n    int x = 5;\n    if (x = 10);\n    printf("Equal\\n");\n    return 0;\n}'
)

st.markdown("### **Compiler Error message (Optional):**")
error_log_input = st.text_input(
    label="Compiler Error Input",
    label_visibility="collapsed",
    placeholder="e.g. error: expected ';' before 'return'"
)

if st.button("🚀 Diagnose with AI", type="primary"):
    if not hf_token.strip():
        st.error("Please paste your Hugging Face Token in the left sidebar.")
    elif not c_code_input.strip():
        st.warning("Please paste some C code to analyze.")
    else:
        with st.spinner("AI Tutor is analyzing your C code logic and syntax..."):
            try:
                ai_output = run_ai_diagnosis(c_code_input, error_log_input, hf_token.strip())
                
                st.divider()
                st.markdown(ai_output)
                
                if user_email.strip():
                    save_history(user_email.strip(), c_code_input, error_log_input, ai_output)
                    st.success("Analysis saved to your sidebar history!")
            except Exception as ex:
                st.error(f"Error while analyzing: {ex}")

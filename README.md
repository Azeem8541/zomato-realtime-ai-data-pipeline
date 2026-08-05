# 🍽️ Zomato Real-Time AI Data Pipeline

An end-to-end Data Engineering + AI project that simulates Zomato order/review data, loads it into Snowflake, transforms it through a Medallion Architecture (Bronze → Silver → Gold) using dbt, orchestrates everything with Apache Airflow, and layers LLM-powered AI features (review sentiment enrichment, RAG chat, and text-to-SQL) on top — all containerized with Docker.

---

![Data Engineering Pipeline Architecture](docs/architecture.png)

---

## 📌 Key Highlights & Features

- **Orchestration:** Fully automated pipeline built and scheduled with Apache Airflow 3 (running on Docker with Postgres metadata DB + API server + scheduler).
- **Cloud Data Warehouse:** Snowflake as the central warehouse — RAW (Bronze), staging (Silver), and marts (Gold) schemas.
- **Keyless Cloud Storage Integration:** Data lands in AWS S3, connected to Snowflake via a secure storage integration (no hardcoded AWS keys) using IAM role trust policies.
- **Transformation Layer (dbt):** 7 staging models (Silver) with source freshness/tests, plus dimension tables, incremental fact tables, and business marts (Gold) — using a custom schema-naming macro.
- **AI Enrichment:** An LLM (OpenAI API) enriches customer reviews with sentiment and topic tags, written back into Snowflake (`ZOMATO.AI.REVIEW_ENRICHED`).
- **RAG Chatbot:** A Streamlit app that lets you "chat with your reviews" using Retrieval-Augmented Generation.
- **Text-to-SQL Assistant:** A second Streamlit app that lets you "chat with your warehouse" in plain English, translated into SQL and run against Snowflake.
- **Infrastructure as Code:** IAM policies and S3 ↔ Snowflake trust relationships version-controlled for repeatable setup.

---

## 🛠️ Tech Stack

| Layer | Tools & Technologies |
|---|---|
| Orchestration | Apache Airflow 3 (Docker, Postgres backend) |
| Storage | AWS S3, AWS IAM (storage integration) |
| Data Warehouse | Snowflake |
| Transformation | dbt (staging + marts, tests, macros) |
| AI / LLM | OpenAI API (sentiment enrichment, RAG, text-to-SQL) |
| AI Apps / UI | Streamlit |
| Containerization | Docker, Docker Compose |
| Language | Python, SQL |

---

## 📁 Repository Structure

    zomato-realtime-ai-data-pipeline/
    │
    ├── airflow/                        # Airflow 3 on Docker
    │   ├── Dockerfile                  #   Snowflake + OpenAI providers, dbt in its own venv
    │   ├── docker-compose.yaml         #   postgres + api-server + scheduler; creds via env vars
    │   ├── example.env                 #   template for SNOWFLAKE_* / OPENAI_API_KEY
    │   └── dags/
    │       └── zomato_batch.py         #   the pipeline DAG (4 tasks)
    │
    ├── zomato/                         # dbt project
    │   ├── models/staging/             #   7 staging views (Silver) + sources + tests
    │   ├── models/marts/               #   dims, incremental facts, business marts (Gold)
    │   └── macros/                     #   custom schema-name macro
    │
    ├── ai/                             # AI layer
    │   ├── enrich_reviews.py           #   LLM enrichment → ZOMATO.AI.REVIEW_ENRICHED
    │   ├── rag_chat.py                 #   RAG — "chat with your reviews" (Streamlit)
    │   ├── text_to_sql.py              #   text-to-SQL — "chat with your warehouse" (Streamlit)
    │   └── example.env                 #   template for the AI credentials
    │
    ├── snowflake/                      # Snowflake setup SQL (run in Snowsight, in order)
    │   ├── 01_setup.sql                #   warehouse ZOMATO_WH, database ZOMATO, schemas, role
    │   ├── 02_storage_integration.sql  #   keyless S3 link (pairs with aws/iam/)
    │   ├── 03_stage_and_formats.sql    #   external stage + CSV file format
    │   ├── 04_raw_tables.sql           #   RAW (Bronze) table DDL, column order matches the CSVs
    │   └── 05_copy_into.sql            #   COPY INTO RAW from the stage
    │
    ├── aws/
    │   └── iam/                        # IAM policy + role trust policies for the S3 ↔ Snowflake handshake
    │
    ├── docs/
    │   └── architecture.png            # architecture diagram
    │
    ├── .gitignore
    └── README.md

---

## ⚙️ Setup & Installation

### 1. Prerequisites
- Docker & Docker Compose installed
- A Snowflake account (free trial works)
- An AWS account with S3 access
- An OpenAI API key

### 2. Clone the repo
```bash
git clone https://github.com/Azeem8541/zomato-realtime-ai-data-pipeline.git
cd zomato-realtime-ai-data-pipeline
```

### 3. Set up Snowflake
Run the SQL scripts in `snowflake/` in order via Snowsight:
`01_setup.sql → 02_storage_integration.sql → 03_stage_and_formats.sql → 04_raw_tables.sql → 05_copy_into.sql`

### 4. Configure AWS IAM
Apply the IAM policy and trust relationship from `aws/iam/` so Snowflake can securely read from your S3 bucket (keyless storage integration).

### 5. Configure environment variables
```bash
cp airflow/example.env airflow/.env
cp ai/example.env ai/.env
```
Set your `SNOWFLAKE_*` and `OPENAI_API_KEY` values in both files.

### 6. Start Airflow
```bash
cd airflow
docker compose up -d
```
Access the Airflow UI at `http://localhost:8080` and trigger the `zomato_batch` DAG.

### 7. Run the AI apps
```bash
cd ai
streamlit run rag_chat.py        # chat with your reviews
streamlit run text_to_sql.py     # chat with your warehouse
```
### 8. What This Project Demonstrates
  - Designing and running a Medallion Architecture (Bronze/Silver/Gold) on a real cloud warehouse
  - Setting up secure, keyless cloud storage integration between AWS and Snowflake using IAM
  - Writing production-style dbt models with tests, macros, and incremental logic
  - Orchestrating a multi-step pipeline with Airflow, fully containerized
  - Applying LLMs to real data problems: sentiment enrichment, RAG, and natural-language-to-SQL


## 👤 Author

**Azeem** — [@Azeem8541](https://github.com/Azeem8541)

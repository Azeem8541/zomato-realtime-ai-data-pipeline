🍽️ Zomato Real-Time AI Data Pipeline

An end-to-end data pipeline that loads Zomato order/review data into Snowflake, transforms it via dbt (Bronze → Silver → Gold), orchestrates everything with Apache Airflow, and adds AI features (review sentiment enrichment, RAG chat, text-to-SQL) using the OpenAI API. Fully containerized with Docker.

![Data Engineering Pipeline Architecture](docs/architecture.png)

🛠️ Tech Stack

Airflow 3 (Docker) · Snowflake · dbt · AWS S3 + IAM · OpenAI API · Streamlit

🏗️ Architecture
Mock Data → AWS S3 (Raw)
          → Snowflake RAW (Bronze)
          → dbt staging (Silver)
          → dbt marts (Gold)
          → OpenAI enrichment / RAG chat / Text-to-SQL (Streamlit)

Orchestrated end-to-end by an Airflow DAG.
📁 Repository Structure
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
│   ├── 01_setup.sql                #   warehouse, database, schemas, role
│   ├── 02_storage_integration.sql  #   keyless S3 link
│   ├── 03_stage_and_formats.sql    #   external stage + CSV file format
│   ├── 04_raw_tables.sql           #   RAW (Bronze) table DDL
│   └── 05_copy_into.sql            #   COPY INTO RAW from the stage
│
├── aws/iam/                        # IAM policy + trust policies for S3 ↔ Snowflake
├── docs/architecture.png           # architecture diagram
├── .gitignore
└── README.md
⚙️ Setup
bash
git clone https://github.com/Azeem8541/zomato-realtime-ai-data-pipeline.git
cd zomato-realtime-ai-data-pipeline
Run the SQL scripts in snowflake/ in order via Snowsight.
Apply the IAM policy from aws/iam/.
Set env vars:
bash
   cp airflow/example.env airflow/.env
   cp ai/example.env ai/.env
Start Airflow:
bash
   cd airflow && docker compose up -d

Open http://localhost:8080 and trigger the zomato_batch DAG. 5. Run the AI apps:

bash
   cd ai
   streamlit run rag_chat.py
   streamlit run text_to_sql.py
   
👤 Author
Azeem — @Azeem8541
Role: Data Engineer

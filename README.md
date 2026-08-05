# 🚴 Zomato Real-time AI Data Engineering Pipeline

Developed a scalable, end-to-end data pipeline designed to ingest real-time Zomato order and review streams, process them using Medallion Lakehouse Architecture (Bronze -> Silver -> Gold), and run automated Sentiment Analysis using LLMs (OpenAI API).

---
![Data Engineering Pipeline Architecture](docs/architecture.png)

## 📌 Key Highlights & Features
- **Real-time Event Ingestion:** Handled high-throughput streaming order events via **Apache Kafka**.
- **Medallion Lakehouse Architecture:** 
  - **Bronze:** Raw JSON landing zone on **AWS S3**.
  - **Silver:** Cleaned, deduplicated, and schema-validated data using **PySpark**.
  - **Gold:** Aggregated business metrics and sentiment tags saved in **Delta / Parquet** format.
- **AI Sentiment Engine:** Integrated OpenAI LLM to automatically classify review text into *Positive*, *Negative*, or *Neutral* along with topic extraction.
- **Automated Workflow:** Pipeline execution scheduled and monitored using **Apache Airflow**.

---

## 🏗️ Data Pipeline Architecture

[ Mock Event Generator ]
│
▼
[ Apache Kafka ]  ──(Streaming)──►  [ AWS S3 - Bronze Zone ]
│
▼
[ PySpark / AWS Glue ]
│
(Clean & Transform)
│
▼
[ AWS S3 - Silver Zone ]
│
▼
[ OpenAI LLM Sentiment ]
│
▼
[ AWS S3 - Gold Zone ]
│
▼
[ AWS Athena / SQL BI ]


---

## 🛠️ Tech Stack Used

| Category | Tools & Technologies |
| :--- | :--- |
| **Data Ingestion** | Apache Kafka, Python Scripts |
| **Storage / Data Lake** | AWS S3, Delta Lake |
| **Processing Engine** | PySpark, AWS Glue |
| **AI / LLM** | OpenAI API (GPT-3.5/4) |
| **Query Engine** | AWS Athena, SQL |
| **Orchestration** | Apache Airflow |

---

## 💡 Key Engineering Challenges I Solved

1. **Cost Optimization in Athena Queries:** Direct JSON querying on S3 was expensive and slow. I transformed raw streams into partitioned **Parquet** format in the Silver layer, reducing query latency by **~65%** and cutting scan costs.
2. **Handling Unstructured Review Data:** Standard SQL couldn't capture customer sentiment nuance. I built an asynchronous Python wrapper around the **OpenAI API** to enrich Gold layer reviews with structured sentiment metrics.

---

## 👤 Author
**Azeem**  
- **GitHub:** [Azeem8541](https://github.com/Azeem8541)  
- **Role:** Data Engineer

# 🎬 Son Tung MTP YouTube Analytics

Project to analyze **Son Tung MTP Official** channel on YouTube with end-to-end pipeline:
- **YouTube API** → Crawl data (video, playlist, stats)
- **Snowflake** →  Data warehouse
- **dbt** → Transform data (Bronze → Silver → Gold)
- **Airflow (Cosmos/Astronomer)** → Orchestrate pipeline
- **Power BI** → Dashboard

Project architecture:

<img src="./imgs/00-architecture.png" alt="Power BI Dashboard" width="800">

---

## 📂 Cấu trúc repo

```

son-tung-mtp-analytics/
├── README.md                # Hướng dẫn sử dụng (file này)
├── pyproject.toml           # Config Python project
├── uv.lock                  # Lock file dependencies
│
├── imgs/                    # Hình minh hoạ (setup & dashboard)
├── logs/                    # Logs chung
│
├── python-get-data/         # Crawl dữ liệu YouTube API
│   ├── get-data.ipynb
│   └── data/\*.csv
│
├── snowflake-create-wh/     # Script khởi tạo Snowflake
│   ├── create-wh.sql
│   └── snowflake-account.txt
│
├── power-bi-dashboard/      # Dashboard Power BI
│   ├── SonTungMTP\_Dashboard.pbix
│   └── assets
│
├── dbt\_youtube/             # Dự án dbt chính
│   ├── models/              # Bronze / Silver / Gold
│   ├── seeds/               # Seed data
│   ├── snapshots/           # Snapshots
│   ├── macros/              # Macros
│   ├── tests/               # Tests
│   ├── dbt\_project.yml
│   └── packages.yml
│
└── dbt\_youtube\_dag/         # Airflow DAG cho dbt
├── dags/                # DAG (Cosmos)
├── requirements.txt
├── Dockerfile
└── astro project files

````

---

## 🛠️ Technology:
- **Language**: Python 3.x, uv (Python package manager), Jupyter Notebook  
- **Data ingestion**: YouTube Data API v3, `google-api-python-client`, `pandas`  
- **Data warehouse**: Snowflake (Warehouse, Database, Schema)  
- **Data transformation**: dbt Core, dbt-snowflake, dbt-utils  
- **Orchestration**: Apache Airflow, Astronomer CLI, Cosmos (dbt + Airflow integration)  
- **Visualization**: Power BI Desktop / Power BI Service  
- **CI/CD & Environment**: Docker, Astronomer Runtime, `.env` secrets  
- **Version control**: GitHub  

---

## ⚙️ Enviroment

### 1. Get API Key
Create API key: [YouTube Data API v3](https://developers.google.com/youtube/v3/getting-started#example-1)

Create file `.env`:
```bash
YOUTUBE_API_KEY=YOUR_API_KEY

SNOWFLAKE_ACCOUNT=xxxx-xxxx
SNOWFLAKE_USER=....
SNOWFLAKE_PASSWORD=....
SNOWFLAKE_ROLE=....
SNOWFLAKE_WAREHOUSE=....
SNOWFLAKE_DATABASE=....
SNOWFLAKE_SCHEMA=....
````

---

### 2. Create venv và install dependencies

```bash
uv venv
source .venv/bin/activate

uv add dbt-core dbt-snowflake
uv add pandas google-api-python-client google-auth-oauthlib ipykernel python-dotenv
```

---

### 3. Snowflake

<img src="./imgs/01-snowflake-setup.png" alt="Power BI Dashboard" width="800">

Run script create warehouse/database/schema:

```bash
cd snowflake-create-wh
# Sửa create-wh.sql theo account của bạn, rồi chạy trên Snowflake UI/CLI
```

---

### 4. Crawl data YouTube

```bash
cd python-get-data
jupyter notebook get-data.ipynb
```

Export CSV →  seed dbt.

---

### 5. Run dbt

<img src="./imgs/02-dbt-run-result.png" alt="Power BI Dashboard" width="800">

```bash
cd dbt_youtube

# Install package dbt_utils
dbt deps

# Seed
dbt seed --profiles-dir .

# Run models
dbt run --profiles-dir .

# Test
dbt test --profiles-dir .

# Snapshot
dbt snapshot --profiles-dir .
```

> Docs:

```bash
dbt docs generate --profiles-dir .
dbt docs serve --profiles-dir .
```

---

### 6. Orchestrate use Airflow (Astronomer)

Install Astronomer CLI:

```bash
curl -sSL https://install.astronomer.io | sudo bash
astro version
```

Initialization project:

```bash
mkdir dbt_youtube_dag && cd dbt_youtube_dag
astro dev init
```

`requirements.txt`:

```
apache-airflow==2.9.3
apache-airflow-providers-snowflake
astronomer-cosmos>=1.3
dbt-core
dbt-snowflake
python-dotenv
```

Start Airflow:

```bash
astro dev start
```

UI: [http://localhost:8080](http://localhost:8080)

> Note: Airflow 3.0+ use `schedule` instead of `schedule_interval`.

<img src="./imgs/03-cosmos-connection-1.png" alt="Cosmos Connection 1" width="800">

<img src="./imgs/03-cosmos-connection-2.png" alt="Cosmos Connection 2" width="800">

<img src="./imgs/03-cosmos-dag.png" alt="Cosmos DAG" width="800">

---

### 7. Dashboard Power BI


<img src="./imgs/06-pbi-dashboard.png" alt="Power BI Dashboard" width="800">

 

Connect Snowflake and select table **gold** :

* g\_channel\_overview
* g\_video\_rankings
* g\_content\_mix
* g\_playlist\_performance
* g\_upload\_heatmap
* g\_duration\_distribution

---

## 🏗️ Overview architecture

1. **Crawl data** YouTube API → CSV
2. **Load Snowflake** (seed / staging)
3. **Transform dbt** (bronze → silver → gold)
4. **Orchestrate Airflow** (Cosmos DAG)
5. **Visualize Power BI**

 
---

---

## 📖 Documentation & References

- **YouTube Data API v3**  
  [https://developers.google.com/youtube/v3](https://developers.google.com/youtube/v3)

- **Snowflake Documentation**  
  [https://docs.snowflake.com](https://docs.snowflake.com)

- **dbt Core Documentation**  
  [https://docs.getdbt.com](https://docs.getdbt.com)

- **dbt-utils Package**  
  [https://hub.getdbt.com/dbt-labs/dbt_utils/latest](https://hub.getdbt.com/dbt-labs/dbt_utils/latest)

- **Apache Airflow Documentation**  
  [https://airflow.apache.org/docs](https://airflow.apache.org/docs)

- **Astronomer Cosmos (dbt + Airflow)**  
  [https://cosmos.astronomer.io](https://cosmos.astronomer.io)

- **Power BI Documentation**  
  [https://learn.microsoft.com/power-bi](https://learn.microsoft.com/power-bi)

---


## 👤 Author

**Nguyen Dinh Hoang Tuan**  
📧 ndhtuan02@gmail.com  
🔗 [LinkedIn](https://www.linkedin.com/in/tuan-nguyen-02353b378) | [GitHub](https://github.com/Devhtuan)

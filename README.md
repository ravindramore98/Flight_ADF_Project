✈️ Flight Data Pipeline — Azure Data Factory

![Azure](https://img.shields.io/badge/Azure-Data%20Factory-0078D4?logo=microsoftazure)
![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![License](https://img.shields.io/badge/License-MIT-yellow)

An end-to-end cloud data pipeline built with Azure Data Factory that ingests, transforms, and loads flight data into Azure Data Lake Storage Gen2 — ready for analytics and reporting.
---
📌 Project Overview
This project demonstrates a production-style ETL pipeline for flight data using Microsoft Azure's native data integration service. The pipeline covers the full data lifecycle: ingestion from a source, transformation using Mapping Data Flows, and loading into a structured destination — all orchestrated through ADF.
Property	Detail
Domain	Aviation / Flight Operations
Pipeline Tool	Azure Data Factory (ADF)
Storage	Azure Data Lake Storage Gen2 (ADLS Gen2)
Transformation	ADF Mapping Data Flows
Source Control	GitHub (ADF Git Integration)
Integration Runtime	Azure Integration Runtime
---
🏗️ Architecture
```
┌─────────────────┐        ┌───────────────────────────┐        ┌──────────────────────┐
│   Data Source   │──────▶ │   Azure Data Factory      │──────▶ │  ADLS Gen2 / Sink    │
│  (CSV / API /   │        │  ┌─────────────────────┐  │        │  ┌────────────────┐  │
│   Blob Storage) │        │  │  Copy Activity       │  │        │  │  Bronze Layer  │  │
└─────────────────┘        │  ├─────────────────────┤  │        │  ├────────────────┤  │
                           │  │  Mapping Data Flow   │  │        │  │  Silver Layer  │  │
                           │  ├─────────────────────┤  │        │  ├────────────────┤  │
                           │  │  Linked Services     │  │        │  │  Gold Layer    │  │
                           │  ├─────────────────────┤  │        │  └────────────────┘  │
                           │  │  Datasets            │  │        └──────────────────────┘
                           │  └─────────────────────┘  │
                           └───────────────────────────┘
```
---
📁 Repository Structure
This repository is connected to ADF via Git integration. Every change made in ADF Studio is committed here automatically.
```
Flight_ADF_Project/
│
├── pipeline/               # ADF pipeline JSON definitions
│   └── *.json              # Pipeline activities, triggers, and flow logic
│
├── dataset/                # Dataset definitions (source & sink schemas)
│   └── *.json              # Linked to storage paths and file formats
│
├── dataflow/               # Mapping Data Flow transformation logic
│   └── *.json              # Column mappings, filters, aggregations
│
├── linkedService/          # Connection definitions (ADLS, SQL, etc.)
│   └── *.json              # Credentials referenced via Key Vault
│
├── integrationRuntime/     # IR configuration (Azure / Self-hosted)
│   └── *.json
│
├── factory/                # ADF factory-level settings
│   └── *.json
│
├── publish_config.json     # ADF publish branch configuration
└── README.md
```
---
🔄 Pipeline Flow
```
1. INGEST      Raw flight data (CSV/JSON) → Azure Blob / ADLS Bronze
       ↓
2. VALIDATE    Schema validation & null checks via Data Flow
       ↓
3. TRANSFORM   Clean, deduplicate, standardize via Mapping Data Flow
       ↓
4. LOAD        Write to ADLS Silver → aggregate to Gold layer
       ↓
5. TRIGGER     Schedule trigger (daily) / Event trigger (on blob arrival)
```
Key transformations applied:
Remove duplicate flight records
Standardize date/time formats (UTC normalization)
Derive calculated columns (delay category, route code)
Filter out cancelled / invalid entries
Aggregate metrics by airline, route, and date
---
⚙️ Azure Services Used
Service	Purpose
Azure Data Factory	Pipeline orchestration and data movement
ADLS Gen2	Raw and processed data storage
Azure Integration Runtime	Compute for data movement and transformation
Azure Key Vault	Secure storage of connection strings and secrets
Azure Blob Storage	Landing zone for raw flight data files
---
🚀 How to Deploy
Prerequisites
Azure subscription (free tier works for testing)
Azure Data Factory instance created
Azure Data Lake Storage Gen2 account
Git configured on your ADF instance
Step 1 — Clone and connect to ADF
```bash
git clone https://github.com/ravindramore98/Flight_ADF_Project.git
```
In ADF Studio:
Go to Manage → Git configuration
Set repository type: GitHub
Repository name: `Flight_ADF_Project`
Collaboration branch: `main`
Publish branch: `adf_publish`
Step 2 — Set up Linked Services
Update the linked services in `linkedService/` with your own:
Storage account name
Key Vault URL (recommended — never hardcode credentials)
Step 3 — Publish
In ADF Studio, click Publish All to deploy pipelines, datasets, and data flows to your ADF instance.
Step 4 — Trigger the pipeline
Either:
Manually: Debug or Trigger Now in ADF Studio
Automatically: Set up a schedule trigger or storage event trigger
---
🔐 Security
All connection strings and secrets are managed through Azure Key Vault
Linked services reference Key Vault secrets — no credentials are hardcoded in JSON files
Access is controlled via Managed Identity (no shared keys where possible)
---
📊 Sample Output Schema
Column	Type	Description
`flight_id`	String	Unique flight identifier
`airline_code`	String	IATA airline code
`origin`	String	Departure airport code
`destination`	String	Arrival airport code
`scheduled_departure`	DateTime	Planned departure (UTC)
`actual_departure`	DateTime	Actual departure (UTC)
`delay_minutes`	Integer	Departure delay in minutes
`delay_category`	String	On-time / Minor / Major / Cancelled
`load_date`	Date	Pipeline execution date
---
🧪 Testing
To validate the pipeline end-to-end:
Upload a sample CSV to the source Blob container
Trigger the pipeline manually in ADF Studio (Debug mode)
Monitor run status under Monitor → Pipeline Runs
Verify output files appear in ADLS Silver/Gold containers
Check row counts match expected input minus filtered records
---
📈 Monitoring & Alerts
Pipeline run history visible under ADF Monitor tab
Failed runs trigger email alerts via ADF alerts configuration
Azure Monitor integration for long-term metrics and dashboards
---
🗺️ Roadmap
[x] Basic pipeline with Copy Activity
[x] Mapping Data Flow for transformations
[x] Git integration with GitHub
[ ] Azure Key Vault integration for all secrets
[ ] CI/CD with GitHub Actions → auto-publish to ADF
[ ] Tumbling window trigger for incremental loads
[ ] Azure Monitor alerts for pipeline failures
[ ] Power BI dashboard on Gold layer data
---
🤝 Contributing
Fork this repository
Create a feature branch: `git checkout -b feature/your-feature`
Commit changes: `git commit -m "Add: your feature description"`
Push and open a Pull Request
---
👤 Author
Ravindra More  
Azure Data Engineer  
GitHub
---
📄 License
This project is licensed under the MIT License.

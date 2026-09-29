# System Architecture and Data Lineage

## 1. Document Information

| Field      | Details                                                |
| ---------- | ------------------------------------------------------ |
| Project    | E-Commerce Return Risk & Revenue Intelligence Platform |
| Document   | System Architecture and Data Lineage                   |
| Version    | 1.0                                                    |
| Status     | Draft — Pending Review and Verification                |
| Phase      | Phase 0 — Architecture & Requirements                  |
| Repository | `ecommerce-return-risk-platform`                       |

## 2. Architecture Overview

The E-Commerce Return Risk & Revenue Intelligence Platform is designed as a batch-based data engineering, business intelligence, and machine learning solution.

The system will use a synthetic e-commerce dataset as its initial source. Data will first be loaded into PostgreSQL to represent an operational relational database. Databricks will then ingest data from PostgreSQL and process it through a Medallion Architecture consisting of Bronze, Silver, and Gold layers.

The Gold layer will provide curated analytical datasets for business intelligence, while a separate feature dataset will support machine learning experiments for predicting order returns.

The architecture is designed to demonstrate an end-to-end data workflow, including relational database design, data ingestion, transformation, quality validation, dimensional modeling, analytics, predictive modeling, and experiment tracking.

## 3. High-Level Data Flow

**Approved data lineage:**

```text
Synthetic E-Commerce Dataset (CSV)
                |
                v
     PostgreSQL Operational DB
                |
                v
       Databricks Ingestion
                |
                v
          Bronze Layer
       (Raw Source Data)
                |
                v
          Silver Layer
   (Cleaned and Validated Data)
                |
                v
           Gold Layer
    (Curated Analytical Data)
           /          \
          v            v
   Tableau Public   ML Feature Dataset
   (BI Reporting)          |
                           v
                     Spark ML Models
                           |
                           v
                         MLflow
```

The PostgreSQL database is the designated operational source for the Databricks ingestion process. The architecture will not introduce an independent CSV-to-Databricks ingestion path unless a technical requirement justifies the change and the decision is documented.

## 4. Architecture Components and Responsibilities

### 4.1 Source Dataset

**Technology:** CSV

The project will use a synthetic e-commerce dataset obtained from Kaggle.

Responsibilities:

* Provide the initial source records for the project.
* Serve as the input for PostgreSQL data loading.
* Support data profiling and schema discovery.
* Provide the basis for identifying data quality issues, relationships, and business definitions.

The dataset represents a simulated business environment. It must not be presented as actual company data.

The dataset's exact row count, column count, date coverage, and data grain will be confirmed during profiling.

### 4.2 Operational Database

**Technology:** PostgreSQL

PostgreSQL will serve as the relational operational source of the platform.

Responsibilities:

* Store the source data in a structured relational format.
* Support staging and normalized table design.
* Establish primary key and foreign key relationships where appropriate.
* Preserve source records and support data validation.
* Provide the source connection for Databricks ingestion.

The PostgreSQL schema will be finalized only after profiling the dataset and investigating its actual grain, duplicate patterns, and relationships.

The project will not assume that `order_id` is unique across all source records without verification.

### 4.3 Data Processing and Lakehouse

**Technology:** Azure Databricks, PySpark, Delta Lake

Databricks will serve as the primary environment for data ingestion, transformation, quality validation, and machine learning processing.

PySpark will be used for distributed data processing and transformations.

Delta Lake will provide the storage format for the Bronze, Silver, and Gold layers.

Responsibilities:

* Ingest data from PostgreSQL.
* Preserve source records in the Bronze layer.
* Clean and validate data in the Silver layer.
* Transform validated data into analytical structures in the Gold layer.
* Support reproducible processing and data quality checks.
* Prepare datasets for downstream analytics and machine learning.

### 4.4 Bronze Layer

**Purpose:** Preserve ingested source data.

The Bronze layer will contain data ingested from PostgreSQL with minimal transformation.

Responsibilities:

* Preserve source values and source-level structure.
* Maintain traceability to the originating data.
* Support investigation of data quality issues.
* Provide a reproducible starting point for downstream transformations.

Source data will not be silently deleted or modified during ingestion.

### 4.5 Silver Layer

**Purpose:** Produce cleaned and validated data.

The Silver layer will contain data that has undergone documented cleaning and standardization.

Potential transformations include:

* Data type standardization.
* Handling of missing values according to documented rules.
* Duplicate investigation and resolution.
* Date and categorical value standardization.
* Validation of business and relational constraints.
* Referential integrity checks.

Every transformation must have a documented purpose and validation result.

Duplicate records must be investigated before any removal or consolidation. No records will be silently discarded.

### 4.6 Gold Layer

**Purpose:** Provide curated analytical datasets.

The Gold layer will contain business-ready data structures designed to support reporting and analysis.

The proposed dimensional model includes:

* `dim_date`
* `dim_customer`
* `dim_product`
* `dim_location`
* `dim_campaign`
* `fact_orders`

A separate `fact_returns` table may be considered if the actual data grain and analytical requirements justify it.

The final model will be determined after profiling and defining the grain of each fact table.

Gold datasets will support the project's approved business questions and KPI definitions. Measures will not be finalized until their underlying data and business semantics have been validated.

### 4.7 Business Intelligence

**Technology:** Tableau Public

Tableau Public will be used to develop and publish the project's business intelligence dashboards.

Responsibilities:

* Visualize curated Gold-layer data.
* Present revenue and return-related metrics.
* Support analysis of trends, product performance, customer behavior, and other approved business questions.
* Communicate analytical findings through interactive visualizations.

**Connectivity consideration:** Tableau Public will require a compatible publishing workflow. The project must account for the use of published extracts or exported datasets rather than assuming a continuously live connection to the PostgreSQL database.

The final export and publishing process will be documented before dashboard implementation.

### 4.8 Machine Learning

**Technology:** PySpark ML

The machine learning component will use a separate, leakage-controlled feature dataset derived from validated data.

The initial objective is to predict whether an order will be returned using information available at or before order placement.

Candidate algorithms:

* Logistic Regression
* Random Forest
* Gradient-Boosted Trees

Model development will follow a temporal train, validation, and test split based on the dataset's actual date coverage.

Features that become available only after the return outcome must be excluded from model training.

The target definition and feature eligibility rules remain subject to validation during data profiling and feature engineering.

### 4.9 Experiment Tracking

**Technology:** MLflow

MLflow will be used to track machine learning experiments and support reproducibility.

Tracked information will include:

* Model type.
* Hyperparameters.
* Dataset and version information.
* Evaluation metrics.
* Model artifacts.
* Experiment results.

The same leakage-controlled feature set and evaluation methodology will be used when comparing candidate models.

## 5. Data Lineage and Transformation Boundaries

| Stage               | Input           | Output              | Main Responsibility                             |
| ------------------- | --------------- | ------------------- | ----------------------------------------------- |
| Source              | Synthetic CSV   | Source records      | Initial data source                             |
| PostgreSQL          | CSV records     | Relational tables   | Structured storage and source validation        |
| Ingestion           | PostgreSQL      | Bronze Delta tables | Controlled extraction and preservation          |
| Transformation      | Bronze tables   | Silver Delta tables | Cleaning, standardization, and validation       |
| Modeling            | Silver tables   | Gold Delta tables   | Dimensional modeling and analytical preparation |
| BI                  | Gold datasets   | Tableau dashboards  | Business reporting and exploration              |
| ML preparation      | Validated data  | Feature dataset     | Leakage-controlled feature preparation          |
| Model training      | Feature dataset | Trained models      | Return prediction                               |
| Experiment tracking | Training runs   | MLflow records      | Evaluation and reproducibility                  |

Each stage must have a clearly defined input, output, transformation rule, and validation process.

Data lineage must remain traceable from the original source through the downstream analytical and machine learning datasets.

## 6. Data Quality and Validation Boundaries

Data quality checks will be performed at appropriate stages rather than relying exclusively on a final validation step.

### PostgreSQL

* Confirm successful loading of source records.
* Compare loaded record counts with source counts.
* Investigate duplicate identifiers and unexpected row multiplication.
* Validate required fields and relational constraints.
* Document rejected, missing, or inconsistent records.

### Bronze

* Confirm successful ingestion from PostgreSQL.
* Validate record counts against the extracted source.
* Preserve source-level values and traceability.
* Record ingestion metadata where applicable.

### Silver

* Validate data types and required fields.
* Check date validity and categorical consistency.
* Investigate duplicate patterns.
* Validate quantity and price constraints.
* Check referential integrity.
* Document every applied transformation.

### Gold

* Validate fact-table grain.
* Check dimension and fact relationships.
* Reconcile analytical totals against validated Silver data.
* Verify KPI calculations against documented definitions.

### Machine Learning

* Validate target-label semantics.
* Exclude post-outcome features.
* Verify temporal separation of training, validation, and test datasets.
* Check class distribution and missing values.
* Confirm that evaluation uses the same feature policy across candidate models.

## 7. Technical Constraints and Risks

### 7.1 PostgreSQL-to-Databricks Connectivity

A PostgreSQL instance running on a local machine is not automatically accessible to a cloud-hosted Databricks environment.

The project must determine a secure and practical connectivity approach before implementing ingestion.

The selected approach must account for:

* Network accessibility.
* Authentication and credential management.
* Secure handling of connection details.
* Reproducibility of the ingestion process.
* Cost and operational complexity.

### 7.2 Tableau Public Data Refresh

The publishing workflow must account for Tableau Public's data connection and refresh limitations.

The architecture will retain Tableau Public as the BI platform, while the required export or extract process will be established during implementation planning.

### 7.3 Return Label Semantics

The proposed target label is based on whether `return_reason` is populated.

This definition is provisional. It must be validated against the dataset's actual meaning and completeness before being adopted for model training.

### 7.4 Data Grain and Duplicate Records

The source dataset may contain multiple records associated with the same order identifier.

The project must investigate whether these represent legitimate line items, repeated records, or other source-level structures before designing relational and analytical tables.

### 7.5 Target Leakage

The feature dataset must contain only information available at or before the prediction point.

Post-outcome fields must not be used as predictive features, even if they improve model performance.

## 8. Architecture Decisions

| Decision                              | Current Direction                     | Status   |
| ------------------------------------- | ------------------------------------- | -------- |
| Initial data source                   | Synthetic Kaggle e-commerce dataset   | Approved |
| Operational source                    | PostgreSQL                            | Approved |
| Data processing                       | Azure Databricks and PySpark          | Approved |
| Storage format                        | Delta Lake                            | Approved |
| Data organization                     | Bronze, Silver, and Gold              | Approved |
| BI platform                           | Tableau Public                        | Approved |
| Machine learning                      | Spark ML                              | Approved |
| Experiment tracking                   | MLflow                                | Approved |
| Data ingestion pattern                | Batch ingestion from PostgreSQL       | Approved |
| Independent CSV-to-Databricks path    | Not included in the approved lineage  | Approved |
| Final PostgreSQL schema               | To be determined after profiling      | Pending  |
| Final Gold dimensional model          | To be determined after grain analysis | Pending  |
| PostgreSQL-to-Databricks connectivity | To be selected                        | Open     |
| Tableau publishing workflow           | To be defined                         | Open     |
| Return target-label definition        | Subject to data validation            | Open     |

## 9. Architecture Change Control

Any proposed architecture change must include:

1. The technical or business reason for the change.
2. The limitation or requirement that prompted it.
3. The impact on existing components and data lineage.
4. The impact on project scope, complexity, and cost.
5. The proposed adjustment.
6. The decision and rationale.

Changes must be reviewed before implementation and reflected in the relevant technical documentation.

New technologies will not be introduced solely to increase the apparent complexity of the project.

## 10. Phase 0 Acceptance Criteria

This document will be considered ready for Phase 0 review when:

* [ ] The high-level architecture is documented.
* [ ] The approved data lineage is clearly represented.
* [ ] Each technology has a defined responsibility.
* [ ] Bronze, Silver, and Gold boundaries are documented.
* [ ] BI and ML data flows are distinguished.
* [ ] Data quality and validation boundaries are defined.
* [ ] Technical risks and open questions are recorded.
* [ ] Architecture decisions are traceable.
* [ ] The document is reviewed for consistency with the business and technical requirements.
* [ ] The final version is committed to GitHub.

**Document Status:** Draft — Pending Review and Verification.

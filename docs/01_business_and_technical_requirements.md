# E-Commerce Return Risk & Revenue Intelligence Platform

## 1. Document Information

| Attribute       | Details                                                |
| --------------- | ------------------------------------------------------ |
| Project Name    | E-Commerce Return Risk & Revenue Intelligence Platform |
| Document        | Business and Technical Requirements                    |
| Version         | 1.0                                                    |
| Status          | Draft — Pending Review                                 |
| Project Phase   | Phase 0 — Architecture & Requirements                  |
| Data Source     | Synthetic Kaggle e-commerce dataset                    |
| Primary Purpose | Return risk analysis and revenue intelligence          |

## 2. Project Overview

The E-Commerce Return Risk & Revenue Intelligence Platform is an end-to-end data engineering, business intelligence, and machine learning portfolio project designed to demonstrate how raw e-commerce data can be transformed into reliable analytical datasets and actionable business insights.

The platform will establish a structured data pipeline that begins with a synthetic e-commerce dataset, stores operational data in PostgreSQL, processes and transforms data through Databricks and Delta Lake, and produces analytical outputs for business intelligence and predictive modeling.

The project will demonstrate practical capabilities in relational database design, data ingestion, data quality management, dimensional modeling, business metric definition, dashboard development, machine learning, experiment tracking, and technical documentation.

**Data disclosure:** The project uses a synthetic dataset. It does not represent actual transactions, customers, or operational records from a real e-commerce company.

## 3. Business Problem

E-commerce businesses must understand how product returns affect revenue and customer behavior. However, raw transactional data may not provide a consistent foundation for analyzing these relationships or identifying potential return risks.

The platform will address the following business needs:

* Understand revenue performance and the financial impact of product returns.
* Measure return frequency and identify patterns across relevant business dimensions.
* Analyze customer, product, geographic, and temporal characteristics associated with returns.
* Establish reliable, reusable datasets for business intelligence.
* Explore whether historical transaction information can support the prediction of return risk before an order is placed.

The project will address these needs through a documented and validated data architecture rather than relying on disconnected analysis scripts or unverified dashboard calculations.

## 4. Business Objectives

1. Establish a reliable data foundation for e-commerce return and revenue analysis.
2. Implement a structured data pipeline from PostgreSQL to Databricks.
3. Transform raw records into validated and analytics-ready datasets.
4. Develop a dimensional data model that supports consistent business reporting.
5. Define transparent and reproducible business metrics.
6. Build a Tableau Public dashboard using Gold-layer analytical outputs.
7. Develop a machine learning model that estimates return risk using information available at or before order placement.
8. Track model experiments and evaluation results using MLflow.
9. Maintain complete technical documentation, validation evidence, and project history.

## 5. Stakeholders and Intended Users

| Stakeholder                        | Information Needs                                                                          |
| ---------------------------------- | ------------------------------------------------------------------------------------------ |
| Business Leadership                | Revenue performance, return impact, and overall business trends                            |
| Business Analysts                  | Consistent metrics, dimensional analysis, and reporting datasets                           |
| Data Engineers                     | Data lineage, ingestion reliability, transformation logic, and data quality                |
| Data Scientists / ML Practitioners | Leakage-controlled features, reproducible datasets, and model evaluation                   |
| Project Reviewer / Recruiter       | Clear architecture, technical implementation, validation evidence, and documented outcomes |

These stakeholders represent the intended users of the fictional platform and its portfolio demonstration.

## 6. Key Business Questions

### Revenue Intelligence

* What is the total revenue generated during a selected period?
* How does revenue change over time?
* What is the average order value?
* How do discounts affect revenue?
* How does revenue vary across products, customers, locations, and campaigns?

### Return Analysis

* How many orders are returned?
* What proportion of eligible orders are returned?
* How much revenue is associated with returned orders?
* How does return activity vary over time?
* Which products, customer segments, or locations exhibit different return patterns?
* How do return patterns relate to order value and discount levels?

### Predictive Analytics

* Can return risk be estimated using information available at order placement?
* Which pre-order characteristics are associated with predicted return risk?
* How do different classification algorithms compare?
* How well do the models identify returned orders, particularly when the target classes are imbalanced?
* Can model outputs support further analysis without being misrepresented as guaranteed outcomes?

The exact metrics, dimensional breakdowns, and analytical definitions will be finalized after source dataset profiling and data model validation.

## 7. Machine Learning Objective

The machine learning component will investigate whether historical order information can be used to predict the likelihood of an order being returned.

### Proposed Target

The initial target definition is:

* `is_returned = 1` when `return_reason` is populated.
* `is_returned = 0` when `return_reason` is missing.

This definition is provisional. It must be validated against the dataset's actual return semantics before being adopted.

### Feature Availability Rule

Only information available at or before order placement may be used as predictive features.

The following post-outcome fields are prohibited as model inputs:

* `return_reason`
* `delivery_status`
* `rating`
* `review_sentiment`
* `customer_feedback`
* `support_ticket_created`

Other fields must also be evaluated for temporal availability before inclusion.

### Proposed Models

* Logistic Regression
* Random Forest
* Gradient-Boosted Trees

### Evaluation Metrics

* ROC-AUC
* PR-AUC
* Precision
* Recall
* F1-score
* Confusion matrix

PR-AUC will receive particular attention if the returned-order class is imbalanced.

A temporal train-validation-test split will be preferred to reduce the risk of evaluating the model on information that would not have been available at prediction time.

## 8. Technical Architecture

### Approved Technology Stack

| Technology     | Responsibility                                                                   |
| -------------- | -------------------------------------------------------------------------------- |
| PostgreSQL     | Operational relational database and source for downstream ingestion              |
| Databricks     | Data engineering, distributed processing, and machine learning environment       |
| PySpark        | Data ingestion, transformation, validation, and feature preparation              |
| Delta Lake     | Reliable storage, schema management, ACID transactions, and table versioning     |
| Tableau Public | Business intelligence and analytical visualization                               |
| Spark ML       | Machine learning model development and evaluation                                |
| MLflow         | Experiment tracking, parameter logging, metric tracking, and artifact management |
| GitHub         | Version control, source code management, and project documentation               |

Each technology must support a defined project requirement. Additional technologies will not be introduced without a documented technical justification and explicit approval.

### End-to-End Data Lineage

The intended data flow is:

**Synthetic CSV Dataset → PostgreSQL → Databricks Ingestion → Bronze → Silver → Gold → Tableau Public / Machine Learning**

The lineage must remain traceable from the original source records through each transformation and analytical output.

The planned architecture does not include an independent CSV-to-Databricks ingestion path. PostgreSQL is the intended operational source for downstream ingestion.

### Layer Responsibilities

**PostgreSQL — Operational Source**

* Store the source dataset in a structured relational environment.
* Support staging and relational modeling.
* Preserve source records and enable validation before downstream ingestion.

**Bronze — Raw Data Layer**

* Preserve ingested source data.
* Maintain traceability to the source.
* Record ingestion metadata where appropriate.
* Avoid premature business transformations.

**Silver — Cleaned and Validated Layer**

* Apply documented data cleaning and standardization.
* Validate data types, required fields, relationships, and business rules.
* Investigate duplicates and anomalies.
* Preserve sufficient information for auditability and reconciliation.

**Gold — Analytical Layer**

* Provide validated, business-oriented datasets.
* Implement a dimensional model based on verified source grain and relationships.
* Support consistent KPI calculations.
* Supply analytical outputs to Tableau Public and machine learning workflows.

**Machine Learning Dataset**

* Use a separate, leakage-controlled feature dataset.
* Include only information available at or before order placement.
* Maintain traceability to the underlying Gold-layer data.

## 9. Business Intelligence Requirements

Tableau Public will be used to present the project's revenue and return intelligence.

The reporting layer must:

* Consume validated Gold-layer outputs.
* Use documented and consistent KPI definitions.
* Support relevant temporal and dimensional analysis.
* Clearly distinguish returned-order metrics from general revenue metrics.
* Avoid calculations that contradict the approved business definitions.
* Include appropriate disclosure that the underlying dataset is synthetic.

**Publishing consideration:** Tableau Public will require a suitable export or extract workflow from the Gold layer. A live database connection must not be assumed. The exact refresh and publication process will be documented before dashboard implementation.

Dashboard development is deferred until the analytical grain, dimensional model, and KPI definitions have been validated.

## 10. Data Quality Requirements

Data quality must be evaluated at each major stage of the pipeline.

### Required Quality Dimensions

* Completeness
* Validity
* Consistency
* Uniqueness
* Referential integrity
* Temporal correctness

### Initial Validation Rules

* Required identifiers must be present.
* Date fields must be valid and interpretable.
* Quantities must be greater than zero where applicable.
* Prices and monetary values must satisfy documented business rules.
* Foreign-key relationships must be validated.
* Return-label semantics must be confirmed.
* Duplicate records and repeated identifiers must be investigated.
* Source-to-target row counts and reconciliation results must be documented.

These rules are initial requirements. Their final implementation will depend on the actual dataset structure and business meaning of each field.

No records may be silently deleted or deduplicated without documented investigation, an explicit decision, and a reproducible transformation rule.

## 11. Data Modeling Requirements

The PostgreSQL schema and Gold dimensional model must be designed using evidence from source profiling.

Potential PostgreSQL entities include:

* `staging_orders_raw`
* `customers`
* `products`
* `orders`
* `order_financials`
* `order_operations`
* `order_engagement`
* `order_returns`

Potential Gold-layer dimensions include:

* `dim_date`
* `dim_customer`
* `dim_product`
* `dim_location`
* `dim_campaign`

The primary analytical fact is provisionally identified as `fact_orders`. A separate `fact_returns` table will be considered only if the verified data grain and analytical requirements justify it.

These are candidate structures, not finalized schemas.

The following must be established before finalizing the model:

1. Actual source dataset grain.
2. Meaning and uniqueness of order identifiers.
3. Relationships between orders, customers, products, and other entities.
4. Treatment of repeated order identifiers and duplicate records.
5. Financial measure definitions.
6. Return event representation.
7. Appropriate fact and dimension grains.

No uniqueness assumption will be made about `order_id` until profiling confirms its behavior.

## 12. KPI Definition Requirements

The following KPIs are planned for the analytical layer:

| KPI                          | Initial Description                             | Definition Status                    |
| ---------------------------- | ----------------------------------------------- | ------------------------------------ |
| Total Orders                 | Count of eligible orders                        | Pending grain validation             |
| Total Revenue                | Revenue generated by eligible orders            | Pending financial definition         |
| Returned Orders              | Count of returned orders                        | Pending return-label validation      |
| Returned Revenue             | Revenue associated with returned orders         | Pending financial definition         |
| Return Rate                  | Proportion of eligible orders returned          | Pending denominator definition       |
| Average Order Value          | Average revenue per eligible order              | Pending grain and revenue definition |
| Average Returned Order Value | Average revenue per returned order              | Pending financial definition         |
| Discount Rate                | Discount relative to an explicitly defined base | Pending discount definition          |

Every KPI must have a documented formula, aggregation grain, inclusion and exclusion rules, and validation approach before it is implemented in Gold or Tableau.

## 13. Technical and Operational Constraints

* The dataset is synthetic and must be represented accurately in all project materials.
* PostgreSQL is the designated operational source for downstream ingestion.
* Cloud Databricks connectivity to PostgreSQL must be addressed before pipeline implementation.
* Local database availability must not be assumed to imply cloud accessibility.
* Tableau Public publishing and refresh constraints must be considered in the reporting workflow.
* No dashboard development may precede data model and KPI validation.
* No machine learning features may use post-outcome information.
* No unapproved technologies may be added.
* All significant data transformations and technical decisions must be documented.
* Major project phases must be verified before proceeding to dependent phases.

## 14. Documentation Requirements

Documentation is a continuous project requirement, not an end-stage activity.

Each major task must record:

* Objective and rationale.
* Source data or inputs.
* Implementation approach and relevant commands or code.
* Working directory and file locations.
* Expected and actual results.
* Validation procedures and evidence.
* Errors, issues, and resolutions.
* Technical decisions and their rationale.
* Data grain and transformation logic where applicable.
* Quality results and downstream impact.
* Relevant dates and software or dataset versions.

Task states must distinguish between:

1. Planned
2. Implemented
3. Tested
4. Verified
5. Documented

A task must not be considered complete solely because its implementation exists.

## 15. Assumptions and Open Questions

The following items remain subject to verification:

| ID   | Assumption or Question                                                                 | Required Action                                                  |
| ---- | -------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| A-01 | The source dataset contains sufficient information for the planned analysis.           | Profile columns, data types, missingness, and distributions.     |
| A-02 | Missing return reasons indicate non-returned orders.                                   | Validate return semantics and target-label reliability.          |
| A-03 | Order identifiers may not be unique.                                                   | Investigate repeated identifiers and determine the actual grain. |
| A-04 | PostgreSQL can serve as the operational source for cloud ingestion.                    | Evaluate connectivity and an appropriate access method.          |
| A-05 | Gold outputs can be published to Tableau Public through an export or extract workflow. | Verify the supported publishing process.                         |
| A-06 | The dataset supports a meaningful temporal model evaluation.                           | Inspect date coverage, event timing, and class distribution.     |
| A-07 | Planned financial measures can be derived consistently.                                | Validate monetary fields and document revenue definitions.       |

These assumptions must remain open until supported by evidence.

## 16. Identified Risks

| Risk                              | Potential Impact                            | Mitigation                                                           |
| --------------------------------- | ------------------------------------------- | -------------------------------------------------------------------- |
| Incorrect source grain            | Inflated counts or inaccurate revenue       | Profile identifiers and relationships before modeling                |
| Uninvestigated duplicates         | Incorrect aggregations or data loss         | Preserve source data and document duplicate analysis                 |
| Incorrect return labels           | Unreliable model training and evaluation    | Validate target semantics before creating labels                     |
| Target leakage                    | Inflated model performance                  | Enforce feature availability rules and review features               |
| Database connectivity limitations | Ingestion delays or architectural changes   | Resolve network and access requirements before implementation        |
| Inconsistent KPI definitions      | Conflicting dashboard results               | Maintain a centralized KPI definition document                       |
| Unverified transformations        | Loss of traceability and unreliable outputs | Implement documented quality checks and reconciliation               |
| Tableau publishing limitations    | Reporting workflow disruption               | Validate the export and refresh process before dashboard development |

## 17. Project Execution Sequence

The project will follow the approved implementation order:

1. Define requirements.
2. Profile the dataset.
3. Design the PostgreSQL source schema.
4. Load data into PostgreSQL.
5. Validate PostgreSQL.
6. Design Databricks ingestion.
7. Build Bronze.
8. Build Silver.
9. Implement data-quality checks.
10. Define analytical grain.
11. Design the Gold dimensional model.
12. Build Gold.
13. Validate Gold.
14. Define business measures.
15. Develop the Tableau dashboard.
16. Prepare the ML feature dataset.
17. Train baseline models.
18. Evaluate model performance.
19. Track experiments using MLflow.
20. Consider optional Streamlit functionality only if justified.
21. Complete GitHub documentation.
22. Conduct final architecture review.

Each phase must satisfy its acceptance criteria before dependent work begins.

## 18. Phase 0 Acceptance Criteria

Phase 0 is considered complete when:

* Business objectives and stakeholder requirements are documented.
* Key business questions are defined.
* The end-to-end architecture and data lineage are documented.
* Technology choices and responsibilities are confirmed.
* Data quality and documentation standards are established.
* Risks, assumptions, and open questions are recorded.
* Technical constraints are acknowledged.
* The requirements document is reviewed and committed to the project repository.
* All Phase 0 deliverables are verified and appropriately recorded in Jira.

**Current Status:** Draft — Pending Review and Verification.

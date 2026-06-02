# Marathos Lab

This project builds a Databricks medallion pipeline for marathon race results from the 
`TWO_CENTURIES_OF_UM_RACES` dataset.

The purpose is to create a data platform that helps Marathos business stakeholders explore race results, marathon types, athlete performance, and country participation.

## AI assistance

AI tools were used for planning, syntax support, and code review. The pipeline logic, cleaning rules, validation checks, and final explanations were reviewed and tested by me.

## Tech stack

- Databricks
- Unity Catalog
- PySpark
- SQL
- Delta tables
- Git and GitHub
- Databricks dashboards / Genie

## Catalog and raw data

**Catalog:** `marathos`  
**Raw volume:** `/Volumes/marathos/default/raw/`  
**Raw file:** `/Volumes/marathos/default/raw/TWO_CENTURIES_OF_UM_RACES 2.csv`

## Project structure

```text
marathos_samuel_lab/
├── 00_setup/
├── explorations/
├── transformations/
│   ├── bronze/
│   ├── silver/
│   └── gold/
├── dimensional_modeling/
├── 04_validations/
├── docs/
├── utils/
└── README.md
```

## Medallion architecture

### Bronze

The Bronze layer stores the raw race data from the CSV file as a Delta table with minimal changes.

### Silver

The Silver layer cleans the data and creates one big table (OBT). Invalid or out-of-scope rows are removed based on documented cleaning rules.

### Gold

The Gold layer contains dimensional tables, a fact table, and views for analysis and dashboarding.

## Cleaning rules

The main cleaning decisions are documented in:

`docs/data_scope.md`

Examples of cleaning decisions:

- remove invalid day-based performance rows
- remove multi-day sub-stage events such as Etappen rows
- keep a focused marathon-like scope
- validate or recalculate speed where possible
- document why rows are removed
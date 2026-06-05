# marathos_samuel_lab
# Marathos Lab

This project builds a Databricks medallion pipeline for marathon and ultra-marathon race results from the `TWO_CENTURIES_OF_UM_RACES` dataset.

The purpose is to create a clean data platform that helps Marathos business stakeholders explore race results, athlete performance, country participation, marathon trends, and distance-based comparisons.

## AI assistance

AI tools were used for planning, syntax support, debugging, code review, dashboard guidance, and explanation support.

The pipeline logic, cleaning rules, validation checks, final notebook outputs, dashboard results, and project explanations were reviewed and tested by me.

## Tech stack

- Databricks Free Edition
- Unity Catalog
- PySpark
- SQL
- Delta tables
- Git and GitHub
- Databricks Jobs & Pipelines
- Databricks dashboards / Genie

## Catalog and raw data

**Catalog:** `marathos`  
**Schemas:** `bronze`, `silver`, `gold`  
**Raw volume:** `/Volumes/marathos/default/raw/`  
**Raw file:** `/Volumes/marathos/default/raw/TWO_CENTURIES_OF_UM_RACES 2.csv`

## Project structure

```text
marathos_samuel_lab/
├── 00_setup/
│   └── 01_create_catalog.sql
├── explorations/
│   ├── EDA_bronze.py
│   └── EDA_silver.py
├── transformations/
│   ├── bronze/
│   │   └── 01_ingest_races.sql
│   ├── silver/
│   │   ├── 01_clean_obt.py
│   │   └── cleaning_decisions
│   └── gold/
│       ├── 01_build_dimensions.sql
│       ├── 02_build_fact.sql
│       └── 03_marathon_views.sql
├── dimensional_modeling/
├── 04_validations/
│   └── 01_manual_checks.sql
├── docs/
│   └── data_scope.md
├── utils/
└── README.md
```

## Medallion architecture

This project follows the medallion architecture pattern:

```text
Raw CSV file
   ↓
Bronze layer
   ↓
Silver layer
   ↓
Gold layer
   ↓
Dashboard / Genie / Analysis
```

## Bronze layer

The Bronze layer stores the raw CSV data as a Delta table.

Table created:

```text
marathos.bronze.races_raw
```

The Bronze layer keeps the original race data mostly unchanged. Column names were renamed into simpler snake_case names so they can be used safely in SQL and Python.

The Bronze table contains:

```text
746,195 rows
```

## Silver layer

The Silver layer cleans the raw race results and creates one big table.

Table created:

```text
marathos.silver.races_obt
```

The Silver table is the cleaned OBT used for Gold modelling.

After cleaning, the Silver table contains:

```text
509,736 rows
```

This means invalid, unrealistic, or out-of-scope rows were removed.

## Gold layer

The Gold layer contains dimension tables, a fact table, and analytical views.

Dimension tables:

```text
marathos.gold.dim_event
marathos.gold.dim_athlete
marathos.gold.dim_date
```

Fact table:

```text
marathos.gold.fct_results
```

Views:

```text
marathos.gold.v_marathon_km_results
marathos.gold.v_marathon_mi_results
```

The Gold layer is used for business analysis, dashboards, and Genie.

## Dimensional model

The Gold layer uses a star schema.

The main fact table is:

```text
fct_results
```

It connects to:

```text
dim_event
dim_athlete
dim_date
```

The fact table stores measurable result values such as:

- performance seconds
- distance in kilometers
- speed in km/h
- number of finishers

The dimension tables describe the context of each result:

- event information
- athlete information
- date/year information

## Cleaning rules

The main cleaning decisions are documented in:

```text
docs/data_scope.md
```

Examples of cleaning decisions:

- remove rows with missing event name
- remove rows with missing country
- remove rows with missing gender
- remove rows with missing distance
- remove rows with missing performance time
- remove invalid performance time values
- remove unrealistic speed values above 25 km/h
- remove multi-day or sub-stage events such as Etappen rows
- remove invalid or unsupported distance formats
- focus the project on marathon-like race result analysis

This follows the lecturer’s advice that it is acceptable to remove bad or out-of-scope data if the reason is clearly documented.

## Validation

Manual validation checks were created in:

```text
04_validations/01_manual_checks.sql
```

The validation checks confirm that:

- Silver and Gold fact table row counts match
- every fact row links to an event
- every fact row links to an athlete
- every fact row links to a date
- speed values are inside the accepted cleaning rule
- dashboard views can be queried correctly

Final validation summary:

```text
silver_rows = 509736
fact_rows = 509736
missing_event_links = 0
missing_athlete_links = 0
missing_date_links = 0
max_speed_kmh = 24.79
```

This confirms that the Gold layer is connected correctly and that unrealistic speed values were removed.

## Jobs & Pipelines

A Databricks Job pipeline was created to run the notebooks in the correct order.

Pipeline name:

```text
Marathos medallion pipeline
```

Task order:

```text
create_catalog
ingest_bronze
clean_silver
build_dimensions
build_fact
build_views
validate_gold
```

The latest job run succeeded end-to-end.

Final run result:

```text
Status: Succeeded
Duration: 3m 29s
```

This confirms that the full medallion pipeline can be automated from setup to validation.

## Dashboard and analysis

A Databricks dashboard was created using the Gold views.

The dashboard includes:

- total results counter
- total athletes counter
- results by year line chart
- top athlete countries bar chart
- athlete gender distribution pie chart
- kilometer vs mile result comparison

The dashboard helps stakeholders quickly understand marathon result trends, country participation, gender distribution, and distance-unit differences.

## Example business questions

The Gold layer and dashboard can help answer questions such as:

- How many race results are available after cleaning?
- Which countries have the most athlete participation?
- How have marathon results changed over time?
- What is the gender distribution of athletes?
- How do kilometer and mile based race results compare?
- What is the average finish time for a selected country and year?

## How to run the project

Run the notebooks in this order:

```text
00_setup/01_create_catalog.sql
transformations/bronze/01_ingest_races.sql
transformations/silver/01_clean_obt.py
transformations/gold/01_build_dimensions.sql
transformations/gold/02_build_fact.sql
transformations/gold/03_marathon_views.sql
04_validations/01_manual_checks.sql
```

Alternatively, run the Databricks Job:

```text
Marathos medallion pipeline
```

This runs the full pipeline automatically.

## Final result

The project successfully creates a complete Databricks medallion pipeline with:

- Bronze raw data ingestion
- Silver cleaning and OBT creation
- Gold star schema
- analytical Gold views
- manual validation checks
- Databricks dashboard
- automated Jobs & Pipelines run

The final cleaned and validated dataset is ready for dashboarding, Genie analysis, and business exploration.
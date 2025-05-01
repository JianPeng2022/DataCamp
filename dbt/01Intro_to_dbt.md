
"data build tool", primarily handles the **T** in ELT, ETL. Aloows easy switching between data warehouses.

## 📦 01 DBT Project Structure Overview

This folder encompasses components for working with data in a **dbt** (Data Build Tool) project. The project is implemented as a structured directory and includes the following key parts:

### 🔧 Project Configuration
- `dbt_project.yml`  
  Defines the project's configuration, such as models path, materializations, and version control.

### 🛠️ Data Sources & Destinations
- `sources/`  
  Contains definitions for external data sources.
- `models/`  
  Contains SQL transformations from raw to curated datasets.
- `seeds/`  
  Stores static CSV data that can be loaded into the database.

### 🧮 SQL Queries
- `models/staging/`  
  Prepares raw data into usable form.
- `models/marts/`  
  Contains domain-specific models (e.g., finance, marketing).

### 📋 Templates
- `macros/`  
  Houses reusable SQL snippets and logic for consistent transformation.
  
### 📝 Documentation
- `docs/`  
  Markdown or YAML files for model-level documentation and descriptions.

---

🧱 **Implemented as a Folder Structure**  
This layout follows dbt best practices to ensure modular, testable, and well-documented data pipelines.


To start a new dbt project, use the `dbt init` command. This sets up the folder structure and initial configuration needed to begin developing transformations, or **dbt init <project_name>**

##  02 DBT Project Workflow



# Interactive Data Analysis on Hospital Diabetes Dataset using Semantic Layer & LLM

This repository demonstrates how to implement a **semantic layer** on top of a structured medical dataset and use a **Large Language Model (LLM)**  OpenAI’s **gpt-4o-mini**  to interact with it in natural language.  
The semantic layer adds rich metadata, column descriptions, synonyms, and categorical value mappings, enabling more **intuitive, accurate, and context-aware** data exploration without manually writing Pandas queries.

---

## Overview

We use the **UCI Diabetes 130-US hospitals for years 1999–2008 dataset** as a real world testbed for building an **LLM-powered semantic query system** capable of answering natural language questions, generating statistical analyses, and creating professional visualizations all without manually writing code.

The project demonstrates how to combine **data semantics**, **large language models**, and **safe execution** into a single workflow:

- **Semantic Layer JSON**  
  We built an automatically generated semantic layer that maps each raw dataset column to:  
  - A **human-readable description** (from UCI documentation or inferred meaning)  
  - A list of **synonyms** (e.g., “length of stay” → `time_in_hospital`, “LOS” → `time_in_hospital`)  
  - **Categorical value mappings** (e.g., `A1Cresult: ">7"` → “A1C greater than 7%”)  
  - Missing value counts and recognized data types  
  This acts as a **bridge between the user’s language and the dataset’s raw structure**, reducing ambiguity and improving LLM accuracy.

- **Natural Language Interface with gpt-4o-mini**  
  We integrated OpenAI’s **gpt-4o-mini** model to interpret free-form user queries, select the correct dataset columns based on the semantic layer, and generate valid Python code using Pandas, Matplotlib, NumPy, Statsmodels, or Scikit-learn.  
  Queries can range from basic (“Average length of stay by readmission status”) to advanced (“Run OLS regression of length of stay on age group and medical specialty and interpret the coefficients”).

- **Safe Execution Loop**  
  To prevent runtime errors and enforce best practices, we developed a **controlled execution environment** that:  
  - Preprocesses categorical variables into numeric form for modeling (one-hot encoding)  
  - Automatically handles missing values  
  - Prevents non-numeric data from being passed into regression models (avoiding the common `ValueError: Pandas data cast to numpy dtype of object`)  
  - Displays both **text results** and **visual outputs** without requiring the user to touch the code

- **Advanced Visualization & Statistical Analysis**  
  We tested and implemented a variety of analytical tasks:  
  - **Descriptive statistics**: distributions, counts, averages by category  
  - **Relationship exploration**: correlations, scatter plots, grouped boxplots  
  - **Regression analysis**: OLS models, feature significance tests  
  - **Categorical analysis**: stacked bar charts, top-K category comparisons  
  - **Annotated plots**: grouped bar charts with computed differences shown directly on the chart  
  - **EDA heatmaps** for correlation among numeric variables

- **Experiments & Iterations**  
  Throughout development, we:  
  - Tested multiple query styles from plain English to highly specific statistical requests  to measure how well gpt-4o-mini leverages the semantic layer  
  - Debugged common LLM code generation issues (mismatched column names, non-numeric dtypes, missing data handling)  
  - Added auto-cleaning pipelines so the LLM could handle ML/stats queries without manual data prep  
  - Created a growing library of **pre-tested “high-end” queries** to demonstrate advanced capabilities like ANOVA tests, mutual information ranking, and trend plots segmented by patient subgroups

This approach transforms the dataset into an **interactive, conversational analytics environment** where domain experts, researchers, or students can explore healthcare data **by simply asking questions**  making it far more accessible and faster than traditional coding-based EDA.



# Approach

We framed the problem as a **Natural Language → Code → Execution → Insight** pipeline:

1. **Semantic Layer Creation**:
   - Auto-generated from the dataset with known column descriptions (UCI metadata).
   - Includes synonyms (e.g., “length of stay” → `time_in_hospital`), value mappings (e.g., `A1Cresult: ">7"` → “A1C greater than 7%”), and missing value counts.
   - Stored as `semantic_layer.json`.

2. **Natural Language Interface**:
   - User asks: “What is the average length of stay for patients readmitted <30 days?”
   - LLM uses the semantic layer to generate correct Pandas code.

3. **Safe Execution Loop**:
   - Preprocesses data before execution (one-hot encoding for ML/statsmodels).
   - Runs code in a sandbox, captures text/plots, and prevents environment changes.

4. **Analysis Types Supported**:
   - Descriptive stats
   - Grouped aggregations
   - Advanced regression (statsmodels, scikit-learn)
   - Visualization (Matplotlib: bar, scatter, boxplot, heatmap, stacked charts)


# Example Capabilities

| Query | What Happens |
|-------|--------------|
| “Average length of stay by readmission bucket” | Groups by `readmitted` and computes mean `time_in_hospital` |
| “Top 10 specialties by <30-day readmission rate” | Filters `<30`, groups by `medical_specialty`, sorts rates |
| “Correlation between lab procedures and medications” | Computes correlation coefficient and scatter plot |
| “Histogram of medications by A1C category” | Plots `num_medications` distribution, color-coded by `A1Cresult` |
| “OLS regression of length of stay on age + medical specialty” | Encodes categoricals, fits regression model, outputs summary |


# Performance Summary

While this is not a predictive modeling benchmark, the **LLM + semantic layer** approach enables:

- **High accuracy** in column selection and filtering
- **Robust handling** of categorical/numeric preprocessing
- **Flexible query scope** — from descriptive to inferential statistics
- **Rich visualization** with minimal user technical input

## Summary of Work Done

### Data

* **Type:** Tabular CSV  
* **Rows:** 101,766  
* **Columns:** 50  
* **Key Features:** Demographics, hospital stay details, diagnoses, medications, readmission info  
* **Source:** [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/296/diabetes+130-us+hospitals+for+years+1999-2008)  

### Semantic Layer

* Column descriptions, synonyms, categorical mappings
* Missing value statistics
* JSON & Markdown export


## Problem Formulation

### Input / Output

* **Input:** Natural language query  
* **Output:** Executed Pandas code, printed results, and/or Matplotlib plots

### LLM Used

* **Model:** OpenAI **gpt-4o-mini** (fast, low-cost, schema-aware via semantic layer injection)

### Libraries

* Pandas, NumPy, Matplotlib, Statsmodels, Scikit-learn
* OpenAI API for LLM integration


## How It Works

 **Clone Repository**
 
git clone https://github.com/yourusername/diabetes-semantic-llm.git
cd diabetes-semantic-llm


**Set OpenAI API Key**\
import os
os.environ["OPENAI_API_KEY"] = "your_api_key_here"


## Run the Notebook

- **`semantic_layer_builder.ipynb`** — Generates the semantic layer from the CSV dataset.
- **`interactive_query_loop.ipynb`** — LLM-powered Q&A and visualization.


## Overview of Repository Files

- **`semantic_layer.json`** — Metadata + synonyms + value mappings.
- **`data_dictionary.md`** — Human-readable column guide.
- **`interactive_query_loop.ipynb`** — Runs natural language queries via LLM and executes results.
- **`README.md`** — Project documentation

## Future Work

- Integrate other LLMs (e.g., Claude, Llama 3.1) for offline use.
- Add multilingual query support.
- Implement SQL backend in addition to Pandas.
- Deploy as a Streamlit/Gradio web app.



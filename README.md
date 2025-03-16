# Stock Market Data Pipeline

This project implements an Apache Airflow DAG that automates the analysis of stock data. The pipeline is designed to fetch stock prices for a given symbol, process and format the data, and finally load it into a data warehouse for further analysis. It leverages various Airflow operators and integrations including Python tasks, Docker containers, and Slack notifications.

---

## Overview

The pipeline consists of the following main steps:

1. **API Availability Check**  
   A sensor task (`is_api_available`) checks whether the external stock API is available by making an HTTP request. It uses Airflow’s connection management to retrieve the API details.

2. **Data Extraction**  
   - **Get Stock Prices:** A Python task (`get_stock_prices`) calls the `_get_stock_prices` function to fetch the latest stock data for a specified symbol (e.g., AAPL).  
   - **Store Prices:** The retrieved stock data is then passed to a second Python task (`store_prices`), which stores the raw data using the `_store_prices` function.

3. **Data Transformation**  
   - **Format Prices:** A Docker task (`format_prices`) executes a containerized application (using the `airflow/stock-app` image) to process and format the stored data. This task is configured to run on a Spark master node.
   - **Get Formatted CSV:** Once the data is formatted, another Python task (`get_formatted_csv`) retrieves the path to the formatted CSV file using the `_get_formatted_csv` function.

4. **Data Loading**  
   The final step loads the formatted CSV file from an S3 bucket (via MinIO) into a PostgreSQL data warehouse. This is achieved using the Astro SDK’s `load_file` function, which handles the data ingestion and transformation into the target table (`stock_market`).

5. **Notifications**  
   Slack notifications are integrated for both successful and failed DAG runs, alerting the configured channel about the status of the pipeline.

---

## Prerequisites

Before running the pipeline, ensure you have the following:

- **Astro CLI:** Installed and configured to run Airflow.
- **Apache Airflow:** Installed and configured to run DAGs.
- **Python Environment:** Python 3.7+ with necessary packages installed (e.g., `astro`, `apache-airflow`, etc.).
- **Docker:** Docker must be installed and properly configured, as the pipeline uses a DockerOperator to run the data formatting task.
- **Airflow Connections:** The following connections must be set up in Airflow:
  - `stock_api`: Contains the host, endpoint, headers, etc.
  - `minio`: Credentials and endpoint for accessing the S3-compatible bucket.
  - `postgres`: Connection details for the target PostgreSQL data warehouse.
  - `slack`: Slack webhook/connection configuration for notifications.

---

## Installation

1. **Clone the Repository:**

   ```bash
   git clone https://your-repo-url.git
   cd your-repo-folder
   ```

2. **Ensure Docker is up and running**

3. **Run the project with the Astro CLI command**

    ```bash
    astro dev start
    ```

3. **Setup Connections in Airflow** Navigate to the Airflow UI. Under **Admin > Connections** setup the relevant connections as mentioned in the Prerequisites.

4. **Run the DAG**

5. **The Project can be brought down with the Astro CLI command**

    ```bash
    astro dev stop
    ```

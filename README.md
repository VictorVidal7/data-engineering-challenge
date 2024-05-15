# Data Engineering Challenge

A comprehensive data engineering project including ETL processes, Airflow DAG, and API integration.

## Project Overview

This repository contains a comprehensive data engineering project that demonstrates the following:

1. **ETL Script (`api_etl.py`)**:
   - Fetches data from an external API.
   - Transforms the raw data into a structured format.
   - Outputs the transformed data to a JSON file.

2. **Airflow DAG (`etl_process_dag.py`)**:
   - Automates an ETL process using Apache Airflow.
   - Extracts data from MySQL databases.
   - Transforms the data and loads it into a new MySQL table.

3. **ETL Functions (`etl_functions.py`)**:
   - Contains the functions used in the Airflow DAG for extracting, transforming, and loading data.

## File Descriptions

### `api_etl.py`
This script demonstrates an ETL process for fetching, transforming, and outputting data from an external API.

- **Fetch Data**: The script fetches data from a given API endpoint.
- **Transform Data**: The raw data is transformed into a structured JSON format focusing on specific fields like `product_id`, `product_name`, `category`, and `price`.
- **Output Data**: The transformed data is output to a JSON file.

#### Code:
```python
import requests
import logging
import json

# Configurar logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Función para extraer datos de la API
def fetch_api_data(url):
    try:
        response = requests.get(url)
        response.raise_for_status()  # Check for HTTP errors
        data = response.json()
        logging.info("Data fetched successfully from API.")
        return data
    except requests.exceptions.RequestException as e:
        logging.error(f"Error fetching data from API: {e}")
        return None

# Función para transformar datos
def transform_data(data):
    transformed_data = []
    for item in data:
        transformed_item = {
            "product_id": item.get("id"),
            "product_name": item.get("name"),
            "category": item.get("category"),
            "price": item.get("price")
        }
        transformed_data.append(transformed_item)
    logging.info("Data transformed successfully.")
    return transformed_data

# Función para salida de datos
def output_data(data, output_file):
    try:
        with open(output_file, 'w') as file:
            json.dump(data, file, indent=4)
        logging.info(f"Data successfully written to {output_file}")
    except IOError as e:
        logging.error(f"Error writing data to file: {e}")

# Función principal
def main():
    api_url = "https://fakestoreapi.com/products"  # URL de ejemplo de API pública
    output_file = "transformed_data.json"

    # Extraer datos de la API
    raw_data = fetch_api_data(api_url)
    if raw_data:
        # Transformar datos
        transformed_data = transform_data(raw_data)
        # Salida de datos
        output_data(transformed_data, output_file)

if __name__ == "__main__":
    main()
```
#### etl_process_dag.py
This script demonstrates an ETL process automated using Apache Airflow.

Extract Data: The DAG extracts data from MySQL databases sales and product.
Transform Data: The extracted data is transformed to calculate total revenue and categorize sales volume.
Load Data: The transformed data is loaded into a new MySQL table transformed_sales.

#### etl_functions.py
This script contains the functions used in the Airflow DAG for the ETL process.

### Setup Instructions
Prerequisites
Ensure you have the following installed:

Python 3.6 or later
MySQL
Apache Airflow (for the DAG part)

#### Clone the Repository
```
git clone https://github.com/tu-usuario/data-engineering-challenge.git
cd data-engineering-challenge
```

#### Install Dependencies
Create a virtual environment and install dependencies:
```
python -m venv venv
source venv/bin/activate  # On Windows use `venv\Scripts\activate`
pip install -r requirements.txt
```

#### Running the ETL Script
Run the ETL script to fetch, transform, and output API data:
```
python api_etl.py
```

### Setting Up Airflow
Initialize the Airflow Database:

```
airflow db init
```

### Create an Airflow User:
```
airflow users create \
    --username admin \
    --firstname FIRST_NAME \
    --lastname LAST_NAME \
    --role Admin \
    --email admin@example.com
```

### Start the Airflow Web Server:
```
airflow webserver --port 8080
```

### Start the Airflow Scheduler:
```
airflow scheduler
```

### Place the DAG File:
Place the etl_process_dag.py file in your Airflow DAGs directory (default is ~/airflow/dags).

### Access the Airflow UI:
Open your browser and go to http://localhost:8080 to access the Airflow UI.
Activate the DAG and monitor its execution.
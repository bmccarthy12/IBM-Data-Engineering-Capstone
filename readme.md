# IBM Data Engineering Capstone Project

## Overview
This capstone project simulated real-world data engineering tasks for SoftCart, a fictional e-commerce company. The project involved designing schemas, managing data pipelines, automating workflows, and implementing analytics solutions using industry-standard tools and programming languages. Each module focused on a key aspect of the data engineering lifecycle, from OLTP and NoSQL databases to data warehousing, big data analytics, and business intelligence.

---

## Objectives
- Designed relational and NoSQL databases to support diverse business needs.
- Developed and automated ETL pipelines to ensure seamless data integration.
- Constructed staging and production data warehouses for robust reporting and analytics.
- Built dashboards to visualize business metrics and predict trends.
- Applied tools like Python, SQL, and Linux shell scripting to automate workflows.

---

## Architecture
SoftCart’s integrated data platform leveraged the following components:
1. **OLTP Database**: MySQL for transactional data storage.
2. **NoSQL Database**: MongoDB for product catalog data.
3. **Staging Warehouse**: PostgreSQL for data transformation.
4. **Production Warehouse**: IBM Db2 on Cloud for analytics.
5. **Big Data Platform**: Hadoop and Apache Spark for analytics.
6. **ETL Pipelines**: Automated using Apache Airflow.
7. **Business Intelligence**: Dashboards created with IBM Cognos Analytics.

---

## Modules and Contributions

### Module 1: OLTP Database with MySQL
The first module focused on designing and managing an OLTP database to store transactional data such as product IDs, customer IDs, prices, quantities, and timestamps.

#### Key Tasks:
1. **Schema Design**: Created a database schema for the `sales` table:
    ```sql
    CREATE DATABASE sales;

    CREATE TABLE sales_data (
        product_id INT NOT NULL,
        customer_id INT NOT NULL,
        price INT NOT NULL,
        quantity INT NOT NULL,
        timestamp TIMESTAMP NOT NULL ON UPDATE CURRENT_TIMESTAMP
    );
    ```
2. **Data Import**: Imported data from a CSV file using phpMyAdmin and verified the data with SQL queries:
    ```sql
    SELECT COUNT(*) FROM sales_data;
    SHOW tables;
    ```
3. **Indexing**: Created an index on the `timestamp` column to optimize queries:
    ```sql
    CREATE INDEX ts ON sales_data (timestamp);
    ```
4. **Automation**: Wrote a bash script to export all rows from the database to a SQL file:
    ```bash
    #!/bin/bash
    mysqldump -u root -p sales sales_data > sales_data.sql
    ```

### Module 2: NoSQL Database with MongoDB
This module introduced NoSQL databases by configuring a MongoDB server to store product catalog data.

#### Key Tasks:
1. **Data Import**: Used `mongoimport` to load JSON data into the `electronics` collection:
    ```bash
    mongoimport --db catalog --collection electronics --file catalog.json
    ```
2. **Data Queries**: Executed queries to gain insights:
    ```javascript
    db.electronics.find({"type": "laptop"}).count();
    db.electronics.aggregate([
        { $match: { type: "smartphone" } },
        { $group: { _id: null, avgScreenSize: { $avg: "$screen_size" } } }
    ]);
    ```
3. **Data Export**: Exported selected fields to a CSV file using `mongoexport`:
    ```bash
    mongoexport --db catalog --collection electronics --fields "_id,type,model" --type=csv --out electronics.csv
    ```

### Module 3: Data Warehousing with PostgreSQL and IBM Db2
This module covered the design and implementation of staging and production data warehouses.

#### Key Tasks:
1. **Schema Design**: Created a star schema in PostgreSQL for analytics:
    ```sql
    CREATE TABLE softcartDimDate (
        DateID INT PRIMARY KEY,
        Year INT,
        Month INT,
        Day INT
    );

    CREATE TABLE softcartFactSales (
        SalesID INT PRIMARY KEY,
        DateID INT,
        ProductID INT,
        SalesAmount FLOAT,
        FOREIGN KEY (DateID) REFERENCES softcartDimDate(DateID)
    );
    ```
2. **Data Migration**: Loaded transformed data into IBM Db2 for production:
    ```sql
    INSERT INTO FactSales (DateID, ProductID, SalesAmount)
    SELECT DateID, ProductID, SUM(SalesAmount)
    FROM StagingSales
    GROUP BY DateID, ProductID;
    ```
3. **Analytics**: Developed grouping sets, rollups, and Materialized Query Tables:
    ```sql
    CREATE TABLE TotalSalesByCountry AS
    SELECT Country, SUM(SalesAmount) AS TotalSales
    FROM FactSales
    GROUP BY Country;
    ```

### Module 4: Business Intelligence Dashboards
In this module, interactive dashboards were created using IBM Cognos Analytics.

#### Key Tasks:
- Loaded CSV data into the platform and created visualizations:
    - **Bar Chart**: Quarterly sales trends.
    - **Pie Chart**: Category-wise sales.
    - **Line Chart**: Month-wise sales trends.

### Module 5: ETL Pipelines and Apache Airflow
ETL pipelines were built and automated using Python and Apache Airflow.

#### Key Tasks:
1. **Data Transformation**: Python scripts were used to clean and transform data:
    ```python
    import pandas as pd

    df = pd.read_csv('sales.csv')
    df['Year'] = pd.to_datetime(df['timestamp']).dt.year
    df.to_csv('transformed_sales.csv', index=False)
    ```
2. **Airflow DAG**: Created tasks to process web server logs:
    ```python
    from airflow import DAG
    from airflow.operators.python import PythonOperator

    def extract_logs():
        with open('access.log', 'r') as file:
            data = [line for line in file if 'html' in line]
        with open('filtered_logs.csv', 'w') as out_file:
            out_file.writelines(data)

    dag = DAG(dag_id='process_logs', schedule_interval='@daily')
    task = PythonOperator(task_id='extract_logs', python_callable=extract_logs, dag=dag)
    ```

### Module 6: Big Data Analytics with Apache Spark
Apache Spark was used for analyzing search term data and predicting sales trends.

#### Key Tasks:
1. **Data Analysis**: Loaded web server data into a Spark DataFrame:
    ```python
    from pyspark.sql import SparkSession

    spark = SparkSession.builder.appName("SearchTermAnalysis").getOrCreate()
    data = spark.read.csv("search_data.csv", header=True)
    data.groupBy("term").count().show()
    ```
2. **Sales Forecasting**: Used a pretrained model to predict sales:
    ```python
    from pyspark.ml.regression import LinearRegressionModel

    model = LinearRegressionModel.load("sales_forecasting_model")
    predictions = model.transform(data)
    predictions.show()
    ```

---

## Tools and Technologies
- **Relational Databases**: MySQL, PostgreSQL, IBM Db2
- **NoSQL Database**: MongoDB
- **Big Data Platforms**: Apache Spark, Hadoop
- **ETL Tools**: Apache Airflow
- **Business Intelligence**: IBM Cognos Analytics
- **Programming & Automation**: Python, Linux Shell Scripting
- **Development Environment**: Watson Studio, Jupyter Notebook

---

## Notable Achievements
- Built scalable and efficient pipelines to handle diverse data requirements.
- Automated workflows to improve efficiency and minimize manual intervention.
- Integrated multiple platforms to create a unified data engineering ecosystem.
- Delivered actionable insights through dashboards and predictive analytics.

---

## About the Project
This capstone project was part of the IBM Data Engineering Professional Certificate program. It provided hands-on experience with real-world tools and methodologies, demonstrating expertise in data engineering workflows.

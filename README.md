# Data-Pipeline-with-Airflow

![af_logo](https://github.com/eaamankwah/Data-Pipeline-with-Airflow/blob/main/screenshots/airflowlogo.png)

# Table of Contents
* Overview
* Project Dataset
* Star Database Schema Design
* * Fact Table
* * Dimension Tables
* Project Steps
* * Building the Operators
* * Running the Project
* * Main Project Data Files
* * Document Process
* References

## Overview
This project is fifth project of the Udacity Data Engineering Nanodegree. 

The objective of this project is to create a custom data pipelines with Apache Airflow, which includes building custom operators to perform tasks such as staging data, filling the data warehouse hosted on AWS Redshift, and running data quality checks on the data. The project makes use of more automation and monitoring to r data warehouse ETL pipelines using Apache Airflow.

## Project Dataset

The source data resides in S3 and needs to be processed in Sparkify's data warehouse in Amazon Redshift. The source datasets consist of JSON logs that tell about user activity in the application and JSON metadata about the songs the users listen to.

**Song Dataset**

Below is the path to locate the song dataset.

* Song data: s3://udacity-dend/song_data

**Log Dataset**

Below is the path to locate the log dataset.

* Log data: s3://udacity-dend/log_data

## Star Database Schema Design

A star schema was used for this project and has the following benefits:

* Queries are simpler: because all of the data connects through the fact table the multiple dimension tables are treated as one large table of information, and that makes queries simpler and easier to perform.
* Easier business insights reporting: Star schemas simplify the process of pulling business reports like "what songs users are listening to".
* Better-performing queries: by removing the bottlenecks of a highly normalized schema, query speed increases, and the performance of read-only commands improves.
* Provides data to OLAP systems: OLAP (Online Analytical Processing) systems can use star schemas to build OLAP cubes.
* Since the data set is small and structured, the star schema is sufficient to model using ERD models and can easily be queried using SQL joins

The star schema optimized for queries on song play analysis includes the tables below:

### Fact Table
The main fact table which contains all the measures associated with each event (user song plays) is shown below:

#### Songplays Table

| COLUMN      | TYPE      | 
|---    |---    |   
|   songplay_id    | INTEGER      |
|   start_time    |  TIMESTAMP    | 
|   user_id    |   INTEGER    | 
|   level    |   VARCHAR  |   
|   song_id    |   VARCHAR   | 
|   artist_id    |   VARCHAR    | 
|   session_id    |   INTEGER    |
|   location    |   VARCHAR    |  
|   user_agent    |   VARCHAR    | 

The songplay_id field has the primary key constraint.

### Dimension Tables

The dimension tables below contain detailed information about each row in the fact table.

#### User Table

| COLUMN      | TYPE      | 
|---    |---    |   
|   user_id    | INTEGER      |  
|   first_name    |    VARCHAR    |    
|   last_name    |    VARCHAR    |   
|   gender    |    VARCHAR | 
|   level    |    VARCHAR    | 

#### Songs Table

| COLUMN      | TYPE      | 
|---    |---    | 
|   song_id    |     VARCHAR     |  
|   title    |    VARCHAR    |   
|   artist_id    |    VARCHAR    |  
|   year    |   NUMERIC | 
|   duration    |   NUMERIC    |   

#### Artists Table 

| COLUMN      | TYPE      | 
|---    |---    |  
|   artist_id    |    VARCHAR     |   
|   name    |   VARCHAR    |   
|   location    |   VARCHAR    |   
|   latitude    |   NUMERIC    |   
|   longitude    |   NUMERC   | 

#### Time Table 

| COLUMN      | TYPE      | 
|---    |---    |  
|   start_time    |  TIMESTAMP      |    
|   hour    |  INTEGER    | 
|   day    |   INTEGER    | 
|   week    |   INTEGER   | 
|   month    |   INTEGER    | 
|   year    |   INTEGER    | 
|   weekday    |   INTEGER    | 


## Project Steps

### Building the Operators

Below are the four operators used to stage the data, transform the data, and run checks on data quality.

1. Stage Operator 

The stage operator is loads any JSON formatted files from S3 to Amazon Redshift. The operator creates and runs a SQL COPY statement based on the parameters provided. The operator's parameters specify where in S3 the file is loaded and what is the target table.

2. Fact Operator

This operator takes as input a SQL statement and target database on which to run the query against.

3. Dimension Operator

This operator loads data with truncate-insert pattern where the target table is emptied before the load. 

4. Data Quality Operator

This operator is use to run checks on the data itself. The operator's main functionality is to receive one or more SQL based test cases along with the expected results and execute the tests. For each the test, the test result and expected result were checked and if there is no match, the operator raises an exception and the task retry and fail eventually.


### Running the project

1. An Redshift cluster with 2 nodes were spinned.

2. The sql queries in create_tables.py was used to create database tables.

3. The Udacity Virtual workspace was used to set up the Airflow directory.

4. To run the DAG,  the command " /opt/airflow/start.sh" was run to start the Airflow web server before the DAG was updated with user login, database and Redshift cluster credentials. 

5. The codes for the DAG and plugins were loaded into the Airflow directory.

3. The  DAG was triggered and monitored to completion.

### Main Project Data Files 

* create_tables.py :  contains sql statements for creating database tables in Redshift.

* DAGs folder: contains two files:

* * sparkify_pipeline_dag.py : includes code for building the Airflow DAG.
* * subdag_factory: includes a factory method for creating tasks for the load_dimensiongs_subdag.

* plugins folder - includes four operators that stage the data, transform the data, and run quality checks on data.  In addition, the folder also includes a helper class for INSERT sql statements.

### Document Process

 The following summary steps were included in this README.md file.
1. Discussion on  the purpose of this project in context of Sparkify, and their analytical goals.
2. State and justify the database schema design and data pipeline processes
3. State the project steps and the main project data file


## References

* [AWS Redshift Cluster Console](https://us-west-2.console.aws.amazon.com/redshiftv2/home?region=us-west-2#landing.)
* [Airflow Documentation](https://airflow.apache.org/docs/)
* [Udacity Q & A Platform](https://knowledge.udacity.com/?nanodegree=nd027&page=1&project=565&rubric=2478)

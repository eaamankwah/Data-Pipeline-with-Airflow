# Apache Airflow Data Pipeline for Music Streaming Analytics

![Apache Airflow](https://github.com/eaamankwah/Data-Pipeline-with-Airflow/blob/main/screenshots/airflowlogo.png)

## Executive Summary

This project demonstrates the implementation of a scalable, automated ETL data pipeline using Apache Airflow for a music streaming service (Sparkify). The solution orchestrates data extraction from Amazon S3, transformation processes, and loading into Amazon Redshift, while implementing comprehensive data quality monitoring and validation.

**Key Technologies:** Apache Airflow, Amazon Redshift, Amazon S3, Python, SQL, Star Schema Design

## Architecture Overview

The pipeline processes JSON-formatted user activity logs and song metadata from S3 buckets, transforms the data according to business requirements, and loads it into a star schema-optimized data warehouse on Amazon Redshift. The architecture ensures data reliability through automated quality checks and monitoring.

### Data Sources
**Song Metadata**: `s3://udacity-dend/song_data` - JSON files containing song and artist information
**User Activity Logs**: `s3://udacity-dend/log_data` - JSON logs capturing user interactions and listening behavior

## Database Schema Design

### Star Schema Implementation

The data warehouse implements a star schema optimized for analytical queries, providing:
**Simplified Query Structure**: Centralized fact table with dimension table joins
**Enhanced Performance**: Optimized for OLAP operations and business intelligence reporting
**Business Intelligence Ready**: Structured for easy integration with BI tools and reporting systems

### Schema Structure

#### Fact Table: `songplays`
Central table capturing user song play events with foreign key relationships to dimension tables.

| Column | Data Type | Description | Constraints |
|--------|-----------|-------------|-------------|
| `songplay_id` | INTEGER | Unique identifier for each play event | PRIMARY KEY |
| `start_time` | TIMESTAMP | Event timestamp | NOT NULL |
| `user_id` | INTEGER | User identifier | FOREIGN KEY |
| `level` | VARCHAR | Subscription level (free/paid) | |
| `song_id` | VARCHAR | Song identifier | FOREIGN KEY |
| `artist_id` | VARCHAR | Artist identifier | FOREIGN KEY |
| `session_id` | INTEGER | User session identifier | |
| `location` | VARCHAR | User location | |
| `user_agent` | VARCHAR | User's browser/device information | |

#### Dimension Tables

**Users Table**
| Column | Data Type | Description |
|--------|-----------|-------------|
| `user_id` | INTEGER | Unique user identifier (PK) |
| `first_name` | VARCHAR | User's first name |
| `last_name` | VARCHAR | User's last name |
| `gender` | VARCHAR | User's gender |
| `level` | VARCHAR | Subscription level |

**Songs Table**
| Column | Data Type | Description |
|--------|-----------|-------------|
| `song_id` | VARCHAR | Unique song identifier (PK) |
| `title` | VARCHAR | Song title |
| `artist_id` | VARCHAR | Associated artist ID |
| `year` | NUMERIC | Release year |
| `duration` | NUMERIC | Song duration in seconds |

**Artists Table**
| Column | Data Type | Description |
|--------|-----------|-------------|
| `artist_id` | VARCHAR | Unique artist identifier (PK) |
| `name` | VARCHAR | Artist name |
| `location` | VARCHAR | Artist location |
| `latitude` | NUMERIC | Geographic latitude |
| `longitude` | NUMERIC | Geographic longitude |

**Time Table**
| Column | Data Type | Description |
|--------|-----------|-------------|
| `start_time` | TIMESTAMP | Timestamp (PK) |
| `hour` | INTEGER | Hour of day (0-23) |
| `day` | INTEGER | Day of month |
| `week` | INTEGER | Week of year |
| `month` | INTEGER | Month of year |
| `year` | INTEGER | Year |
| `weekday` | INTEGER | Day of week (0-6) |

## Technical Implementation

### Custom Airflow Operators

The pipeline leverages four custom operators designed for scalability and reusability:

#### 1. Stage Operator
**Purpose**: Efficiently loads JSON data from S3 to Redshift staging tables
**Implementation**: Utilizes optimized SQL COPY statements with configurable parameters
**Features**: 
  * Dynamic S3 path construction
  * Error handling and retry logic
  * Configurable data formatting options

#### 2. Fact Table Operator
**Purpose**: Executes complex SQL transformations to populate the central fact table
**Implementation**: Parameterized SQL execution with transaction management
**Features**:
  * Data deduplication logic
  * Performance-optimized INSERT operations
  * Referential integrity validation

#### 3. Dimension Table Operator
**Purpose**: Implements SCD Type 1 (Slowly Changing Dimensions) updates
**Implementation**: Truncate-and-load pattern with atomic transactions
**Features**:
  * Configurable truncate/append modes
  * Data freshness validation
  * Rollback capabilities on failure

#### 4. Data Quality Operator
**Purpose**: Comprehensive data validation and quality assurance
**Implementation**: SQL-based test framework with configurable assertions
**Features**:
  * Row count validation
  * Null value checks
  * Data integrity constraints
  * Custom business rule validation
  * Automated alerting on quality failures

### Pipeline Orchestration

The DAG implements the following workflow:
1. **Initialization**: Create staging and target tables if not exists
2. **Data Staging**: Parallel loading of song and log data from S3
3. **Fact Table Population**: Transform and load songplay events
4. **Dimension Table Updates**: Process user, song, artist, and time dimensions
5. **Quality Validation**: Execute comprehensive data quality checks
6. **Monitoring**: Log pipeline metrics and performance statistics

## Deployment and Operations

### Infrastructure Requirements
**Amazon Redshift**: Multi-node cluster (minimum 2 nodes recommended)
**Amazon S3**: Source data storage with appropriate IAM permissions
**Apache Airflow**: Version 2.0+ with custom plugin support

### Deployment Process
1. **Cluster Provisioning**: Deploy Redshift cluster with appropriate node configuration
2. **Schema Initialization**: Execute `create_tables.py` to establish database schema
3. **Airflow Configuration**: Configure connections and variables in Airflow UI
4. **Plugin Installation**: Deploy custom operators to Airflow plugins directory
5. **DAG Activation**: Enable and trigger the pipeline DAG

### Monitoring and Maintenance
* Real-time pipeline monitoring through Airflow UI
* Automated data quality alerts
* Performance metrics tracking
* Scheduled maintenance windows for cluster optimization

## Project Structure

```
├── dags/
│   ├── sparkify_pipeline_dag.py      # Main DAG definition
│   └── subdag_factory.py             # Sub-DAG factory for dimension loading
├── plugins/
│   ├── operators/                    # Custom Airflow operators
│   └── helpers/                      # SQL helper classes
├── sql/
│   └── create_tables.py              # Database schema creation scripts
└── README.md                         # Project documentation
```

## Performance Optimizations

**Parallel Processing**: Concurrent staging operations for song and log data
**Optimized SQL**: Efficient JOIN operations and indexing strategies
**Resource Management**: Dynamic task concurrency based on cluster capacity
**Data Partitioning**: Time-based partitioning for improved query performance

## Business Impact

This automated pipeline enables Sparkify to:
**Scale Analytics**: Handle growing data volumes without manual intervention
**Ensure Data Quality**: Maintain high data integrity through automated validation
**Enable Real-time Insights**: Support business intelligence and reporting requirements
**Reduce Operational Overhead**: Minimize manual data processing tasks
**Improve Decision Making**: Provide reliable, timely data for strategic decisions

## Future Enhancements

* Implementation of CDC (Change Data Capture) for real-time updates
* Integration with data lineage tracking systems
* Advanced anomaly detection in data quality checks
* Machine learning model integration for predictive analytics
* Multi-region deployment for disaster recovery

---

**Technical Lead**: Edward  
**Project Duration**: 1 week  
**Last Updated**: July 2025


## References

* [AWS Redshift Cluster Console](https://us-west-2.console.aws.amazon.com/redshiftv2/home?region=us-west-2#landing.)
* [Airflow Documentation](https://airflow.apache.org/docs/)
* [Udacity Q & A Platform](https://knowledge.udacity.com/?nanodegree=nd027&page=1&project=565&rubric=2478)

# Data Modeling with Apache Cassandra
>
In this project, a NoSQL database is created by using Apache Cassandra<sup>[1](#myfootnote1)</sup> to analyze a dataset, which contain the songs and the user activities in CSV format. An ETL pipeline with tables created to fit the queries are presented in the jupyter notebook.

<a name="myfootnote1"><sup>1</sup></a> Data Modeling in Apache Cassandra:

* Denormalization is not just okay -- it's a must

* Denormalization must be done for fast reads

* Apache Cassandra has been optimized for fast writes

* ALWAYS think Queries first

* One table per query is a great strategy

* Apache Cassandra does not allow for JOINs between tables

In Apache Cassandra, you want to model your data to your queries, and if your business need calls for quickly changing requirements, you need to create a new table to process the data. That is a requirement of Apache Cassandra. If your business needs calls for ad-hoc queries, these are not a strength of Apache Cassandra. However keep in mind that it is easy to create a new table that will fit your new query.

## Table of contents

* [Introduction](#introduction)
* [Project Description](#project-description)
* [Dataset](#dataset)
* [Project Steps](#project-steps)
* [Files in this Project](#files-in-this-project)
* [Project Instructions](#project-instructions)

## Introduction

A startup called Sparkify wants to analyze the data they've been collecting on songs and user activity on their new music streaming app. The analysis team is particularly interested in understanding what songs users are listening to. Currently, there is no easy way to query the data to generate the results, since the data reside in a directory of CSV files on user activity on the app.

As a data engineer, my goal is to create an Apache Cassandra database which can create queries on song play data to answer the questions. I will also be able to test the database by running queries given to me by the analytics team from Sparkify to create the results.

## Project Description
* Use data modeling with Apache Cassandra to build an ETL pipeline using Python.

* Model the data by creating tables in Apache Cassandra to run queries. Part of the ETL pipeline that transfers data from a set of CSV files are provided within a directory to create a streamlined CSV file to model and insert data into Apache Cassandra tables.

## Dataset
The data is presented in the **event_data** folder. The directory of CSV files partitioned by date. Here are examples of filepaths to two files in the dataset:

```
event_data/2018-11-08-events.csv
event_data/2018-11-09-events.csv
```
## Project Steps
Below are steps you can follow to complete each component of this project:

### Modeling your NoSQL database or Apache Cassandra database
1. Design tables to answer the queries outlined in the project template.

2. Write Apache Cassandra `CREATE KEYSPACE` and `SET KEYSPACE` statements.

3. Develop the `CREATE` statement for each of the tables to address each question.

4. Load the data with `INSERT` statement for each of the tables.

5. Include `IF NOT EXISTS` clauses in your `CREATE` statements to create tables only if the tables do not already exist. Also, include `DROP TABLE` statement for each table, so the database can drop and create tables whenever you want to reset your database and test your ETL pipeline.

6. Test by running the proper select statements with the correct `WHERE` clause.

### Build ETL Pipeline
1. Implement the logic in section Part I of the notebook template to iterate through each event file in `event_data` to process and create a new CSV file in Python.

2. Make necessary edits to Part II of the notebook template to include Apache Cassandra `CREATE` and `INSERT` statements to load processed records into relevant tables in your data model.

3. Test by running `SELECT` statements after running the queries on the database.

## Files in this Project
In addition to the data files in the `event_data` folder, the project workspace includes three files:

1. `event_datafile_new.csv` is the file generated from the ***Part I. ETL Pipeline for Pre-Processing the Files*** in file `project_etl.ipynb` listed below. The image below is a screenshot of what the denormalized data should appear like in the **event_datafile_new.csv** after the code above is run:

![event_datafile_new-image](/images/image_event_datafile_new.jpg)

2. `project_etl.ipynb` reads and processes the csv files from **event_data** folder that will be used for Apache Casssandra tables. This notebook also contains detailed instructions on the ETL process for each of the tables.

3. `README.md` provides discussion on the project.

## Project Instructions
1. Installation of Apache Cassandra in the local machine is required to run the code locally. See the link on [how to install Apach Cassandra](https://cassandra.apache.org/doc/latest/getting_started/installing.html).

2. Follow each step in the jupyter notebook `project_etl.ipynb` to finish the project.

## Notes on NoSQL Database and Apache Cassandra

### When to use a NoSQL database

* Need **high Availability** in the data: Indicates the system is always up and there is no downtime.

* Have **Large Amounts** of Data.

* Need **Linear Scalability**: The need to add more nodes to the system so performance will increase linearly.

* **Low Latency**: Shorter delay before the data is transferred once the instruction for the transfer has been received.

* Need fast reads and write

### The CAP Theorem

It is impossible for a distributed data store to simultaneously provide more than two out of three guarantees of **C**onsistency, **A**vailability, and **P**artition tolerance.

* Consistency: Every read from the database gets the latest (and correct) piece of data or an error

* Availability: Every request is received and a response is given -- without a guarantee that the data is the latest update

* Partition Tolerance: The system continues to work regardless of losing network connectivity between nodes

There is no such thing as Consistency and Availability in a distributed database since it must always tolerate network issues. You can only have Consistency and Partition Tolerance (CP) or Availability and Partition Tolerance (AP). Remember, relational and non-relational databases do different things, and that's why most companies have both types of database systems.

Q. How's the consistency in ACID different from the consistency in CAP?

A. Consistency in the ACID principle refers to the requirement that only transactions that abide by constraints and database rules are written into the database, otherwise the database keeps previous state. In other words, the data should be correct across all rows and tables. However, consistency in the CAP theorem refers to every read from the database getting the latest piece of data or an error.

### CQL - Cassandra Query Language

CQL is the way to interact with the database and is very similar to SQL. However, JOINs, GROUP BY, and subqueries are not in CQL.

### Partition Key

* Must be unique

* The PRIMARY KEY is made up of either just the PARTITION KEY or may also include additional CLUSTERING COLUMNS

* A Simple PRIMARY KEY is just one column that is also the PARTITION KEY. A Composite PRIMARY KEY is made up of more than one column and will assist in creating a unique value and in your retrieval queries

* The PARTITION KEY will determine the distribution of data across the system

EXAMPLES

**Primary Key Simple (Only one Partition Key)**
```SQL
CREATE TABLE music_library (
    year int,
    artist_name text,
    album_name text,
    PRIMARY KEY(year)
)
```

**Primary Key Composite (Two Partition Keys, hashed together)**
```SQL
CREATE TABLE music_library (
    year int,
    artist_name text,
    album_name text,
    PRIMARY KEY(year, artist_name)
)
```

**Clustering Columns (One Partition Key + One or more Clustering Columns)**
```SQL
CREATE TABLE music_library (
    year int,
    artist_name text,
    album_name text,
    PRIMARY KEY((year), artist_name)
)
```

### Clustering Columns

* The clustering column will sort the data in sorted ascending order, e.g., alphabetical order. 

* More than one clustering column can be added (or none!)

* From there the clustering columns will sort in order of how they were added to the primary key

You can use as many clustering columns as you would like. You cannot use the clustering columns out of order in the SELECT statement. You may choose to omit using a clustering column in your SELECT statement. That's OK. Just remember to use them in order when you are using the SELECT statement.

### WHERE clause

* Data Modeling in Apache Cassandra is query focused, and that focus needs to be on the WHERE clause.

* Failure to include a WHERE clause will **result in an error**.

The WHERE statement is allowing us to do the fast reads. With Apache Cassandra, we are talking about big data -- think terabytes of data -- so we are making it fast for read purposes. Data is spread across all the nodes. By using the WHERE statement, we know which node to go to, from which node to get that data and serve it back. For example, imagine we have 10 years of data on 10 nodes or servers. So 1 year's data is on a separate node. By using the WHERE year = 1 statement we know which node to visit fast to pull the data from.

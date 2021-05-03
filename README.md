# Data Modeling with Apache Cassandra
>
Summary of this project

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




# Data Modeling with Apache Cassandra
>
In this project, a NoSQL database is created by using Apache Cassandra<sup>[1](#myfootnote1)</sup> to analyze a dataset, which contain the songs and the user activities in CSV format. An ETL pipeline with tables created to fit the queries are presented in the jupyter notebook.

<a name="myfootnote1"><sup>1</sup></a> Data Modeling in Apache Cassandra:

* Denormalization is not just okay -- it's a must.

* Denormalization must be done for fast reads.

* Apache Cassandra has been optimized for fast writes.

* ALWAYS think Queries first.

* One table per query is a great strategy.

* Apache Cassandra does not allow for JOINs between tables.

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
Below are partial code and query results of this project. For detail steps please refer to file `project_etl.ipynb`.

### Modeling your NoSQL database or Apache Cassandra database
1. Design tables to answer the queries outlined in the project template.

2. Write Apache Cassandra `CREATE KEYSPACE` and `SET KEYSPACE` statements.

    2-1. Creating a Cluster
    ```python
    from cassandra.cluster import Cluster
    cluster = Cluster()

    session = cluster.connect()
    ```

    2-2. Create Keyspace
    ```python
    session.execute(""" CREATE KEYSPACE IF NOT EXISTS sparkify 
                    WITH REPLICATION = {'class': 'SimpleStrategy' , 'replication_factor': 1}
                """)
    ```

    2-3. Set Keyspace
    ```python
    try:
        session.set_keyspace('sparkify')
    except Exception as e:
        print(e)
    ```

3. Develop the `CREATE` statement for each of the tables to address each question.

    3-1. Create Table
    ```python
    session.execute("""CREATE TABLE IF NOT EXISTS song_session
    (sessionId int,
     itemInSession int,
     artist text,
     song_title text,
     length float,
     PRIMARY KEY(sessionId, itemInSession))
    """) 
    ```

4. Load the data with `INSERT` statement for each of the tables.

    4-1. Insert Table
    ```python
    file = 'event_datafile_new.csv'

    with open(file, encoding = 'utf8') as f:
        csvreader = csv.reader(f)
        next(csvreader)
        for line in csvreader:
            query = "INSERT INTO song_session(sessionId, itemInSession, artist, song_title, length)"
            query = query + " VALUES (%s, %s, %s, %s, %s)"
            session.execute(query, (int(line[8]), int(line[3]), line[0], line[9], float(line[5])))
    ```

5. Include `IF NOT EXISTS` clauses in your `CREATE` statements to create tables only if the tables do not already exist. Also, include `DROP TABLE` statement for each table, so the database can drop and create tables whenever you want to reset your database and test your ETL pipeline.

6. Test by running the proper select statements with the correct `WHERE` clause.

    6-1. Make a Query
    ```python
    query = "SELECT artist, song_title, length from song_session WHERE sessionId=338 AND itemInSession=4"
    rows = session.execute(query)
    for row in rows:
        print(row.artist, row.song_title, row.length)
    ```
    Output:
    ```
    Faithless Music Matters (Mark Knight Dub) 495.30731201171875
    ```

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

**1. Primary Key Simple (Only one Partition Key)**: The Partition Key is `year`.
```SQL
CREATE TABLE music_library (
    year int,
    artist_name text,
    album_name text,
    PRIMARY KEY(year)
);
```

This can also be declared inline:
```SQL
CREATE TABLE music_library (
    year int PRIMARY KEY,
    artist_name text,
    album_name text
);
```

**2. Composite Partition Key (Composite Partition Key, hashed together)**: The Partition Key is `year` and `artist_name`.
```SQL
CREATE TABLE music_library (
    year int,
    artist_name text,
    album_name text,
    PRIMARY KEY((year, artist_name))
);
```

**3. Composite Primary Key (One Partition Key + One Clustering Column)**: The Partition Key is `year`, and the Clustering Key is `artist_name`.
```SQL
CREATE TABLE music_library (
    year int,
    artist_name text,
    album_name text,
    PRIMARY KEY(year, artist_name)
);
```

**4. Composite Primary Key (One Partition Key + two or more Clustering Columns)**: The Partition Key is `year`, and the Clustering Key is `artist_name` and `album_name`.
```SQL
CREATE TABLE music_library (
    year int,
    artist_name text,
    album_name text,
    PRIMARY KEY(year, artist_name, album_name)
);
```

**5. Composite Primary Key (Composite Partition Key + one Clustering Column)**: The Composite Partition Key is `year` and `artist_name`, and the Clustering Key is `title`.
```SQL
CREATE TABLE music_library (
    year int,
    artist_name text,
    album_name text,
    PRIMARY KEY((year, artist_name), title)
);
```

**6. Composite Primary Key (Composite Partition Key + two or more Clustering Columns)**: The Composite Partition Key is `year` and `artist_name`, and the Clustering Key is `title` and `duration`.
```SQL
CREATE TABLE music_library (
    year int,
    artist_name text,
    album_name text,
    PRIMARY KEY((year, artist_name), title, duration)
);
```

The partition key is the minimum-specifier needed to perform a query using a where clause.

On the table defined above, you can perform query only by passing at least both `year` and `artist_name`, these are the 2 columns that define the partition key.

The "general" rule to make query is you have to pass at least all partition key columns, then you can add optionally each clustering key **in the order they're set**.

so the valid queries are (excluding secondary indexes)

* `year` and `artist_name`
* `year` and `artist_name` and `title`
* `year` and `artist_name` and `title` and `duration`

Invalid:

* `year` and `artist_name` and `duration`
* anything that does not contain both `year` and `artist_name`

Examples-1: Valid query
```SQL
query = "SELECT * FROM music_library WHERE YEAR = 1970 AND artist_name = 'The Beatles'"
```

Examples-2: Valid query
```SQL
query = "SELECT * FROM music_library WHERE YEAR = 1970 AND artist_name = 'The Beatles' AND title = 'Rubber Soul'"
```

Examples-3: Invalid query
```SQL
query = "SELECT * FROM music_library WHERE YEAR = 1970 AND artist_name = 'The Beatles' AND duration = 30"
```

### Clustering Columns

* The clustering column will sort the data in sorted ascending order, e.g., alphabetical order. 

* More than one clustering column can be added (or none!)

* From there the clustering columns will sort in order of how they were added to the primary key

You can use as many clustering columns as you would like. You cannot use the clustering columns out of order in the SELECT statement. You may choose to omit using a clustering column in your SELECT statement. That's OK. Just remember to use them in order when you are using the SELECT statement.

### WHERE clause

* Data Modeling in Apache Cassandra is query focused, and that focus needs to be on the **WHERE** clause.

* Failure to include a WHERE clause will **result in an error**.

The WHERE statement is allowing us to do the fast reads. With Apache Cassandra, we are talking about big data -- think terabytes of data -- so we are making it fast for read purposes. Data is spread across all the nodes. By using the WHERE statement, we know which node to go to, from which node to get that data and serve it back. For example, imagine we have 10 years of data on 10 nodes or servers. So 1 year's data is on a separate node. By using the WHERE year = 1 statement we know which node to visit fast to pull the data from.



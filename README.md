# 95-702 Distributed Systems for Information Systems Management
# Project 5 - Distributed Computation
## Fall 2026

Assigned: Monday, November 16, 2026

Due: Monday, November 30, 2026, 11:59 PM

---
# Introduction

Big data infrastructures support distributed computation through services such as MapReduce and Spark.

Both approaches exemplify the principle of spatial locality by moving computation close to data. Large datasets can then be processed in parallel while minimizing data movement.

In Part 1 of this project, you will develop and execute MapReduce applications using Hadoop running inside the Docker environment introduced in Lab 9. If you did not complete lab 9, you may do so now. Or, you might complete it again so
that you are familiar with the environment.

In Part 2 of this project, you will use Apache Spark running locally in IntelliJ.

This project builds directly on the work completed in Lab 9. You will use the same Docker-based Hadoop environment, the same HDFS workflow, and the same Hadoop 3.x programming model that were introduced in the lab.

---

# Learning Objectives

After completing this project, you will be able to:

* Develop MapReduce applications in Java using the Hadoop 3.x API.
* Execute MapReduce jobs using Hadoop and HDFS.
* Transfer data between Linux filesystems and HDFS.
* Analyze large datasets using distributed computation.
* Develop Spark applications using Java.
* Perform distributed data analytics with Spark.
* Work productively in a Linux environment.

---

# Academic Integrity

Unless explicitly prohibited in an individual task, you may use:

* Large language models
* Web resources
* Books and documentation

You are responsible for understanding all submitted work.

Any use of external resources must be documented within your source code.

Tasks that prohibit external assistance must be completed without:

* Large language models
* Code-generation tools
* External web resources

Failure to comply with these restrictions may be treated as an academic integrity violation.

---

# Software Environment

Part 1 uses the Hadoop Docker environment introduced in Lab 9.

The Docker container provides:

* Hadoop 3.5.0
* HDFS
* YARN
* MapReduce
* Job History Server

Although all Hadoop services execute inside a single Docker container, the same Hadoop services found in larger production clusters are present.

Before beginning this project, review Lab 9 and verify that you can:

* Start the Hadoop container.
* Open a shell inside the container.
* Copy files into HDFS.
* Execute a MapReduce job.
---

# Filesystems Used in This Project

Three different filesystems are involved.

Understanding the differences between these filesystems is essential for working with Hadoop.

MapReduce jobs execute within Hadoop and use HDFS for input and output. Source code is stored in your project workspace. Instructor-provided datasets are available in a separate read-only location.

---

## Host Filesystem

The host filesystem is the storage on your computer outside of Docker.

The project repository is stored here.

Example:

```text
workspace/
```

Inside the Docker container, this directory appears as:

```text
/home/student/Project5
```

Files stored in the host filesystem remain available even if the Docker container is stopped, removed, or recreated.

You will edit your Java source code within this directory using your preferred editor or IDE.

---

## Container Linux Filesystem

The container filesystem exists inside the running Docker container.

Examples:

```text
/home/student
/home/public
```

The directory:

```text
/home/public
```

contains instructor-provided datasets and example programs.

Example files include:

```text
words.txt
combinedYears.txt
P1V.txt
CrimeLatLonXYTabs.txt
WordCount.java
```

You may read these files but should not modify them.

Files created elsewhere in the container filesystem may be lost if the container is removed and recreated.

---

## HDFS

HDFS (Hadoop Distributed File System) is the filesystem used by Hadoop.

Examples:

```text
/user/student/input
/user/student/output
```

MapReduce jobs read their input from HDFS and write their output to HDFS.

A typical workflow is:

1. Copy an input file from `/home/public` or `/home/student/Project5` into HDFS.
2. Execute a MapReduce job.
3. Examine the output written to HDFS.
4. Copy or merge the output back into your project directory.

For example:

```bash
hdfs dfs -copyFromLocal /home/public/words.txt /user/student/input/words.txt
```

After a job has completed:

```bash
hdfs dfs -getmerge /user/student/output TaskOutput

```

---

## Summary

Throughout this project you will routinely move files among these three locations:

| Location | Purpose |
|-----------|----------|
| `/home/student/Project5` | Source code, project files, and final outputs |
| `/home/public` | Instructor-provided datasets and code|
| `/user/student/*` | HDFS input and output for MapReduce jobs |

Understanding which filesystem a file resides in is one of the most important skills you will develop while working with Hadoop.

---

# Starting the Environment


After completing Lab 9, start the Hadoop environment:

```bash
docker compose up -d --wait
```

Verify that the container is healthy:

```bash
docker compose ps
```

Enter the Hadoop container:

```bash
docker compose exec hadoop bash
```

The shell should open in:

```text
/home/student/Project5
```

This directory corresponds to the `workspace` directory on your computer.

From inside the container, verify that Hadoop services are running:

```bash
lab-status
```


---

# Useful Web Interfaces

The Hadoop services provide several web interfaces that can be viewed from a browser running on your computer.

## HDFS NameNode

```text
http://localhost:9870
```

The NameNode interface allows you to:

* Browse HDFS directories
* View file locations
* View HDFS capacity information
* Monitor HDFS health

---

## YARN ResourceManager

```text
http://localhost:8088
```

The ResourceManager interface allows you to:

* View running jobs
* View completed jobs
* Monitor resource consumption
* Examine MapReduce application status

---

## MapReduce Job History

```text
http://localhost:19888
```

The Job History interface allows you to:

* Review completed MapReduce jobs
* View mapper and reducer execution details
* Examine task logs
* Troubleshoot failed jobs

Students are encouraged to use these interfaces while developing and debugging their MapReduce applications.

---

# Useful Commands

The following commands will be helpful throughout the project.

## Display Files on HDFS example

```bash
hdfs dfs -ls /user/student/input
```

---

## Copy a Local File into HDFS example

```bash
hdfs dfs -copyFromLocal localFile.txt /user/student/input/localFile.txt
```

Use the `-f` option to overwrite an existing file:

```bash
hdfs dfs -copyFromLocal -f localFile.txt /user/student/input/localFile.txt
```

---

## Display a File Stored on HDFS example

```bash
hdfs dfs -cat /user/student/input/localFile.txt
```

---

## Delete an HDFS Directory example

```bash
hdfs dfs -rm -r -f /user/student/output
```

MapReduce jobs will fail if the output directory already exists.

---

## Merge Reducer Output into a Single Local File example

```bash
hdfs dfs -getmerge /user/student/output mergedOutput.txt
```

---

## List Running MapReduce Jobs example

```bash
mapred job -list
```

---

## List YARN Applications example

```bash
yarn application -list -appStates ALL
```

---

## Kill a Running Job example

```bash
mapred job -kill JOB_ID
```

Replace `JOB_ID` with the identifier displayed by:

```bash
mapred job -list
```

---

# Programming Model

All MapReduce applications in this project will use the Hadoop 3.x MapReduce API.

Your programs should use classes from packages such as:

```java
org.apache.hadoop.mapreduce
```

and

```java
org.apache.hadoop.mapreduce.lib.*
```

The starter program provided in Task 0 demonstrates the programming style expected throughout Part 1.

The older Hadoop API based on:

```java
org.apache.hadoop.mapred
```

should not be used in this project.

All MapReduce applications developed for this project should follow the same general pattern:

1. Define a Mapper class.
2. Define a Reducer class.
3. Configure a Job object.
4. Specify HDFS input and output paths.
5. Execute the job through Hadoop.
6. Merge the results back to your local system

Task 0 serves as the reference implementation for the remaining MapReduce tasks.

---
# Directory Structure

The project repository already contains the directory structure used for Part 1.

```text
workspace/
└── Part_1/
    ├── Task0/
    ├── Task1/
    ├── Task2/
    ├── Task3/
    ├── Task4/
    ├── Task5/
    ├── Task6/
    └── Task7/
```

Complete each task in its assigned directory.

Unless instructed otherwise, leave all task files in their original locations.

Note that each task already has starter code with comments. Task0 initially contains only a README.md file. You will copy WordCount.java into the Task0 directory.

---

# Submission

Submit a single ZIP file named:

```text
Project5.zip
```

The ZIP file should contain all source code developed for Part 1 and Part 2.

Submit Java source code only.

Do not submit:

* Compiled class files (`.class`)
* JAR files
* IDE configuration files
* Hadoop output directories
* Temporary files
* Build artifacts

For Task 7, additionally submit:

```text
GoogleEarthScreenshot.png
```

---

# Future Automated Grading

To support automated grading, follow the required directory structure exactly.

Do not rename:

* Task directories
* Required Java source files
* Required package names

The grading system will compile and test submitted Java source code.

Some tasks may be evaluated using hidden datasets in addition to the datasets provided in this project.

Programs must compile and execute successfully without modification.

---

# Grading

Your submission will be evaluated based on:

* Correctness
* Documentation
* Code quality
* Successful compilation
* Successful execution

Programs that do not compile will receive little or no credit.

---

# PART 1 - MAPREDUCE PROGRAMMING

## General Notes

Unless otherwise specified, all MapReduce jobs will follow the same workflow.

### Step 1

Make an HDFS directory to hold the input and
copy the input to HDFS.

### Step 2

Remove any previous output directory from HDFS.
Hadoop will fail if the output directory already exists.

### Step 3

Compile the MapReduce program.

### Step 4

Place the compiled code in a Java archive (jar file).
The jar file will typically contain a directory path
that matches the package name of the Java class.


### Step 5

Deploy and execute the jar file on HDFS.

### Step 6

Merge reducer output into a local file.


### Step 7

Inspect the results.

---

## Reducer Output Files

A MapReduce job may generate one or more reducer output files:

```text
part-r-00000
part-r-00001
part-r-00002
...
```

When a task requires a single output file, use:

```bash
hdfs dfs -getmerge
```

to merge reducer output into a single local file before examining the results.

We will place our final output files in the Task subdirectory. For example,
place the Task0 output file in /home/student/Project5/Part_1/Task0/Task0Output.

---

# Task 0 - Word Count

The file:

```text
/home/public/WordCount.java
```

contains a complete Hadoop 3.x MapReduce application.

Copy this file into the Task0 directory:

```bash
cp /home/public/WordCount.java /home/student/Project5/Part_1/Task0
```

The WordCount.java program serves as the reference implementation for the remainder of Part 1.

Study the program carefully and make sure that you understand:

* The Mapper class
* The Reducer class
* The Job configuration
* ToolRunner
* HDFS input paths
* HDFS output paths
* Context.write(...)
* Key/value pairs emitted by the mapper
* Key grouping performed before reduction

The package declaration is:

```java
package edu.cmu.project5.task0;
```

After compilation, you will see the edu directory, corresponding
to the start of the package path.

Future tasks will follow a similar package naming convention.

---

## Running the Program

Create an HDFS input directory:

```bash
hdfs dfs -mkdir -p /user/student/input
```
Old files may be removed from HDFS with the following command:

```bash
hdfs dfs -rm  /user/student/input/*
```

Copy an input file to HDFS:

```bash
hdfs dfs -copyFromLocal /home/public/words.txt /user/student/input/words.txt
```

Remove any existing output directory:

```bash
hdfs dfs -rm -r -f /user/student/output
```

Copy WordCount.java into the Task0 directory.

```bash
cp /home/public/WordCount.java WordCount.java

```

Compile the application with the following command.
Note, the -d . option causes the compiler to create the
package directory hierarchy automatically. You should see
edu as a new directory after the compile.

```bash
javac -classpath "$(hadoop classpath)" -d . WordCount.java

```

From within Task0, create a jar (Java archive) file:

```
jar -cvf wordcount.jar edu

```
You should now see the following in Task0:
```
ls
edu  README.md	wordcount.jar  WordCount.java
```

We need input placed on HDFS.

Make the input directory on our local system:

```bash
mkdir -p /home/student/input
```

For initial testing we will use a very small input file so that expected counts can easily be verified by inspection. Put some text in the input directory:

```bash
printf "dog cat dog dog\n" > /home/student/input/test.txt

```

Make a directory to hold the input on HDFS:

```
hdfs dfs -mkdir -p /user/student/task0/input
```

Copy our local input to HDFS:

```
hdfs dfs -copyFromLocal -f /home/student/input/test.txt /user/student/task0/input
```

Remove any old output from HDFS:

```
hdfs dfs -rm -r -f /user/student/output
```

Deploy and execute the jar:

```bash
hadoop jar wordcount.jar edu.cmu.project5.task0.WordCount /user/student/input /user/student/output
```


After the job completes, examine the output stored on HDFS:

```bash

hdfs dfs -cat /user/student/output/part-r-00000

```

Merge the reducer output into a local file named /home/student/Project5/Part_1/Task0/Task0Output


```
hdfs dfs -getmerge /user/student/output /home/student/Project5/Part_1/Task0/Task0Output

```
View the results:

```
cat /home/student/Project5/Part_1/Task0/Task0Output
```

---

## Deliverables

A grader may examine:

* Source code documentation in WordCount.java
* Successful program execution
* Task0Output

You may use a large language model or web resources for this task.

Any external assistance must be documented within your source code comments.

Be sure to document WordCount.java with your own words describing what is going on.

---

## Task 1 Preview

In Task 1, you will modify the WordCount design to build a MapReduce application that computes letter frequencies rather than word frequencies.

The structure, programming model, package organization, and Hadoop workflow used in Task 0 should serve as the foundation for your Task 1 solution.

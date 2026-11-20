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

Part 1 will be graded automatically using Gradescope.

Part 2 should be submitted as a single PDF document to Gradescope and will be evaluated through manual
inspection of the PDF file.

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

# Filesystems Used in Part 1

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

The instructor provided files include:

```text
words.txt
combinedYears.txt
P1V.txt
CrimeLatLonXYTabs.txt
WordCount.java
MaxTemperature.java
MaxTemperatureMapper.java
MaxTemperatureReducer.java

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

0. Make a directory for input on HDFS.
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
hdfs dfs -getmerge /user/student/output /home/student/Project5/Part_1/Task0/Task0Output

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

Make an input directory on HDFS:

```console
hdfs dfs -mkdir -p /user/student/input
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

# Example Commands

The following commands may not yet run but will be helpful throughout the project.

## Display Files on HDFS

```bash
hdfs dfs -ls /user/student/input
```

---

## Copy a Local File into HDFS to be used as input

```bash
hdfs dfs -copyFromLocal localFile.txt /user/student/input/localFile.txt
```

Use the `-f` option to overwrite an existing file:

```bash
hdfs dfs -copyFromLocal -f localFile.txt /user/student/input/localFile.txt
```

---

## Display an input file stored on HDFS

```bash
hdfs dfs -cat /user/student/input/localFile.txt
```

---

## Delete an HDFS directory named output

```bash
hdfs dfs -rm -r -f /user/student/output
```

MapReduce jobs will fail if the output directory already exists.

---

## Merge reducer output into a single local file

```bash
hdfs dfs -getmerge /user/student/output /home/student/Project5/Part_1/Task0/Task0Output
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
6. Merge the results back to your local system under a Task.

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


# Automated Grading

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

Merge reducer output into a local file under a Task.


### Step 7

Inspect the results.

---

## Reducer Output Files

A MapReduce job may generate one or more reducer output files and these
will reside in the output directory (under HDFS).

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

We will place our final output files in the Task subdirectories. For example,
place the Task0 output file in /home/student/Project5/Part_1/Task0/Task0Output.

---

## Autograder Concern

In this project, all MapReduce programs must accept exactly two command-line arguments:

```
<input-path> <output-path>

```

corresponding to the HDFS input and output paths. Programs must not hard-code HDFS paths. The grading system may use hidden datasets and HDFS directories that differ from those used during development.


# Task 0 - Word Count

The file WordCount.java is found near the end of this assignment and is also available in
the container's file system. The file at this location

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

After compilation, you will see the edu directory corresponding
to the start of the package path. If you cd into edu then you
will see cmu and so on.

Future tasks will follow a similar package naming convention.

---

## Complete these solution steps for Task0

We will test WordCount.java with a small file and then a larger file.

If you have not already done so, create an HDFS input directory:

```bash
hdfs dfs -mkdir -p /user/student/input

```
Old files should be removed from HDFS with the following command. You will
get an error if there are no files to delete.

```bash
hdfs dfs -rm  /user/student/input/*
```

We are using a standard directory for all of our input to MapReduce jobs
on HDFS. We are using /user/student/input/ for input files.

We are using a standard directory for all of our output files generated from MapReduce jobs on HDFS. We are using /user/student/output/ for all of our output files on HDFS.

Before running a new job, remove an existing output directory:

```bash
hdfs dfs -rm -r -f /user/student/output
```

Copy WordCount.java into our local Task0 directory.

```bash
cp /home/public/WordCount.java WordCount.java

```

Compile the application with the following command.
Note, the -d . option causes the compiler to create the
package directory hierarchy. You should see
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
```

```
edu  README.md	wordcount.jar  WordCount.java
```

We need input placed on HDFS.

We will use the directory /home/student/input for input before moving it
to HDFS.

If you have not already done so, make the input directory on our local system:

```bash
mkdir -p /home/student/input
```

For initial testing we will use a very small input file so that expected counts can easily be verified by inspection. Put some text in the input directory:

```bash
printf "dog cat dog dog\n" > /home/student/input/test.txt

```


Copy our local input to HDFS:

```
hdfs dfs -copyFromLocal -f /home/student/input/test.txt /user/student/input/test.txt
```

Remove any old output from HDFS:

```
hdfs dfs -rm -r -f /user/student/output
```

From within the Task0 directory, deploy and execute the jar:

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

Now, do the same with a larger file: words.txt. The grader may test against
the short file created above or against the longer words.txt.

## Deliverables

A grader may examine:

* Source code documentation in WordCount.java
* Successful program compilation and execution against either a short
file or a larger one.

The reducer output should be merged into a local file named:

/home/student/Project5/Part_1/Task0/Task0Output

This file is provided for your convenience during development. The grading system will compile and execute your MapReduce program using test and hidden datasets rather than grading this file directly.

Programs must execute correctly without modification and must accept input and output paths through command-line arguments.

You may use a large language model or web resources for this task.

Any external assistance must be documented within your source code comments.

Be sure to document WordCount.java with your own words describing what is going on.

---

## Task 1

In Task 1, you will modify the WordCount design to build a MapReduce application that computes letter frequencies rather than word frequencies.

The structure, programming model, package organization, and Hadoop workflow used in Task 0 should serve as the foundation for your Task 1 solution.

Count letters in a case-insensitive manner. Ignore digits, punctuation, and whitespace.

Working from WordCount.java, build and deploy a MapReduce application
named LetterCounter.java and place it into a jar file called lettercount.jar.
This program will compute the total number of each letter in the words.txt file. The output from the reducer will need to be merged to a single output file.

The reducer output should be merged into a local file named:

/home/student/Project5/Part_1/Task1/Task1Output
 
This file is intended to help you inspect and debug your results during development.

Note that
WordCount.java (Task 0) uses a call to nextToken() on the iterator. Without this call,
the program will enter an infinite loop and will need to be killed.

You may use a large language model or the web to help with this problem.
Be sure to cite any external sources in your code.

## Deliverables

A grader may examine:

* Source code documentation in LetterCounter.java
* Successful program compilation and execution

The reducer output should be merged into a local file named:

/home/student/Project5/Part_1/Task1/Task1Output

This file is provided for your convenience during development. The grading system will compile and execute your MapReduce program using test and hidden datasets rather than grading this file directly.

Programs must execute correctly without modification and must accept input and output paths through command-line arguments.

You may use a large language model or web resources for this task.

Any external assistance must be documented within your source code comments.

Be sure to document LetterCounter.java with your own words describing what is going on.


### Task 2  

Build a MapReduce application named FindPattern.java. For each word containing the string "fun" (case insensitive), emit the word as the key and the value 1. The reducer will sum the values for each matching word. The final output should therefore contain each distinct matching word together with the number of times it appears in the input.

The search for "fun" is case insensitive. Convert matching words to lowercase before emitting them so that words differing only in capitalization are counted together.

Your program must accept exactly two command-line arguments:

<input-path> <output-path>

The input path specifies the HDFS file or directory to be processed. The output path specifies the HDFS directory where reducer output will be written. Do not hard-code HDFS paths in your solution. Hidden tests may use inputs and outputs located in different HDFS directories.

You may use a large language model or the web to help with this problem.
Be sure to cite any external sources in your code.

## Deliverables

A grader may examine:

* Source code documentation in FindPattern.java
* Successful program compilation and execution

The reducer output should be merged into a local file named:

/home/student/Project5/Part_1/Task2/Task2Output

This file is provided for your convenience during development. The grading system will compile and execute your MapReduce program using test and hidden datasets rather than grading this file directly.

Programs must execute correctly without modification and must accept input and output paths through command-line arguments.

You may use a large language model or web resources for this task.

Any external assistance must be documented within your source code comments.

Be sure to document FindPattern.java with your own words describing what is going on.


### Task 3

In this task, we will use a fairly large dataset from Tom White's book.
The data file is located at "/home/public/combinedYears.txt". It contains thousands of temperature readings
(in Celsius times 10) in the USA by year from 1901 to 1902. The year is specified
in positions 15-18. The temperature is specified beginning at column 87 and begins
with a plus or minus character. Four digits are used for the temperature. Be sure
to study the code below to see how it references these locations.

Below are three files: MaxTemperature.java, MaxTemperatureMapper.java and MaxTemperatureReducer.java.
These files may be found at /home/public. Copy these files to your
/home/student/Project5/Part_1/Task3 directory.

Run this application against the data set under /home/public/combinedYears.txt. Your jar file will
be named temperature.jar.  

Your program must accept exactly two command-line arguments:

<input-path> <output-path>

The input path specifies the HDFS file or directory to be processed. The output path specifies the HDFS directory where reducer output will be written. Do not hard-code HDFS paths in your solution. Hidden tests may use inputs and outputs located in different HDFS directories.

You may use a large language model or the web to help with this problem.
Be sure to cite any external sources in your code.

## Deliverables

A grader may examine:

* Source code documentation in MaxTemperature.java, MaxTemperatureMapper.java and MaxTemperatureReducer.java.
* Successful program compilation and execution

The reducer output should be merged into a local file named:

/home/student/Project5/Part_1/Task3/Task3Output

This file is provided for your convenience during development. The grading system will compile and execute your MapReduce program using test and hidden datasets rather than grading this file directly.

Programs must execute correctly without modification and must accept input and output paths through command-line arguments.

The package declarations provided in the starter files should not be changed.

You may use a large language model or web resources for this task.

Any external assistance must be documented within your source code comments.

Be sure to document your Java source code with your own words describing what is going on.


### Task 4

Modify the code from Task 3 and build a minimum temperature application. Note that the
temperatures in this file are degrees Celsius * 10.  Your jar file will be named
mintemperature.jar.

The source code files will be named MinTemperature.java, MinTemperatureMapper.java and MinTemperatureReducer.java.

Your program must accept exactly two command-line arguments:

<input-path> <output-path>

The input path specifies the HDFS file or directory to be processed. The output path specifies the HDFS directory where reducer output will be written. Do not hard-code HDFS paths in your solution. Hidden tests may use inputs and outputs located in different HDFS directories.

You may use a large language model or the web to help with this problem.
Be sure to cite any external sources in your code.

## Deliverables

A grader may examine:

* Source code documentation in MinTemperature.java, MinTemperatureMapper.java and MinTemperatureReducer.java.
* Successful program compilation and execution

The reducer output should be merged into a local file named:

/home/student/Project5/Part_1/Task4/Task4Output

This file is provided for your convenience during development. The grading system will compile and execute your MapReduce program using test and hidden datasets rather than grading this file directly.

Programs must execute correctly without modification and must accept input and output paths through command-line arguments.

The package declarations provided in the starter files should not be changed.

You may use a large language model or web resources for this task.

Any external assistance must be documented within your source code comments.

Be sure to document your Java source code with your own words describing what is going on.

### Task 5

Within the /home/public directory, there is a file called P1V.txt.

P1V.txt is a tab-delimited text file containing individual criminal offense incidents from January 1990 through December 1999 for serious violent crimes (FBI Part 1) in Pittsburgh.

The first two columns (X,Y) represent State Plane (projected, rectilinear) coordinates (measured
in feet) specifying the location of the crime.
The third column is the time.
The fourth column is a street address.
The fifth column is the type of offense (aggravated assault, Robbery, Rape, Etc.)
The sixth column is the date.
The seventh column is the 2000 census tract.

Write a MapReduce application that finds the total number of aggravated assaults and
robberies. If there were 100 aggravated assaults and 50 robberies then the reducer
will generate the following single line with a key and value pair.

```text
crimes   150
```

Your single Java program will be named AggravatedAssaultsPlusRobberies.java.

Your program must accept exactly two command-line arguments:

<input-path> <output-path>

The input path specifies the HDFS file or directory to be processed. The output path specifies the HDFS directory where reducer output will be written. Do not hard-code HDFS paths in your solution. Hidden tests may use inputs and outputs located in different HDFS directories.

Your jar file will be named aggravatedassaultsplusrobberies.jar.

## Deliverables

A grader may examine:

* Source code documentation in AggravatedAssaultsPlusRobberies.java.
* Successful program compilation and execution

The reducer output should be merged into a local file named:

/home/student/Project5/Part_1/Task5/Task5Output

This file is provided for your convenience during development. The grading system will compile and execute your MapReduce program using test and hidden datasets rather than grading this file directly.

Programs must execute correctly without modification and must accept input and output paths through command-line arguments.

The package declarations provided in the starter files should not be changed.

You should *not* use a large language model or web resources for this task. You may
use work from previous Tasks.

Be sure to document your Java source code with your own words describing what is going on.


### Task 6

Modify your solution to Task 5 so that it finds the total number of aggravated assault crimes that occurred within
350 meters of 3803 Forbes Avenue in Oakland. This location has the (X,Y) coordinates of
(1354326.897,411447.7828). Use these coordinates and the Pythagorean theorem to decide if a particular
aggravated assault occurred within 350 meters of 3803 Forbes Avenue.

The distance requirement is specified in meters, but the state plane coordinates are measured in feet.

Use:

1 meter = 3.28084 feet

Therefore:

350 meters × 3.28084 = 1148.294 feet

A crime is considered within range if its distance from
(1354326.897, 411447.7828) is less than or equal to
1148.294 feet.

If there were 42 aggravated assaults within 350 meters of 3803 Forbes Avenue, then the reducer would emit
exactly one key/value pair:

```text
crimes    42
```
Count only aggravated assault crimes. All other crime categories should be ignored.

Your jar file will be named oaklandcrimestats.jar.

Your single Java program will be named OaklandCrimeStats.java.

Your program must accept exactly two command-line arguments:

<input-path> <output-path>

The input path specifies the HDFS file or directory to be processed. The output path specifies the HDFS directory where reducer output will be written. Do not hard-code HDFS paths in your solution. Hidden tests may use inputs and outputs located in different HDFS directories.


## Deliverables

A grader may examine:

* Source code documentation in OaklandCrimeStats.java.
* Successful program compilation and execution

The reducer output should be merged into a local file named:

/home/student/Project5/Part_1/Task6/Task6Output

This file is provided for your convenience during development. The grading system will compile and execute your MapReduce program using test and hidden datasets rather than grading this file directly.

Programs must execute correctly without modification and must accept input and output paths through command-line arguments.

The package declarations provided in the starter files should not be changed.

You may *not* use a large language model or web resources for this task. You may
use work from previous Tasks.

Be sure to document your Java source code with your own words describing what is going on.

### Task 7

In Task 7, the input file is named CrimeLatLonXYTabs.txt. It can be copied from the /home/public directory.
Its format is similar to P1V.txt but also includes the latitude and longitude of each crime.

CrimeLatLonXYTabs.txt is a tab-delimited text file containing individual criminal offense incidents
from January 1990 through December 1999 for serious violent crimes (FBI Part 1) in Pittsburgh.

The first two columns (X,Y) represent State Plane (projected, rectilinear) coordinates (measured
in feet) specifying the location of the crime.
The third column is the time.
The fourth column is a street address.
The fifth column is the type of offense (aggravated assault, rape, Etc.)
The sixth column is the date.
The seventh column is the 2000 census tract.
The eight column specifies the latitude.
The ninth column specifies the longitude.

These last two columns are used for viewing in GIS tools (such as Google Earth Pro).

Modify your solution to Task 5 so that it finds all of the aggravated assault
crimes that occurred within 350 meters of 3803 Forbes Avenue in Oakland. This location has
the (X,Y) coordinates of (1354326.897,411447.7828). Use these coordinates and the Pythagorean
theorem to decide if a particular aggravated assault occurred within 350 meters of 3803 Forbes
Avenue. State plane coordinates are measured in feet. Your code is testing on meters. Use the same
distance calculation and conversion factors described in Task 6.

Your single Java program will be named OaklandCrimeStatsKML.java.

Your jar file will be named oaklandcrimestatskml.jar. The output file will be a well formed KML file
suitable for viewing in Google Earth. The KML file will be used to display each of these crimes on
a map.
The reducer output should be merged into a local file named:

/home/student/Project5/Part_1/Task7/Task7Output

This file is provided for your convenience during development. The grading system will compile and execute your MapReduce program using test and hidden datasets rather than grading this file directly.

Your program must accept exactly two command-line arguments:

<input-path> <output-path>

The input path specifies the HDFS file or directory to be processed. The output path specifies the HDFS directory where reducer output will be written. Do not hard-code HDFS paths in your solution. Hidden tests may use inputs and outputs located in different HDFS directories.

The output KML file must contain one <Placemark> element for each aggravated assault crime that satisfies the distance requirement. The ordering of the placemarks in the output file is not important.

Each <Placemark> element must contain a <Point> element and a <coordinates> element in the format:

```text
longitude,latitude,0
```

where the longitude and latitude values are taken directly from the input file.

Here is a simple KML file. You can save it and then load it into Google Earth. Your solution will be a longer file.
This KML file only has one <placemark> element. Your solution will have many.

```text
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2">
<Placemark>
  <Point>
    <coordinates>
      -79.9456,40.4406,0
    </coordinates>
  </Point>
</Placemark>  
</kml>
```

You can test your KML formatting by viewing your KML file in Google Earth. You will not include
the screenshot in your submission. The submission is auto graded by parsing the resulting KML,
validating its structure, and verifying the generated placemarks and coordinates.

You should consider limiting the number of reducers to 1. You can do this by
generating the same key from each mapper.


## Deliverables

A grader may examine:

* Source code documentation in OaklandCrimeStatsKML.java
* Successful program compilation and execution
* A well-formed KML file

Programs must execute correctly without modification and must accept input and output paths through command-line arguments.

The package declarations provided in the starter files should not be changed.

You may not use a large language model or web resources for this task.

Be sure to document your Java source code with your own words describing what is going on.





//************** WordCount.java *********************
```
package edu.cmu.project5.task0;

// Java classes used for input/output exceptions and tokenization.
import java.io.IOException;
import java.util.StringTokenizer;

// Hadoop configuration classes.
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.conf.Configured;

// Hadoop filesystem path class.
import org.apache.hadoop.fs.Path;

// Hadoop writable data types used during MapReduce processing.
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;

// Hadoop MapReduce classes.
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;

// Hadoop input and output format classes.
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

// Hadoop utility classes used to launch the job.
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

/**
 * WordCount.java
 *
 * Author: Your Name Here
 *
 * Purpose:
 * Demonstrates a complete Hadoop MapReduce application.
 *
 * The mapper reads text and emits:
 *
 *     (word, 1)
 *
 * for each word in the input.
 *
 * The reducer receives:
 *
 *     (word, [1,1,1,...])
 *
 * and adds the values to produce:
 *
 *     (word, count)
 *
 * Input:
 *     Text file stored on HDFS.
 *
 * Output:
 *     Word frequencies written to HDFS.
 */
public class WordCount extends Configured implements Tool {

    /**
     * Mapper
     *
     * Receives one line of text at a time.
     * Emits (word,1) for every word found in the line.
     */
    public static class WordMapper
            extends Mapper<LongWritable, Text, Text, IntWritable> {

        // Reusable constant that represents the value 1.
        private static final IntWritable ONE = new IntWritable(1);

        // Reusable Text object used as the output key.
        private final Text word = new Text();

        /**
         * Called once for each line of input.
         *
         * key     = byte offset of line within file
         * value   = contents of the line
         * context = mechanism for writing mapper output
         */
        @Override
        protected void map(LongWritable key,
                           Text value,
                           Context context)
                throws IOException, InterruptedException {

            // Convert the Hadoop Text object into a Java String.
            String line = value.toString();

            // Split the line into tokens separated by whitespace.
            StringTokenizer tokenizer = new StringTokenizer(line);

            // Process each word in the line.
            while (tokenizer.hasMoreTokens()) {

                // Store the current word in the Text object.
                word.set(tokenizer.nextToken());

                // Emit (word,1).
                context.write(word, ONE);
            }
        }
    }

    /**
     * Reducer
     *
     * Receives:
     *
     *     (word, [1,1,1,1,...])
     *
     * Adds the values together and emits:
     *
     *     (word,totalCount)
     */
    public static class WordReducer
            extends Reducer<Text, IntWritable, Text, IntWritable> {

        /**
         * Called once for each unique word.
         */
        @Override
        protected void reduce(Text key,
                              Iterable<IntWritable> values,
                              Context context)
                throws IOException, InterruptedException {

            // Running total for the current word.
            int sum = 0;

            // Add all counts associated with this word.
            for (IntWritable value : values) {
                sum += value.get();
            }

            // Emit the final count for the word.
            context.write(key, new IntWritable(sum));
        }
    }

    /**
     * Configures and launches the MapReduce job.
     *
     * args[0] = HDFS input path
     * args[1] = HDFS output path
     */
    @Override
    public int run(String[] args) throws Exception {

        // Verify that exactly two command-line arguments were supplied.
        if (args.length != 2) {
            System.err.println(
                    "Usage: WordCount <input-path> <output-path>");
            return 2;
        }

        // Create a new Hadoop job named "wordcount".
        Job job = Job.getInstance(getConf(), "wordcount");

        // Identify the class containing the main method.
        job.setJarByClass(WordCount.class);

        // Specify the mapper class.
        job.setMapperClass(WordMapper.class);

        // Specify the reducer class.
        job.setReducerClass(WordReducer.class);

        // Specify the mapper/reducer output key type.
        job.setOutputKeyClass(Text.class);

        // Specify the mapper/reducer output value type.
        job.setOutputValueClass(IntWritable.class);

        // Set the HDFS input directory or file.
        FileInputFormat.addInputPath(job, new Path(args[0]));

        // Set the HDFS output directory.
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        // Submit the job and wait for completion.
        return job.waitForCompletion(true) ? 0 : 1;
    }

    /**
     * Program entry point.
     */
    public static void main(String[] args) throws Exception {

        // Launch the Tool and return its exit code.
        int result =
                ToolRunner.run(
                        new Configuration(),
                        new WordCount(),
                        args);

        // Terminate the JVM with the result code.
        System.exit(result);
    }
}
```

```
//****************** MaxTemperatureMapper.java ************


package edu.cmu.project5.task3;

import java.io.IOException;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

public class MaxTemperatureMapper
        extends Mapper<LongWritable, Text, Text, IntWritable> {

    private static final int MISSING = 9999;

    private final Text yearText = new Text();

    @Override
    protected void map(LongWritable key,
                       Text value,
                       Context context)
            throws IOException, InterruptedException {

        // Convert Hadoop Text to Java String.
        String line = value.toString();

        // Extract year from positions 15-18.
        String year = line.substring(15, 19);

        // Extract air temperature.
        int airTemperature;

        if (line.charAt(87) == '+') {
            airTemperature =
                    Integer.parseInt(line.substring(88, 92));
        } else {
            airTemperature =
                    Integer.parseInt(line.substring(87, 92));
        }

        // Extract quality code.
        String quality = line.substring(92, 93);

        // Emit (year, temperature) if temperature is valid.
        if (airTemperature != MISSING
                && quality.matches("[01459]")) {

            yearText.set(year);

            context.write(
                    yearText,
                    new IntWritable(airTemperature));
        }
    }
}

```

```
// *********** MaxTemperatureReducer.java ****************


package edu.cmu.project5.task3;

import java.io.IOException;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class MaxTemperatureReducer
        extends Reducer<Text, IntWritable,
                        Text, IntWritable> {

    @Override
    protected void reduce(Text key,
                          Iterable<IntWritable> values,
                          Context context)
            throws IOException, InterruptedException {

        int maxValue = Integer.MIN_VALUE;

        for (IntWritable value : values) {
            maxValue =
                    Math.max(maxValue, value.get());
        }

        context.write(
                key,
                new IntWritable(maxValue));
    }
}

```
```
//*************** MaxTemperature.java ***************

package edu.cmu.project5.task3;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.Path;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;

import org.apache.hadoop.mapreduce.Job;

import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

public class MaxTemperature
        extends Configured
        implements Tool {

    @Override
    public int run(String[] args) throws Exception {

        if (args.length != 2) {
            System.err.println(
                "Usage: MaxTemperature <input-path> <output-path>");
            return 2;
        }

        Job job =
                Job.getInstance(
                        getConf(),
                        "Max temperature");

        job.setJarByClass(MaxTemperature.class);

        job.setMapperClass(
                MaxTemperatureMapper.class);

        job.setReducerClass(
                MaxTemperatureReducer.class);

        job.setOutputKeyClass(Text.class);

        job.setOutputValueClass(
                IntWritable.class);

        FileInputFormat.addInputPath(
                job,
                new Path(args[0]));

        FileOutputFormat.setOutputPath(
                job,
                new Path(args[1]));

        return job.waitForCompletion(true)
                ? 0
                : 1;
    }

    public static void main(String[] args)
            throws Exception {

        int result =
                ToolRunner.run(
                        new Configuration(),
                        new MaxTemperature(),
                        args);

        System.exit(result);
    }
}
```

## Part 2 Spark Programming

In Part 2, we are not auto-grading. You will submit a single PDF file to Gradescope.

You may use a large language model or the web to help with the questions in Part 2.
Be sure to site any external sources in your code.

In this part, we will be running Spark within IntelliJ. This is similar to what
we did in [Lab 9](https://github.com/CMU-Heinz-95702/lab9-MapReduceAndSpark). Note that in
this part, we need to use JDK 8 rather than JDK 17. There is a known issue with using
Spark and JDK 17.

When you first run IntelliJ, be sure to select a JDK 8 compiler (JDK 1.8 is the same thing).

Begin by working with the following input file named "ShortTextFile".


```
ABCD
EFGH
IJK LMN OP;/
QRST U
V WXYZ
WXYZ
abcd

cool beans
```
This file is intentionally small enough that the correct results can be verified by inspection. Your Spark program should produce the following values:

```
Number of lines: 9
Number of words: 13
Number of distinct words: 12
Number of symbols: 51
Number of distinct symbols: 39
Number of distinct letters: 35
```

For this project, count lines, words, and distinct words exactly as produced by the specified Spark transformations. A symbol is any individual character. A letter is an alphabetic character (A-Z or a-z). For distinct letters, uppercase and lowercase letters are considered different.

If your Spark program computes these same values on this small dataset, it will work well for the larger
one.  

We will be using a data file found on the course schedule. This data file is "All's Well That Ends Well" by William Shakespeare. The file is found at the following link:

[All's Well That Ends Well](http://www.andrew.cmu.edu/course/95-702/homework/data/SparkDataFiles/AllsWellThatEndsWell.txt)

You need to download "All's Well That Ends Well" to your local machine and set both your working directory and the file name in IntelliJ. See [Lab 9](https://github.com/CMU-Heinz-95702/lab9-MapReduceAndSpark) for detailed directions on setting up IntelliJ to run a Spark application with an input file.


### Part 2 Summary

Write a Java program that uses Spark to read "All's Well That Ends Well" and perform various calculations. The name of the program is ShakespeareAnalytics.java.

Simply add each task below to the code in the file ShakespeareAnalytics.java. This one file will contain all of the functionality listed here as separate tasks.

Include your labelled and well-formatted code in the PDF submission. Include an output interaction as well. The output will be a copy and paste of the IntelliJ output window. At the bottom, the interaction will show the results of searching for the string "love" in "All's Well That Ends Well". The string "love" appears, for example, in the word "cloven". The string "love" does not appear in the string "Love". Our search is case-sensitive. See Task 6.

The screen scrape of the output must include all of the "Info" lines that are generated by IntelliJ. Your program output will be interspersed in this output.

For debugging and development, it is a good idea (but not required) to pepper your development code with lines that write RDD's to a file. It is fine if you leave such code in your final submission. The following is an example from my solution:

```
 pairData.saveAsTextFile("002_WordsPairedWith1");

```

### Documentation

ShakespeareAnalytics.java needs to be well documented. It must begin with the author's name and an overall description. Each line of code needs to be described in your own words.

### Task 0.

Using the count method of the JavaRDD class, display the number of lines in "All's Well That Ends Well".

Write this output to the screen with System.out.println(). This output will be included in your PDF.

### Task 1.

Using the split method of the java String class and the flatMap method of the JavaRDD class,
use the count method of the JavaRDD class to display the number of words in "All's Well That Ends Well". So that we are all on the same page, be sure to use
the string "[^a-zA-Z]+" as the regular expression delimiter in your split method.

In Task 1 and Task 2, you might also make good use of a filter function:

```
Function<String, Boolean> filter = k -> ( !k.isEmpty());

```

Write this output to the screen with System.out.println(). This output will be included in your PDF.


### Task 2.

Using some of the work you did above and the JavaRDD distinct() and count() methods, display
the number of distinct words in "All's Well That Ends Well".

Write this output to the screen with System.out.println(). This output will be included in your PDF.

### Task 3.

Use the split method with a regular expression of "" and a flatmap to find the number of symbols in "All's Well That Ends Well".

Write this output to the screen with System.out.println(). This output will be included in your PDF.

### Task 4.

Find the number of distinct symbols in "All's Well That Ends Well".

Write this output to the screen with System.out.println(). This output will be included in your PDF.

### Task 5.

Find the number of distinct letters in "All's Well That Ends Well".

Write this output to the screen with System.out.println(). This output will be included in your PDF.

### Task 6.

This is an interactive piece. Ask your user to enter a word and show all of the lines of "All's Well That Ends Well" that contain that word. The search will be case-sensitive. If, for example, the user enters the word "love", she would see such lines as:

```
Attend his love.
That I should love a bright particular star
Th' ambition in my love thus plagues itself:
:
:

```
But she would see no lines with an uppercase "Love".

Interact with the user and write this output to the screen with System.out.println(). This output will be included in your PDF.

### Part 2 Summary

Part 2 will be graded by carefully inspecting the single program and the output (including INFO lines).

## Deliverables

A grader may examine:

* The single pdf file submitted to Gradescope.
* Source code documentation in ShakespeareAnalytics.java.
* Successful execution of ShakespeareAnalytics.java.

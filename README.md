# 95-702 Distributed Systems For Information Systems Management
# Distributed Computation
## Project 5 Spring 2025

Assigned: Monday, April 7, 2025

Due: Monday, April 21, 2:00 PM

**Principles**

Big data infrastructures support distributed computation through services
such as MapReduce and Spark. Both of these approaches exemplify the
principle of spatial locality by moving computation close to data. These
data are then processed in parallel.

**Learning objectives**

The student will be able to program applications using MapReduce and Spark.
For those new to Linux, this project will also expose you to compiling and running Java programs in a Linux environment.
There is introductory Linux material on Canvas. See the "linuxTutorial.mp4" and the "Unix command Quick List" files in
the Canvas module labelled "Distributed Computation".

**How will you be graded?**

Note: We will assume, unless otherwise noted, that all problems operating on text data are case sensitive. The letters "E" and "e" are two different letters.

You need to post a single pdf file to Canvas and provide your Heinz cluster password by taking the online quiz. You will make a Heinz cluster account available for inspection by the grader. The pdf will be clearly labelled. Your name and email will be at the top. ***Be sure to order and label the pdf
sections as described in the next 6 steps.***

Please organize your pdf file so that the grader can evaluate your work.

Using the pdf, the grader will:

1. Open your single pdf file on Canvas.
2. Look at the screen shot of Google Earth from Part 1, Task 7.
3. Look at your single Spark program from Part 2. This will be graded based on documentation and execution.
4. Look at the output screenshot of Part 2. This is the entire Part 2 execution. It is not just the interactive piece. It includes the print statements from all of the Part 2 tasks.
5. Look at your Heinz cluster user ID (studentxxx). This is labelled and in the pdf.
6. Look at your Heinz cluster password. This is also labelled and in the pdf.

The grader will then use the Heinz cluster to:

7. Log on to your Heinz cluster account. He or she will need the password from the pdf.
8. cd into Project5 and cd into Part_1.
9. cd into a task and grade it based upon
correct execution and documentation. All files, as described below,
should be available for inspection by the grader. In particular, the grader will be looking for the output files that resulted from the merge operations. The HDFS input and output directories on the cluster will be empty. The grader will be able to copy the input file to the cluster (using copyFromLocal) and will be able to deploy and execute the jar file without first having to delete the HDFS output directory. The input file, source code, and other task related artifacts will be available in the directory Project5/Part_1/TaskX (where X = 0,1,2,3,4,5,6,7).


**These notes are for later reference**

When documenting your code, follow the Documentation Example shown in the Course Information Module on Canvas.

[Documentation Example](./Documentation.md)

Your well documented programs will always begin with a block of comments
(using Javadoc style or regular style) that provide the name of the programmer and the purpose of the program.

On the Heinz High Performance Cluster, we will be working with two different
directory structures. The first is your local directory structure /home/userID.
The second is the HDFS directory structure /user/userID. We will compile code
on the first (/home/userID) and then deploy jar files to the second (/user/userID).

The input to the program must be stored on HDFS and the output from the program will be written to HDFS. So, we will be copying input from our local system to HDFS before running a jar. We will be copying the output from HDFS
to our local directory with the hadoop getMerge utility.

The code and example data for the MaxTemperature application is from
"Hadoop: The Definitive Guide, Second Edition, by Tom White.
Copyright 2011 Tom White, 978-1-449-38973-4."

We will be running Linux, Centos 7.7, on a Hadoop cluster of several
virtual machines at Heinz College.

To reach the hadoop cluster, you will need to ssh into
jumbo2.heinz.cmu.local.

If you are working from home, first run the Cisco AnyConnect
Secure Mobility Client. This tool may be downloaded from here:

[Cisco Any Connect](https://www.cmu.edu/computing/services/endpoint/network-access/vpn/how-to/)

Enter vpn.cmu.edu in Cisco Any Connect. You should then be able to connect to
the cluster with SSH.

```
ssh -l YOUR_USER_ID jumbo2.heinz.cmu.local

```

If the above command fails, try the following:

```
ssh -oHostKeyAlgorithms=+ssh-rsa YOUR_USER_ID@jumbo2.heinz.cmu.local

```

There are Linux text editors available (pico, nano, and vi).
You should spend a little time learning your text editor of choice. There
is plenty of help online.

To use nano, use these commands:

```
nano filename.java        starts the editor
<control> o               to save a file
<control> x               to exit nano.

```

On the cluster, your home directory is /home/userID.

The hadoop jars and binaries are store at /usr/local/hadoop/share/hadoop/mapreduce and /usr/local/hadoop/share/hadoop/common/.

Your HDFS file system directories are at /user/userID/output.

Note that one is "usr" and the other is "user".

It is assumed that students with Windows machines will be running putty or
some other telnet tool to connect to the cluster. Mac users will likely
use ssh.

It is also assumed that each student has a user-ID and password for the Heinz cluster.

You should have established a new password when you completed the
Hadoop lab. Your user-ID should be on the grade book on Canvas. You should
communicate your password to the TA's by taking the quiz which asks for your
password. Also, note that your user ID and password need to be placed on the submission pdf.

If you need to transfer data from your machine to the cluster, be sure to use
sftp. The "copy and paste" approach will likely introduce hidden characters in your file. Here is an example execution of sftp.

```
sftp userID@jumbo2.heinz.cmu.local
sftp>put MaxTemperature.java
sftp>get MaxTemperature.java
```

Here are some basic examples that you will need to study before attempting to work on the cluster.
They are meant for reference. When working on the tasks below, you will need to use the commands
here (but with appropriate modifications based on the task.) These commands will not run unless
they are executed in the appropriate order and with appropriate resources. Spend some time
studying them and then begin to use those that you need to complete the tasks.

The overall idea is to compile your code, package it into a Java archive (JAR), and deploy the JAR for execution on the cluster. Below, we demonstrate how to perform these steps and also outline some basic Linux commands that you may need.

**Note: Use the up arrow in unix to select previously executed commands.**

The user wants to sort an employee file on the second column of a file and that column is numeric. We want high values near the top. The switch "2nr" means second column, numeric, reverse order. This is standard Linux and may be used when you are asked to sort a file.

```
sort -k 2nr employee.txt

```
The user wants to copy an existing directory and all of its content to a new directory. The destination directory is created with this command. The "R" is for recursive. This is standard Linux.

```
cp -R source_dir destination_dir

```

The user would like to see a list of files on the Hadoop distributed file system under /user/userID/input.

Note again the use of "user" and not "usr". "user" is a reference to the directory in HDFS. "usr" is a reference
to your local directory. You might use this command to view the input to a map reduce job that you plan to run.

```
hdfs dfs -ls /user/userID/input/

```

Suppose an input file needs to be placed under HDFS. This file will be used for subsequent map reduce processing.
With this command, the HDFS file and directory are created. If they already exist on HDFS, then this command will return an 'already exists' message.

```
hdfs dfs -copyFromLocal /home/userID/input/1902.txt /user/userID/input/1902.txt

```

Look at the contents of a file on HDFS.

```
hdfs dfs -cat /user/userID/input/testFile

```

Remove a local directory of Java classes that may contain Java packages. Typically, the code in temperature_classes is already compiled and exists
in a directory that corresponds to Java package names. This is standard Linux.

```
rm -r temperature_classes
```

Create a directory for Java classes. This is standard Linux.

```
mkdir temperature_classes

```

Compile three Java classes using a library of Hadoop classes stored in
a jar. The classes directory (./temperature_classes) is the
target of the compile and may be populated with a directory structure
that corresponds to Java packages. The classes directory (./temperature_classes) is also consulted during the compile. The Java files are in the current directory. The directory temperature_classes must already exist as a subdirectory of the current directory. This is all standard Linux.

These commands assume that you are in a directory just above temperature_classes.
So, before running the first compile, if we execute an 'ls' command, we would see:

```
ls
temperature_classes MaxTemperatureMapper.java MaxTemperatureReducer.java MaxTemperature.java

```
Here we compile our Java source code (MaxTemperatureMapper.java, MaxTemperatureReducer.java, and MaxTemperature.java). We place the compiled code into the directory named temperature_classes. This directory must exist before running this compile step. The temperature_classes directory will be populated with a directory structure corresponding to our java package.  

The necessary jar files are already in our class path and we include the $CLASSPATH in the compile.


```
javac -d temperature_classes -cp temperature_classes:$CLASSPATH MaxTemperatureMapper.java

javac -d temperature_classes -cp temperature_classes:$CLASSPATH MaxTemperatureReducer.java

javac -d temperature_classes -cp temperature_classes:$CLASSPATH MaxTemperature.java


```
To compile a single file and place the resulting classes into a directory, make the directory and execute the following command:

```
javac -d wordcount_classes WordCount.java

```
To examine the classpath variable in linux:

```
echo $HADOOP_CLASSPATH
echo $CLASSPATH

```

Remove an existing jar file using standard Linux.

```
rm temperature.jar
```

Create a new jar file called temperature.jar. It will include all of the classes found in temperature_classes. Note the "." at the end of the line. The dot is important here. It refers
to the current directory. Note too that temperature_classes must be a subdirectory of the current directory. This is standard Linux.

In the jar (Java Archive) command, the "c" switch means to create a new archive. The "v" switch means to produce verbose output. The "f" switch means that the file name is being provided. In this case, it is "temperature.jar".

Again, note the dot at the end of the line.

```
jar -cvf temperature.jar -C  temperature_classes/  .

```

Remove the output directory from the distributed file system. We need to do this prior to running
a new job.

```
hdfs dfs -rm -r /user/userID/output

```

Merge and copy files from the Hadoop Distributed File system to the client.

```
hdfs dfs -getmerge /user/userID/output aCoolLocalFile

```

You may view what jobs are running on the cluster with the command:
```
mapred job -list


```

Kill a job that is not making progress (you may need to do this):
```

mapred job -kill job_1727103369351_0002


```
To kill a job, you will need the Job ID. The Job ID may be found by running mapred job -list.

The "hadoop jar" command allows us to execute a map reduce job on the cluster. The file temperature.jar holds the map reduce code. Next,
a path to the class with a main routine is provided. Then, the input and output is specified. The input is a file
that must exist on the distributed file system and the output is a directory that will be created. It must
not exist before running the command or you will receive an exception.

```
hadoop jar /home/userID/temperature.jar edu.cmu.andrew.mm6.MaxTemperature  /user/userID/input/combinedYears.txt /user/userID/output

```

We wish to get a copy of the content of the output directory. The second command is standard Linux.
```

hdfs dfs -getmerge /user/userID/output coolProjectOutput

cat coolProjectOutput

```

Note that the code below is defined within Java packages. So, when you compile
the source code the compiled (.class files) will be placed within directories
and sub directories.

:checkered_flag:**The proper submission of Project 5 is worth points on your Project 5 grade. In order to submit properly, your directory structure must be as described here:**

Make a directory in your home directory called Project5.

```
cd
mkdir Project5

```

Within the Project5 directory, make an additional subdirectory with the
name Part_1. Within the Part_1 directory make the following subdirectories: Task0,
Task1, Task2 and so on, all the way through Task7. Place all Task0 work within the
Task0 directory. Place all Task1 work within the Task1 directory and so on, all the way through Task7.

For Task0,

```
cd Project5
mkdir Part_1
cd Part_1
mkdir Task0
cd Task0

```

:checkered_flag:**This is the beginning of the actual assignment.**

## Part 1 Map Reduce Programming

### Task 0   

Compile and execute a MapReduce job developed from WordCount.java. The file
WordCount.java is found under /home/public/. The code is also available at
the bottom of this document. Copy it to your Project5/Part_1/Task0 directory. Compile it and
generate a jar file called wordcount.jar. Deploy the jar file and test it
against /home/public/words.txt. The file words.txt will need to be copied
to HDFS using Hadoop's copyFromLocal command.

Note, you may view the output with an hdfs dfs -cat command:

```
hdfs dfs -cat /user/userID/output/part-r-00000

```

The final output should be merged and left in
your /home/userID/Project5/Part_1/Task0/Task0Output file.

The grader will be looking for this merged result file.

You may use a large language model or the web to help with this problem. However, ensure you understand the code you submit, as your comprehension will be crucial for performing well on the next exam. Additionally, be sure to cite any external sources in your code. If you use an LLM tool to generate the code, make sure to cite the tool.

### Task 1  

Working from WordCount.java, build and deploy a MapReduce application
named LetterCounter.java and place it into a jar file called lettercount.jar.
This program will compute the total number of each letter in the words.txt file. The output from the reducer will need to be merged and then sorted (use the standard Linux sort command). The most
frequently occurring letter and its count will appear at the top of the file - sorted by decreasing frequency. The letter "e" is the most common letter and will appear at the top of the file.

The result will be left in your /home/userID/Project5/Part_1/Task1/Task1Output file. The grader will be looking for this merged result file.

Note that
WordCount.java (Task 0) uses a call to nextToken() on the iterator. Without this call,
the program will enter an infinite loop and will need to be killed.

You may use a large language model or the web to help with this problem. However, ensure you understand the code you submit, as your comprehension will be crucial for performing well on the next exam. Additionally, be sure to cite any external sources in your code. If you use an LLM tool to generate the code, make sure to cite the tool.

### Task 2  

Create a Java program named "FindPattern.java". It will be a modification of the WordCount.java code. FindPattern.java searches through words.txt and outputs any word that contains the string "fun". This is not a case sensitive search.
The final output will be a single file containing a list of words that contain the string "fun". If the word "Fungia" appeared in the input, it too would be output. As was noted, the search is not case sensitive.

All of these words will be listed in the following file:
/home/userID/Project5/Part_1/Task2/Task2Output. The grader will be looking for this merged result file.

Here is the start of the final output with a few words that contain "fun":

```
Infundibulata
afunction
antifundamentalist
defunct

```

You may use a large language model or the web to help with this problem. However, ensure you understand the code you submit, as your comprehension will be crucial for performing well on the next exam. Additionally, be sure to cite any external sources in your code. If you use an LLM tool to generate the code, make sure to cite the tool.

### Task 3


In this task, we will use a fairly large dataset from Tom White's book.
The data file is located at "/home/public/combinedYears.txt". It contains thousands of temperature readings
(in Celsius times 10) in the USA by year from 1901 to 1902. The year is specified
in positions 15-18. The temperature is specified beginning at column 87 and begins
with a plus or minus character. Four digits are used for the temperature. Be sure
to study the code below to see how it references these locations.

Below are three files: MaxTemperature.java, MaxTemperatureMapper.java and MaxTemperatureReducer.java. These files may be found at /home/public. Copy these files to your
/home/userID/Project5/Part_1/Task3 directory.

Run this application against the data set under /home/public/combinedYears.txt. Your jar file will
be named temperature.jar.  The output should be left in your
/home/userID/Project5/Part_1/Task3/Task3Output file. The grader will be looking for this merged result file.

You may use a large language model or the web to help with this problem. However, ensure you understand the code you submit, as your comprehension will be crucial for performing well on the next exam. Additionally, be sure to cite any external sources in your code. If you use an LLM tool to generate the code, make sure to cite the tool.

### Task 4

Modify the code from Task 3 and build a minimum temperature application. Note that the
temperatures in this file are degrees Celsius * 10.  Your jar file will be named
mintemperature.jar.  The output should be left in your
/home/userID/Project5/Part_1/Task4/Task4Output
file. The grader will be looking for this merged result file.

You may use a large language model or the web to help with this problem. However, ensure you understand the code you submit, as your comprehension will be crucial for performing well on the next exam. Additionally, be sure to cite any external sources in your code. If you use an LLM tool to generate the code, make sure to cite the tool.


### Task 5

Within the /home/public directory, there is a file called P1V.txt.

P1V.txt is a tab delimited text file with a individual criminal offense incidents
from January 1990 through December 1999 for serious violent crimes (FBI Part 1) in Pittsburgh.

The first two columns (X,Y) represent State Plane (projected, rectilinear) coordinates (measured
in feet) specifying the location of the crime.
The third column is the time.
The fourth column is a street address.
The fifth column is the type of offense (aggravated assault, Robbery, Rape, Etc.)
The sixth column is the date.
The seventh column is the 2000 census tract.

Write a MapReduce application that finds the total number of aggravated assaults and
robberies. If there were 100 aggravated assaults and 50 robberies then this program
would generate the value 150 to an output file.

Your jar file will be named aggrvatedassaultsplusrobberies.jar. The output should be left
in your /home/userID/Project5/Part_1/Task5/Task5Output file. The grader will be looking for this merged result file.

You may **not** use a large language model or any external source for this task.

### Task 6

Modify your solution to Task 5 so that it finds the total number of aggravated assault crimes that occurred within
100 meters of 3803 Forbes Avenue in Oakland. This location has the (X,Y) coordinates of
(1354326.897,411447.7828). Use these coordinates and the Pythagorean theorem to decide if a particular
aggravated assault occurred within 100 meters of 3803 Forbes Avenue. State plane coordinates are measured in feet.
Your code is testing on meters.

Your jar file will be named oaklandcrimestats.jar. The output should be left
in your /home/userID/Project5/Part_1/Task6/Task6Output file. The grader will be looking for this merged result file.

You may **not** use a large language model or any external source for this task.

### Task 7


In Task 7, the input file is named CrimeLatLonXYTabs.txt. It can be copied from the /home/public directory.
Its format is similar to P1V.txt but also includes the latitude and longitude of each crime.

CrimeLatLonXYTabs.txt is a tab delimited text file with a individual criminal offense incidents
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
crimes that occurred within 100 meters of 3803 Forbes Avenue in Oakland. This location has
the (X,Y) coordinates of (1354326.897,411447.7828). Use these coordinates and the Pythagorean
theorem to decide if a particular aggravated assault occurred within 100 meters of 3803 Forbes
Avenue. State plane coordinates are measured in feet. Your code is testing on meters.

Your jar file will be named oaklandcrimestatskml.jar. The output file will be a well formed KML file
suitable for viewing in Google Earth. The KML file will be used to display each of these crimes on
a map. The output file will be left in your /home/userID/Project5/Part_1/Task7/Task7Output file. The grader will be looking for this merged result file.

Here is a simple KML file from Google. You can save it and then load it into Google Earth. Your solution will be a longer file.

```
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2">
  <Placemark>
    <name>Simple placemark</name>
    <description>Attached to the ground. Intelligently places itself
       at the height of the underlying terrain.</description>
    <Point>
      <coordinates>-122.0822035425683,37.42228990140251,0</coordinates>
    </Point>
  </Placemark>
</kml>
```

You will also need to provide a screenshot showing Google Earth viewing the KML. Include the screenshot on your pdf that is submitted to Canvas.

You should consider limiting the number of reducers to 1. You can do this by
generating the same key from each mapper.

You may **not** use a large language model or any external source for this task.

### Part 1 Summary

Part 1 will be graded by carefully inspecting tasks on the cluster.

### WordCount.java

```
package org.myorg;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.TextInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

public class WordCount extends Configured implements Tool {

    public static class MapClass extends Mapper<LongWritable, Text, Text, IntWritable> {
        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
            String line = value.toString();
            StringTokenizer tokenizer = new StringTokenizer(line);
            while (tokenizer.hasMoreTokens()) {
                word.set(tokenizer.nextToken());
                context.write(word, one);
            }
        }
    }

    public static class ReduceClass extends Reducer<Text, IntWritable, Text, IntWritable> {
        public void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
                sum += val.get();
            }
            context.write(key, new IntWritable(sum));
        }
    }

    public int run(String[] args) throws Exception {
        Configuration conf = getConf();
        Job job = Job.getInstance(conf, "wordcount");
        job.setJarByClass(WordCount.class);

        job.setMapperClass(MapClass.class);
        job.setReducerClass(ReduceClass.class);

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        job.setInputFormatClass(TextInputFormat.class);
        job.setOutputFormatClass(TextOutputFormat.class);

        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        return job.waitForCompletion(true) ? 0 : 1;
    }

    public static void main(String[] args) throws Exception {
        int res = ToolRunner.run(new Configuration(), new WordCount(), args);
        System.exit(res);
    }
}

```
### MaxTemperatureMapper.java

```

// ============== MaxTemperatureMapper.java ================================
  package edu.cmu.andrew.mm6;
  import java.io.IOException;
	import org.apache.hadoop.io.IntWritable;
	import org.apache.hadoop.io.LongWritable;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapred.MapReduceBase;
	import org.apache.hadoop.mapred.Mapper;
	import org.apache.hadoop.mapred.OutputCollector;
	import org.apache.hadoop.mapred.Reporter;

	public class MaxTemperatureMapper extends MapReduceBase implements Mapper<LongWritable, Text, Text, IntWritable> {
		private static final int MISSING = 9999;
		public void map(LongWritable key, Text value, OutputCollector<Text, IntWritable> output, Reporter reporter) throws 			IOException {

                        // Get line from input file. This was passed in by Hadoop as value.
                        // We have no use for the key (file offset) so we are ignoring it.

			String line = value.toString();

                        // Get year when weather data was collected. The year is in positions 15-18.
                        // This field is at a fixed position within a line.
			String year = line.substring(15, 19);

                        // Get the temperature too.

			int airTemperature;
			if (line.charAt(87) == '+') { // parseInt doesn't like leading plus signs
				airTemperature = Integer.parseInt(line.substring(88, 92));
			} else {
				airTemperature = Integer.parseInt(line.substring(87, 92));
                        }

                        // Get quality of reading. If not missing and of good quality then
                        // produce intermediate (year,temp).

                        String quality = line.substring(92, 93);
			if (airTemperature != MISSING && quality.matches("[01459]")) {

                             // for each year in input, reduce will be called with
                             // (year,[temp,temp,temp, ...])
                             // They key is year and the list of temps will be placed in an iterator.

				output.collect(new Text(year), new IntWritable(airTemperature)); }
			}
	 }
```
### MaxTemperatureReducer.java

```

=========== MaxTemperatureReducer.java ====================================================
	package edu.cmu.andrew.mm6;
  import java.io.IOException;
  import java.util.Iterator;
	import org.apache.hadoop.io.IntWritable;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapred.MapReduceBase;
	import org.apache.hadoop.mapred.OutputCollector;
	import org.apache.hadoop.mapred.Reducer; import org.apache.hadoop.mapred.Reporter;

	public class MaxTemperatureReducer extends MapReduceBase implements Reducer<Text, IntWritable, Text, IntWritable> {

		public void reduce(Text key, Iterator<IntWritable> values, OutputCollector<Text,
                                   IntWritable> output, Reporter reporter) throws IOException {

                        // from the list of values, find the maximum
                        int maxValue = Integer.MIN_VALUE;
                        while (values.hasNext()) {
			     maxValue = Math.max(maxValue, values.next().get());
                        }
                        // emit (key = year, value = maxTemp = max for year)
			output.collect(key, new IntWritable(maxValue));
		}
	}

```
### MaxTemperature.java

```
// ======= And, to get it all running and tied together: MaxTemperature.java ============

  package edu.cmu.andrew.mm6;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.IntWritable;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapred.FileInputFormat;
	import org.apache.hadoop.mapred.FileOutputFormat; import org.apache.hadoop.mapred.JobClient;
	import org.apache.hadoop.mapred.JobConf;

	public class MaxTemperature {
		public static void main(String[] args) throws IOException {
			if (args.length != 2) {
				System.err.println("Usage: MaxTemperature <input path> <output path>");
				System.exit(-1);
 			}
			JobConf conf = new JobConf(MaxTemperature.class);
			conf.setJobName("Max temperature");
			FileInputFormat.addInputPath(conf, new Path(args[0]));
			FileOutputFormat.setOutputPath(conf, new Path(args[1]));
			conf.setMapperClass(MaxTemperatureMapper.class);
			conf.setReducerClass(MaxTemperatureReducer.class);
			conf.setOutputKeyClass(Text.class);
			conf.setOutputValueClass(IntWritable.class);
			JobClient.runJob(conf);
		 }
	}

```

## Part 2 Spark Programming

You may use a large language model or the web to help with Part 2. However, ensure you understand the code you submit, as your comprehension will be crucial for performing well on the next exam. Additionally, be sure to cite any external sources in your code. If you use an LLM tool to generate the code, make sure to cite the tool.

In this part, we will be running Spark within IntelliJ. This is similar to what
we did in [Lab 9](https://github.com/CMU-Heinz-95702/lab9-MapReduceAndSpark). Note that in this part, we need to use JDK 8 rather than
JDK 17. There is a known issue with using Spark and JDK 17.

When you first run IntelliJ, be sure to select a JDK 8 compiler (JDK 1.8 is the same thing).

Begin by working with the following input file named "ShortTextFile". Note that "ABCD" is not the same as "abcd".

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

Write a Spark program that generates the following counts:

```
Number of lines: 9
Number of words: 13
Number of distinct words: 12
Number of symbols: 51
Number of distinct symbols: 39
Number of distinct letters: 35
```

If your Spark program computes these same values on this small dataset, it will work well for the larger
one.

Note, the number of symbols is 51. In Java, the split("") method does not include trailing newline characters at the end of a string. So, if a line ends with a newline character, it won’t be included in the resulting array. However, if there is a newline character on a line by itself (i.e., an empty line), it will be included as a separate element in the array.


We will be using a data file found on the course schedule. This data file is "All's Well That Ends Well" by William Shakespeare. The file is found at the following link:

[All's Well That Ends Well](http://www.andrew.cmu.edu/course/95-702/homework/data/SparkDataFiles/AllsWellThatEndsWell.txt)

You need to download "All's Well That Ends Well" to your local machine and set both your working directory and the file name in IntelliJ. See [Lab 9](https://github.com/CMU-Heinz-95702/lab9-MapReduceAndSpark) for detailed directions on setting up IntelliJ to run a Spark application with an input file.



### Part 2 Summary

Write a Java program that uses Spark to read "All's Well That Ends Well" and perform various calculations. The name of the program is ShakespeareAnalytics.java.

Simply add each task below to the code in the file ShakespeareAnalytics.java. This one file will contain all of the functionality listed here as separate tasks.

Include your labelled and well-formatted code in the pdf submission. Include an output interaction as well. The output will be a copy and paste of the IntelliJ output window. At the bottom, the interaction will show the results of searching for the string "love" in "All's Well That Ends Well". The string "love" appears, for example, in the word "cloven". The string "love" does not appear in the string "Love". Our search is case-sensitive. See Task 6.

The copy and paste of the output must include all of the "Info" lines that are generated by IntelliJ. Your program output will be interspersed in this output.

For debugging and development, it is a good idea (but not required) to pepper your development code with lines that write RDD's to a file. It is fine if you leave such code in your final submission. The following is an example from my solution:

```
 pairData.saveAsTextFile("002_WordsPairedWith1");

```

### Documentation

ShakespeareAnalytics.java needs to be well documented. It must begin with the author's name and an overall description. Each line of code needs to be described in your own words.

### Task 0.

Using the count method of the JavaRDD class, display the number of lines in "All's Well That Ends Well".

Write this output to the screen with System.out.println(). This output will be included in your pdf.

### Task 1.

Using the split method of the java String class and the flatMap method of the JavaRDD class,
use the count method of the JavaRDD class to display the number of words in "All's Well That Ends Well". So that we are all on the same page, be sure to use
the string "[^a-zA-Z]+" as the regular expression delimiter in your split method.

In Task 1 and Task 2, you might also make good use of a filter function:

```
Function<String, Boolean> filter = k -> ( !k.isEmpty());

```

Write this output to the screen with System.out.println(). This output will be included in your pdf.


### Task 2.

Using some of the work you did above and the JavaRDD distinct() and count() methods, display
the number of distinct words in "All's Well That Ends Well".

Write this output to the screen with System.out.println(). This output will be included in your pdf.

### Task 3.

Use the split method with a regular expression of "" and a flatmap to find the number of symbols in "All's Well That Ends Well".

Write this output to the screen with System.out.println(). This output will be included in your pdf.

### Task 4.

Find the number of distinct symbols in "All's Well That Ends Well".

Write this output to the screen with System.out.println(). This output will be included in your pdf.

### Task 5.

Find the number of distinct letters in "All's Well That Ends Well". Note that "e" is not the same letter as "E". In this task, we are case-sensitive.

Write this output to the screen with System.out.println(). This output will be included in your pdf.

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

Interact with the user and write this output to the screen with System.out.println(). This output will be included in your pdf.

### Part 2 Summary

Part 2 will be graded by carefully inspecting the single program and the output (including INFO lines).

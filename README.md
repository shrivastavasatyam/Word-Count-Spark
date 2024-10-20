# Word Count - Spark (Scala)

This project implements a Word Count program using Apache Spark and Scala. The program processes a text dataset and counts the frequency of each word in the input. It also covers various deployment scenarios, including running the Spark job locally, in a pseudo-distributed Hadoop cluster, and on AWS EMR (Elastic MapReduce). Additionally, this version of the Spark WordCount demonstrates how to visualize the lineage of the RDD transformations to gain insights into the computation process. This README.md provides detailed instructions for setting up the required environment, executing the program in different modes, and using AWS for distributed processing.

Author
-----------
- Satyam Shrivastava

Installation
------------
This project was set up on an Apple MacBook M1 Pro. The following components need to be installed first:
- OpenJDK 11 (installed via Homebrew)
- Hadoop 3.3.5 (downloaded from https://hadoop.apache.org/release/3.3.5.html)
- Maven (downloaded from https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.6.3/, using the first link)
- Scala 2.12.17 (downloaded from https://www.scala-lang.org/download/2.12.7.html)
- Spark 3.3.2 (without bundled Hadoop, downloaded from http://download.nust.na/pub2/apache/spark/spark-3.3.2/)
- AWS CLI 1 (installed via Homebrew)

### Steps to Install

1) Install OpenJDK 11: Install OpenJDK 11 using Homebrew:

   `brew install openjdk@11`

2) Install Hadoop 3.3.5: Download Hadoop 3.3.5, unzip the file, and move it to the appropriate directory:

   `mv hadoop-3.3.5 /usr/local/hadoop-3.3.5`

3) Install Maven 3.6.3: Download Maven 3.6.3, unzip the file, and move it to the appropriate directory:

   `mv apache-maven-3.6.3 /usr/local/apache-maven-3.6.3`

4) Install Scala 2.12.7: Download Scala 2.12.7, unzip the file, and move it to the appropriate directory:

   `mv scala-2.12.7 /usr/local/share/scala`

5) Install Spark 3.3.2 (without bundled Hadoop): Download Spark 3.3.2 (without bundled Hadoop), unzip the file, and move it to the appropriate directory:

   `mv spark-3.3.2-bin-without-hadoop /usr/local/spark-3.3.2-bin-without-hadoop`

6) Install AWS CLI 1: Install AWS CLI version 1 using Homebrew:

   `brew install awscli@1`

After installation, ensure the components are correctly set up by checking their versions:
```
java -version
hadoop version
mvn -version
aws --version
scala -version
spark-submit --version
```

Environment
-----------
1) **Setting up Environment Variables**:

   Environment variables are required to be defined based on the preferred shell. The zsh (Z-Shell) is used for this project on MacOS.

   Following are the relevant environment variables (in `~/.zshrc`) based on project's setup:

   ```
   export JAVA_HOME=/opt/homebrew/opt/openjdk@11

   export HADOOP_HOME=/usr/local/hadoop-3.3.5
   export YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop
   export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

   export M2_HOME=/usr/local/apache-maven-3.6.3
   export PATH=$M2_HOME/bin:$PATH

   export SCALA_HOME=/usr/local/share/scala
   export PATH=$PATH:$SCALA_HOME/bin

   export SPARK_HOME=/usr/local/spark-3.3.2-bin-without-hadoop
   export PATH=$PATH:$SPARK_HOME/bin
   export SPARK_DIST_CLASSPATH=$(hadoop classpath)
   
   export PATH="/opt/homebrew/opt/awscli@1/bin:$PATH"
   ```

2) Explicitly set `JAVA_HOME` in the Hadoop configuration file. Edit `$HADOOP_HOME/etc/hadoop/hadoop-env.sh` and add:

   `export JAVA_HOME=/opt/homebrew/opt/openjdk@11`

Execution
---------
Following are the general steps of execution used for implementing the project:

1) Clone the project's GitHub repository or unzip the project files into Visual Studio Code (or any preferred IDE).

2) Open Terminal window in VS Code. Navigate to directory where project files unzipped.

3) **Edit Makefile** and `pom.xml` **file**:
   All build and execution commands are organized in the Makefile. The Makefile is pre-configured to handle local, pseudo-distributed, and AWS EMR setups. Project can be executed directly using the `make` commands provided below without modifying the Makefile unless we're customizing the environment.
   Additionally, the pom.xml file is configured with the recommended software versions for this course (such as OpenJDK 11, Hadoop 3.3.5, and Maven 3.6.3, Spark 3.3.2, Scala 2.12.7). Since the same versions are being used as suggested in the course, there's no need to update the pom.xml file
   
   Edit the Makefile to customize the environment at the top.
	Sufficient for standalone: hadoop.root, jar.name, local.input
	Other defaults acceptable for running standalone.

4) Standalone Hadoop:
	- `make switch-standalone`		-- set standalone Hadoop environment (execute once)
	- `make local`

5) Test Run with a Small File:
   Before running the full program, it's a good idea to validate the setup with a small test file for visualizing the results easily. Create a simple text file (`hhg_Small.txt`) with 2-3 lines in the input folder. Create a separate `outputSmall` folder. Edit the parameters (local.input and local.output) in Makefile. Execute the WordCount program locally using the small file now. Visualize the output.
	- `make local`

6) Adding Lineage Visualization to the WordCount Program:
   To visualize the lineage of the RDD transformations, modify the `WordCount.scala` file to include a call to `toDebugString`. Here's how:
   	- Open `WordCount.scala` and add the following before the `saveAsTextFile` line:

   	  `logger.info(counts.toDebugString)`
   	
    - Run the program to  see the lineage printed in the generated logs.

7) Pseudo-Distributed Hadoop: (https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SingleCluster.html#Pseudo-Distributed_Operation)
	- `make switch-pseudo`			-- set pseudo-clustered Hadoop environment (execute once)
	- `make pseudo`					-- first execution
	- `make pseudoq`				-- later executions since namenode and datanode already running

8) AWS EMR Hadoop: (you must configure the emr.* config parameters at top of Makefile)
	- `make make-bucket`			-- Create an S3 bucket (only for the first execution)
	- `make upload-input-aws`		-- Upload input data to AWS S3 (only for the first execution)
	- `make aws`					-- Run the Hadoop job on AWS EMR. Check for successful execution with web interface (aws.amazon.com)
	- `download-output-aws`		-- After successful execution & termination, download the output from S3
 	- `download-log-aws`		-- After successful execution & termination, download the logs from S3
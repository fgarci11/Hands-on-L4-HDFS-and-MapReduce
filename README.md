## Hands-on L4: Word count using MapReduce

## Project Overview

The purpose of this hands-on is to gain practical experience with Hadoop MapReduce
Jobs by implementing a Word Count program. This project creates Hadoop MapReduce 
jobs using Java and Maven, deploys Hadoop MapReduce jobs using Docker, and uploads 
files to the Hadoop cluster.

## Approach and Implementation – Explanation of the Mapper and Reducer logic

This program utilizes a Mapper to prepare the data for counting and a Reducer to aggregate the final counts and present them in a sorted order.

## Explanation of the WordReducer Logic

The WordMapper class is responsible for the "map" phase of the MapReduce job. Its primary role is to process input data (in this case, lines of text) and transform it into key-value pairs. This transformation is crucial as it standardizes the data for efficient aggregation by the reducer.

The map method is the heart of the mapper's logic. It receives an input line of text as a Text object. The process unfolds as follows:

Tokenization: The input line is broken down into individual words using the StringTokenizer. This allows the program to process each word separately.

Filtering: A conditional statement (if (currentWord.length() >= 3)) is used to filter out short words. This ensures that only words with three or more characters are considered for the final count.

Key-Value Pair Emission: For each word that passes the filter, the mapper emits a new key-value pair using context.write(word, one). The word itself becomes the key (Text), and a constant value of one (IntWritable(1)) is assigned as its value. This simple (word, 1) structure is a fundamental part of word counting; each emission signifies a single occurrence of that word. 

## Explanation of the WordReducer Logic
The WordReducer class handles the "reduce" phase, where it aggregates the key-value pairs emitted by the mapper. Unlike a standard word count reducer that would simply output the sum for each word, this reducer includes a cleanup method to sort the final results.

Aggregation in the reduce Method: The reduce method receives a key (a unique word) and an Iterable list of all the IntWritable values (the ones) emitted for that word from all the mappers. The reducer iterates through this list, summing the values to get the total count for the word. Instead of immediately writing the result to the output, it stores each word and its final count in an in-memory Map. This is a crucial step for the subsequent sorting.

Sorting in the cleanup Method: The cleanup method is a special function that is called exactly once after all the reduce calls have completed. This is where the in-memory map is processed. The words and their counts are converted into a List of Map.Entry objects, which is then sorted in descending order based on the word counts.

Final Output: After the list is sorted, the cleanup method iterates through it, writing each word and its corresponding count to the final output file. This ensures that the results are presented from the most frequent word to the least frequent. 

## Execution Steps 

**Part 1: Installing the Java Development Kit (JDK)** 

Maven requires Java to run, so we must install this first

**Step 1.1: Download the JDK** 

For new users, it is best to use a free, easy-to-install distribution of Java. Use
**Eclipse Adoptium (Temurin)**, which is a popular, community-supported version.

1) Go to the official download page: https://adoptium.net/temurin/releases/
2) On the download page, make the following selections:
- **Operating System**: Windows
- **Architecture**: x64
- **Package Type**: MSI (This is an installer, which is the easiest option)
- **Version**: Choose an **LTS** (Long-Term Support) version 8.
3. Click the .msi download button

**Step 1.2: Run the Installer**

1) Once the download is complete, double-click the .msi file to run the installer.
2) Proceed through the installation wizard by clicking "Next".
3) When you reach the "Custom Setup" screen, pay close attention. Click on the
small hard drive icon next to **"Set JAVA_HOME variable"** and select **"Will be
installed on local hard drive."**
4) Do the same for **"Add to PATH"**.
- **Why is this important?** This step makes the installer automatically set up
your environment variables for you, which is a huge time-saver!
5) Click "Next" and then "Install" to complete the installation. 

**Part 2: Installing Apache Maven**

Unlike the JDK, Maven does not have an installer. You simply download and unzip a
folder.

**Setp 2.1: Download Maven**

1) Go to the official Apache Maven download page:
https://maven.apache.org/download.cgi
2) Look for the "Files" section. Underneath it, find the **"Binary zip archive"** link. It
will be a file named something like apache-maven-3.9.8-bin.zip.
3) Click the link to download the .zip file.

**Step 2.2: Extract Maven**

1) Create a permanent folder for Maven. A good, clean location is C:\Program
Files\maven. Open File Explorer, navigate to C:\Program Files, and create a new
folder named maven.
2) Find the .zip file you just downloaded (e.g., apache-maven-3.9.8-bin.zip).
3) Unzip or extract the contents of this file into the C:\Program Files\maven
directory you just created. 

After extracting, your folder structure should look like this: C:\Program
Files\maven\apache-maven-3.9.8 (the version number might be diƯerent). Inside that
folder, you will see directories like bin, conf, lib, etc. 

**Part 3: Setting Up Environment Variables Manually**

If the Java installer did not set the variables for you, or to set them for Maven, follow
these steps.

**Step 3.1: Open the Environment Variables Window**

1. Press the **Windows Key** and type Edit the system environment variables.
2. Click on the matching search result.
3. In the "System Properties" window that opens, click the **"Environment
Variables..."** button. 

**Step 3.2: Create System Variables for JAVA_HOME and MAVEN_HOME**

In the bottom half of the window ("System variables"), we will create two new
variables.

1) **For JAVA_HOME**:
- Click "New...".
- **Variable name**: JAVA_HOME
- **Variable value**: C:\Program Files\Eclipse Adoptium\jdk-21.0.x.x (Browse
to or type the path to your JDK installation. The exact version number
might diƯer).
- Click OK.
2) **For MAVEN_HOME**:
Click "New...".
- **Variable name**: MAVEN_HOME
- **Variable value**: C:\Program Files\maven\apache-maven-3.9.8 (Browse to
or type the path where you extracted Maven).
- Click OK.

**Step 3.3: Add Java and Maven to the Path Variable**

The Path variable tells Windows where to find executable programs.
1) In the "System variables" section, find and select the variable named **Path**.
2) Click the "Edit..." button.
3) A new window will open showing a list of paths. Click the **"New"** button to add an
empty line.
4) Type the following exactly: %JAVA_HOME%\bin
5) Click **"New"** again to add another empty line.
6) Type the following exactly: %MAVEN_HOME%\bin
- **Why do we use %...%?** This syntax reuses the JAVA_HOME and
MAVEN_HOME variables we just created. If you ever update Java or Maven
to a latest version, you only need to change the _HOME variable, and the
Path will automatically point to the right place.
7. Click **OK** on all the windows to close and save your changes


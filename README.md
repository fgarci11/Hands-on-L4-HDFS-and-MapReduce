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




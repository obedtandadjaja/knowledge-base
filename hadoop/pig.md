# Pig

Pig is a high-level programming language useful for analyzing large data sets.

In a MapReduce framework, programs need to be translated into a series of Map and Reduce stages. However, this is not a programming model that is fast to develop on and so abstraction called Pig was built on top of Hadoop. It enables people to analyze bulk data sets and spend less time writing MapReduce programs. Pig is designed to work on any kind of data.

## Architecture

2 Components:
1. Pig Latin - language
2. Runtime Environment - for running Pig Latin

A Pig Latin program consists of a series of operations or transformations which are applied to the input data to produce output. Underneath, results of these transformations are series of MapReduce jobs which a programmer is unaware of.

![picture](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/061114_1128_INTRODUCTIO2.jpg?raw=true)

### Execution Modes

1. *Local Mode* - Pig runs in a single JVM and makes use of local file system. This mode is suitable only for analysis of small datasets
2. *MapReduce Mode* - Queries written in Pig Latin are translated into MapReduce jobs and are run on a Hadoop cluster (cluster may be psuedo or fully distributed). This mode is suitable for analysis of large datasets

## Data Model

**Bag** - collection of tuples, e.g. {(Obed, 23), ...}

**Tuple** - an ordered set of fields, e.g. (Obed, 23) - note that tuples can contain x many fields in Pig

**Field** - a piece of data, e.g. ['name'#'Obed', 'age'#'23']

**Null** - can be unknown value or a non-existent value. Used as a placeholder for optional values

## Statements

- Statements work with relations which include expressions and schemas
- Except LOAD and STORE, Pig Latin statements take a relation as input and produce another relation as output.

Load example:
```
Student_data = LOAD 'student_data.txt' USING PigStorage(',')as 
   ( id:int, firstname:chararray, lastname:chararray, phone:chararray, city:chararray );
```

## Arithmetic Operators

All the usual plus:

- **Bincond** - ternary boolean operators, e.g. a == 1 ? 'hi' : 'hello'
- **Case** - if elseif else statements

## Comparison Operators

All the usual plus:

- **matches** - Pattern matching, e.g. `x matches '.*Obed.*'`

## Construction Operators

- **Tuple constructor operator** - ()
- **Bag constructor operator** - {}
- **Map constructor operator** - []

## Relational Operators

|Operator|Description|Syntax|Example|
|--------|-----------|--------|--------|
|**Loading and Storing**|
|LOAD|To Load the data from the file system (local/HDFS) into a relation.|`Relation_name = LOAD 'file_path' USING *function* as *schema*`|`student = LOAD 'file_path' USING PigStorage(',') as (id:int, first:chararray, last:chararray);`|
|STORE|To save a relation to the file system (local/HDFS).|`STORE Relation_name INTO 'directory_path' [USING *function*]`|`STORE student INTO 'directory_path' USING PigStorage(',')`|
|**Filtering**|
|FILTER|To remove unwanted rows from a relation.|`filtered_relation = FILTER relation_name BY (conditional)`|`older_students = FILTER students BY (age > 23)`|
|DISTINCT|To remove duplicate rows from a relation.|`distinct_relation = DISTICT relation_name`||
|FOREACH, GENERATE|To generate data transformations based on columns of data.|`new = FOREACH relation_name GENERATE (columns)`|`count_per_country = FOREACH PeopleByCountry GENERATE CONCAT(country, CONCAT(':', COUNT(id)))`|
|STREAM|To transform a relation using an external program.|
|**Grouping and Joining**|
|JOIN|To join two or more relations.|`join_name = JOIN relation_name1 BY [LEFT,RIGHT,FULL OUTER] (field, ...), relation_name2 BY (field, ...)`|`customers3 = JOIN customers1 BY id, customers2 BY id`|
|COGROUP|To group the data in two or more relations.|`data = COGROUP relation_name by (field, ...), relation_name2 by (field, ...)`|`data = COGROUP student_details by age, employee_details by age`|
|GROUP|To group the data in a single relation.|`data = GROUP relation_name BY (field, ...)`|`data = GROUP student_details by age`|
|CROSS|To create the cross product of two or more relations.|`data = CROSS relation_name1, relation_name2`|`cross_data = CROSS customers, orders`|
|**Sorting**|
|ORDER|To arrange a relation in a sorted order based on one or more fields (ascending or descending).|`ordered = ORDER relation_name BY field (ASC,DESC)`||
|LIMIT|To get a limited number of tuples from a relation.|`limited = LIMIT relation_name #`||
|**Combining and Splitting**|
|UNION|To combine two or more relations into a single relation.|`union = UNION relation_name1, relation_name2`|`student = UNION student1, student2`|
|SPLIT|To split a single relation into two or more relations.|`SPLIT relation_name INTO relation_name2 IF (condition1), relation_name3 IF (condition2)`|`SPLIT student_details into older_students if (age > 23), younger_students if (age <= 23)`|
|**Diagnostic Operators**|
|DUMP|To print the contents of a relation on the console.|`DUMP relation_name`|`DUMP student`|
|DESCRIBE|To describe the schema of a relation.|`DESCRIBE relation_name`|`DESCRIBE student`|
|EXPLAIN|To view the logical, physical, or MapReduce execution plans to compute a relation.|
|ILLUSTRATE|To view the step-by-step execution of a series of statements.|

- **GROUP** - will return a tuple of value and bag, e.g. `(23, {(Obed, 23)})` - grouping people by age
- **GROUP ALL** - very similar to DISTINCT in SQL - `GROUP relation_name ALL`
- **JOIN** - when the type of join is not specified, the default will be inner join
- **CROSS** - very similar to sql regular JOIN basically coming up with all permutation between the two relations
- **UNION** - combining two datasets into one relation, question: does the two need to have matching schema?

## Aggregate Functions

**AVG()** To compute the average of the numerical values within a bag.

**BagToString()** To concatenate the elements of a bag into a string. While concatenating, we can place a delimiter between these values (optional).

**CONCAT()** To concatenate two or more expressions of same type.

**COUNT()** To get the number of elements in a bag, while counting the number of tuples in a **bag**.

**COUNT_STAR()** It is similar to the COUNT() function. It is used to get the number of **elements** in a bag.

**DIFF()** To compare two bags (fields) in a tuple.

**IsEmpty()** To check if a bag or map is empty.

**MAX()** To calculate the highest value for a column (numeric values or chararrays) in a single-column bag.

**MIN()** To get the minimum (lowest) value (numeric or chararray) for a certain column in a single-column bag.

**PluckTuple()** Using the Pig Latin PluckTuple() function, we can define a string Prefix and filter the columns in a relation that begin with the given prefix.

**SIZE()** To compute the number of elements based on any Pig data type.

**SUBTRACT()** To subtract two bags. It takes two bags as inputs and returns a bag which contains the tuples of the first bag that are not in the second bag.

**SUM()** To get the total of the numeric values of a column in a single-column bag.

**TOKENIZE()** To split a string (which contains a group of words) in a single tuple and return a bag which contains the output of the split operation.

## Bag & Tuple Functions

**TOBAG()** To convert two or more expressions into a bag.

**TOP()** To get the top N tuples of a relation.

**TOTUPLE()** To convert one or more expressions into a tuple.

**TOMAP()** To convert the key-value pairs into a Map.

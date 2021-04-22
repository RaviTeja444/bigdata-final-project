# Ravi Teja Pagidoju

# Big Data Final Project.
<table>
<td align="center"><img src="https://github.com/RaviTeja444/bigdata-final-project/blob/main/IMG_0189.jpg" width="100px;" alt=""/><br /><sub><b>Ravi Teja Pagidoju</b></sub></a><br /></td></table>

# Requiremnets.
- Python.
- Databricks Community Edition.
- Databricks PySpark Api
- Spark Processing Engine

# About Data:
- I selected The Gutenberg eBook of Adventures of Sherlock Holmes, which is a free to use.
- This text contains words,helping words like is, the , and , etc.
- [Link to the File.](http://www.gutenberg.org/files/1661/1661-0.txt)

# Commands Used:
#### Importing content from the selected url.
```import urllib.request as data
data.urlretrieve("http://www.gutenberg.org/files/1661/1661-0.txt", "/tmp/ravi_data.txt")
dbutils.fs.mv("file:/tmp/ravi_data.txt","dbfs:/data/ravi_data.txt")
```
 - We use urllib library to get data from the webpage to the notebook and stored in the tmp folder as ravi_data.txt.
 - Moving the data from tmp to dbfs.
 
 #### Filtering the Content.
 ```python
rv_RDD=sc.textFile("dbfs:/data/ravi_data.txt",5)
```
- converting the data into RDD for spark
 ```python
# Spliting and Converting all the data into Lower Case.
rvWordsRDD=rv_RDD.flatMap(lambda line:line.lower().strip().split(" "))
```
- The entire data from source is splitted by space

 ```python
# To remove Stop words.
from pyspark.ml.feature import StopWordsRemover as remover
rem =remover()
sWords = rem.getStopWords()
filterData=rvWordsRDD.filter(lambda w: w not in sWords)
```
- The data can still contain stop words.
- To filter words we import stopWordsRemover function from pyspark.ml.feature.
- Filtering data from those stop words.

```python
# To remove punctutations.
import re
finalData = filterData.map(lambda wrds: re.sub(r'[^a-zA-Z]','',wrds))

# To eliminate Space.
rvPairsRDD = finalData.filter(lambda y: y != "")
 ```
 - It may also has blank spaces.
 - We are removing them using a library.
 
 ```python
 # Setting Map for the words with the number of repetitions.
rvRDDpairs= rvPairsRDD.map(lambda word:(word,1))
resultRDD=rvRDDpairs.reduceByKey(lambda acc,value:acc+value)
myResult = resultRDD.map(lambda x:(x[1], x[0])).sortByKey(False).take(25)
```
- Mapping all words with its count(incremented on repitition)
```python
print(myResult);
```
- Print the result to view the top 10 words.
```python 
import numpy as nmp
import pandas as pnd
import matplotlib.pyplot as plt
import seaborn as sns
from collections import Counter

# preparing chart information
source = 'The Project Gutenberg eBook of Sherlock Holmes Adventures'
title = 'Top Words in ' + source
xlabel = 'word'
ylabel = 'count'
```
- import plot libraries.
- Creation of chart based on above values.
```python
# create Pandas dataframe from list of tuples
dframe = pnd.DataFrame.from_records(myResult, columns =[xlabel, ylabel]) 
print(dframe);

# create plot (using matplotlib)
plt.figure(figsize=(15,10))
sns.barplot(xlabel, ylabel, data=dframe, palette="Paired").set_title(title)
```
- Create the dataframe by using the above values.
- Using matplotlib to plot a graph.
#### Displaying Graph of the Project.
- ![Graph](https://github.com/RaviTeja444/bigdata-final-project/blob/main/chart.png)
## Refrences:
- [Spliting of Data](https://campus.datacamp.com/courses/big-data-fundamentals-with-pyspark/programming-in-pyspark-rdds?ex=14)
- [Removing spaces](https://stackoverflow.com/questions/3232953/python-removing-spaces-from-list-objects/39668144)
- [Dr. Denise Case](https://github.com/denisecase/starting-spark)

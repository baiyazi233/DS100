# 1 Introduction

## 1.1 Data Science Lifecycle

![alt text](./img/Data%20Science%20Lifecycle.png)

### 1.1.1 Ask a Question

问问题的关键在于清楚

- What do we want to know?
  - A question that is too ambiguous may lead to confusion.
- What problems are we trying to solve?
  - The goal of asking a question should be clear in order to justify your efforts to stakeholders.
- What are the hypotheses we want to test?
  - This gives a clear perspective from which to analyze final results.
- What are the metrics for our success?
  - This establishes a clear point to know when to conclude the project.

### 1.1.2 Obtain Data

注意数据单位，数据是否有代表性

- What data do we have, and what data do we need?
  - Define the units of the data (people, cities, points in time, etc.) and what features to measure.
- How will we sample more data?
  - Scrape the web, collect manually, run experiments, etc.
- Is our data representative of the population we want to study?
  - If our data is not representative of our population of interest, then we can come to incorrect conclusions.

Key procedures: *data acquisition*, *data cleaning*

### 1.1.3 Understand the Data

理解数据对数据进行处理

- How is our data organized, and what does it contain?
  - Knowing what the data says about the world helps us better understand the world.
- Do we have relevant data?
  - If the data we have collected is not useful to the question at hand, then we must collect more data.
- What are the biases, anomalies, or other issues with the data?
  - These can lead to many false conclusions if ignored, so data scientists must always be aware of these issues.
- How do we transform the data to enable effective analysis?
  - Data is not always easy to interpret at first glance, so a data scientist should strive to reveal the hidden insights.

Key procedures: *exploratory data analysis*, *data visualization*.

### 1.1.4 Understand the World

- What does the data say about the world?
  - Given our models, the data will lead us to certain conclusions about the real world.
- Does it answer our questions or accurately solve the problem?
  - If our model and data can not accomplish our goals, then we must reform our question, model, or both.
- How robust are our conclusions and can we trust the predictions?
  - Inaccurate models can lead to false conclusions.

Key procedures: *model creation*, *prediction*, *inference*.

# 2 Pandas I

## 2.2 `Series`, `DataFrame`s, and Indices

pandas中有三种基本的数据结构：

1.**`Series`**: 1D labeled array data; best thought of as columnar data.

2.**`DataFrame`**: 2D tabular data with rows and columns.

3.**`Index`**: A sequence of row/column labels.

![alt text](./img/fundamental%20data%20structures.png)

### 2.2.1 Series

Series表示DataFrame的一列;更一般地说，它可以是任何一维的类似数组的对象。它包含：

1.相同类型的值的序列

2.称为索引的数据标签序列

```python
s = pd.Series(["welcome", "to", "data 100"])

# Accessing data values within the Series
s.values

# Accessing the Index of the Series
s.index

# 默认情况下，Series的索引是从0开始的整数的顺序列表
# 可以将手动指定的所需索引列表传递给index参数
s = pd.Series([-1, 10, 2], index = ["a", "b", "c"])

# 索引也可以在初始化后更改
s.index = ["first", "second", "third"]
```

#### 2.2.1.1 Selection in `Series`

```python
s = pd.Series([4, -2, 0, 6], index = ["a", "b", "c", "d"])

# A Single Label
# We return the value stored at the index label "a"
s["a"] 

# A List of Labels
# We return a Series of the values stored at the index labels "a" and "c"
s[["a", "c"]] 

# A Filtering Condition
s[s > 0] 
```

### 2.2.2 `DataFrames`

我们可以将DataFrame视为所有共享同一索引的Series的集合

#### 2.2.2.1 Creating a `DataFrame`

```python
# More generally, the syntax for creating a DataFrame is:
pandas.DataFrame(data, index, columns)

# 1.From a CSV file
elections = pd.read_csv("data/elections.csv")

# 2. Using a List and Column Name(s)
df_list = pd.DataFrame([1, 2, 3], columns=["Numbers"])

df_list = pd.DataFrame([[1, "one"], [2, "two"]], columns = ["Number", "Description"])

# 3.From a Dictionary
df_dict = pd.DataFrame({
    "Fruit": ["Strawberry", "Orange"], 
    "Price": [5.49, 3.99]
})

df_dict = pd.DataFrame(
    [
        {"Fruit":"Strawberry", "Price":5.49}, 
        {"Fruit": "Orange", "Price":3.99}
    ]
)

# 4.From a Series
# Notice how our indices, or row labels, are the same
s_a = pd.Series(["a1", "a2", "a3"], index = ["r1", "r2", "r3"])
s_b = pd.Series(["b1", "b2", "b3"], index = ["r1", "r2", "r3"])

pd.DataFrame(s_a)

s_b.to_frame()

# To merge the two Series and specify their column names
pd.DataFrame({
    "A-column": s_a, 
    "B-column": s_b
})
```

### 2.2.3 Indices

```python
# 从技术角度来说，索引不必是整数，也不必是唯一的
# Creating a DataFrame from a CSV file and specifying the index column
elections = pd.read_csv("data/elections.csv", index_col = "Candidate")

# 我们还可以选择一个新列并将其设置为DataFrame的索引
elections.reset_index(inplace = True) # Resetting the index so we can set it again
# This sets the index to the "Party" column
elections.set_index("Party")

# 我们可以将索引恢复为默认的整数列表
# This resets the index to be the default list of integer
elections.reset_index(inplace=True) 

```

## 2.3 `DataFrame` Attributes: Index, Columns, and Shape

```python
# For index/row labels, use DataFrame.index
elections.set_index("Party", inplace = True)

# For column labels, use DataFrame.columns:
elections.columns

# 对于DataFrame的形状，我们可以使用DataFrame.shape来获取行数和列数
elections.shape
```

## 2.4 Slicing in `DataFrame`s

We will do so with four primary methods of the `DataFrame` class:

1. `.head` and `.tail`
2. `.loc`
3. `.iloc`
4. `[]`

### 2.4.1 Extracting data with `.head` and `.tail`

```python
# Extract the first 5 rows of the DataFrame
elections.head(5)

# Extract the last 5 rows of the DataFrame
elections.tail(5)
```

### 2.4.2 Label-based Extraction: Indexing with `.loc`

要使用.loc抓取数据，我们必须指定数据所在的行和列标签。行标签是.loc函数的第一个参数;列标签是第二个参数

Arguments to `.loc` can be:

- A single value.
- A slice.
- A list.

```python
elections.loc[0, 'Candidate']

# 传入一个参数作为单个值将产生一个Series
elections.loc[[87, 25, 179], "Popular vote"]

# 如果我们将“Popular vote”作为列表传递，则输出将是DataFrame
elections.loc[[87, 25, 179], ["Popular vote"]]

# 要选择多个行和列，我们可以使用Python切片表示法
# 与Python切片不同，.loc包含右上界
elections.loc[0:3, 'Year':'Popular vote']

# 我们要提取前四行的所有列值使用:
elections.loc[0:3, :]
```

### 2.4.3 Integer-based Extraction: Indexing with `.iloc`

使用.iloc进行切片的工作原理与. loc类似。但是，.iloc使用行和列的索引位置，而不是标签

切片在.iloc中不再是包容性的-它是排他性的。换句话说，使用. iloc时不包括切片的右端

### 2.4.4 Context-dependent Extraction: Indexing with

```python
# A slice of row numbers
# 前四行
elections[0:4]

# A slice of row numbers
# 前四列
elections[["Year", "Candidate", "Party", "Popular vote"]]

# A single-column label
elections["Candidate"]
```




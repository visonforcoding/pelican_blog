Title: Quick tips for using Pandas with MongoDB
Date: 2017-07-30 10:32
Category: python

# 数据存储在MongoDB，如何对它们进行分析？

*这篇文章将会细讲以下两个方面:*

1.如何更高效地将数组数据从MongoDB导入到Pandas

2.利用MongoDB的查询和聚合过滤出你所需要的数据到Pandas非常的重要和有用。

## 对与初学者：什么是pandas

![pandas](images/2017-07-30-10-43-37.png)

从[它们的文档](http://pandas.pydata.org/pandas-docs/stable/)来看:

Pandas是一个提供快速、灵活、生动的数据结构设计使得处理"relational"和"labeled"数据都能容易和直观的python包。

它旨在成为在Python中进行实际的现实世界数据分析的基础高层次构建模块。此外，它有更广泛的目标，成为任何语言提供的最强大和最灵活的开源数据分析/操纵工具。它已经很好地实现了这一目标。

## pandas 很适合许多不同种类的数据：

- 具有异类型列的表格数据，如SQL表或Excel电子表格中所示
- 有序和无序（不一定是固定频率）的时间序列数据。
- 具有行和列标签的任意矩阵数据（均匀类型或异构）
- 任何其他形式的观察/统计数据集。数据实际上根本不需要被标记为放在熊猫数据结构中

## Pandas 与 R 的异同

*这个话题上互联网上有很多材料，但是这里有一些更好的总结:*

- pandas 和R都用于统计和数字分析，包括导入数据和绘图的能力。

- pandas和R都以称为dataframe的二维空间为基础对象。

- R 是具有自己的语言和模块生态系统的自己的环境。

- pandas 是您导入python的模块，所以语言是python，生态系统是python。

- R 有一个更广泛的本机统计功能。

- pandas 从python面向对象的环境中获益，还可以轻松集成更多的“对等”模块和功能

- python和r都有MongoDB的驱动程序/ API

## 如何从MongoDB获取数据到Pandas

这是“经典”5行，将产生一个dataframe。没有参数的find(）本质上是select * from myCollection;所有的字段，没有过滤。 python原生函数list(）用于调用find()返回的游标（一个可迭代的），直到cursor完成：

```python
  import pandas as pd
  from pymongo import MongoClient
  client = MongoClient()
  db = client.sourceDatabase
  df = pd.DataFrame(list(db.myCollection.find()))
```

pandas 在摄取数据方面非常灵活，而MongoDB python驱动程序将产生丰富的类型（即真实日期，真正的数组等，而不是字符串表示或不透明的blob），所以几乎所有在myCollection中的任何东西都将被削弱数据帧。所以在行动中，5-Liner运行这个数据：

```shell
  db.myCollection.insert（{“a”：1，“b”：2，“c”：3}）
  db.myCollection.insert（{“a”：4，“b”：5，“c”：6}）
```

将产生此输出：

```shell
                          _id    a    b    c
  0  5804e627119c0d2c13c0a7e1  1.0  2.0  3.0
  1  5804e627119c0d2c13c0a7e2  4.0  5.0  6.0
```

*请注意，特殊的MongoDB唯一文档标识符_id也将被带入框架*,从返回的数据集中可以看到简单的技术;我们稍后会探讨一下。如前所述，pandas将正确地遏制复杂的数据，包括数组中的混合类型（以下示例中第二个插入中的字段f）：

```shell
  db.myCollection.insert({"a":"hello", "b": datetime.datetime.now(), "c": {"d":"AA","e":"BB"}, "f": [2,3,4]})

  db.myCollection.insert({"a":"goodbye", "c": {"e":"WW"}, "f": [0, "grimble", datetime.datetime.now(), 7.7 ]})
```

```shell
                          _id        a                       b
  0  5804e758119c0d2c13c0a7e5    hello 2016-10-17 14:59:36.029
  1  5804e758119c0d2c13c0a7e6  goodbye

                              c                                         f
  0  {u'e': u'BB', u'd': u'AA'}                             [2.0, 3.0, 4.0]
  1               {u'e': u'WW'}  [0.0, grimble, 2016-10-17 14:59:36.107000, 7.7]
```

>虽然5-Liner强调了易于集成，但它回避了一个重要的问题：如何最好地处理MongoDB中的数组。

## pandas和dataframe 创建的一些背景

仅仅支持一点点，大多数pandas的用例围绕创建数字矩阵，通常以数字列的形式提供。数组的数组真的是最容易构造的，并且很好地驱动动态数据帧创建（即数组的长度驱动帧大小），尽管像所有基于整数偏移的寻址一样，更大的数据集或缺少的值可能会有点麻烦来处理与（如CSV文件）：

```python
  values = [
      [ 4, 5, 6, 7],
      [ 7, 8, 9, 10],
      [ 10, 11, 12]
  ]
  print pd.DataFrame(values)
```

```shell
      0   1   2     3
  0   4   5   6   7.0
  1   7   8   9  10.0
  2  10  11  12   NaN
```

Arrays of key:简单标量的值结构使得field管理变得更容易，实际上是传统RDBMS平台（考虑到ResultSet的游标）的主要表现形式：

```python
  values = [
      { "a":4, "b":5, "c":6, "d":7},
      { "a":7, "b":8, "c":9, "d":10},
      { "b":11, "c":12 }
  ]
  print pd.DataFrame(values)
```

```shell
       a   b   c     d
  0  4.0   5   6   7.0
  1  7.0   8   9  10.0
  2  NaN  11  12   NaN
```

Combining key:value是数组，但是，产生一个有趣的结果:

```python
  values = [
      { "a": [ 4,5,6,7]},
      { "a": [ 8,9,10] },
      { "a": [ 11, 12] }
  ]
  print pd.DataFrame(values)
```

```shell
                a
  0  [4, 5, 6, 7]
  1    [8, 9, 10]
  2      [11, 12]
```

数组不会动态地驱动列数。 相反，只有一列存在类型数组！ 这通常不是我们寻求的结构。
在MongoDB中，存储数组数组通常是非常有利的。 但是5-Liner中方法遇到了同样的问题：

```python

data = [
      { "a": [ 4,5,6,7]},
      { "a": [ 8,9,10] },
      { "a": [ 11, 12]
      ]
  db.myCollection.insert(data)

  print pd.DataFrame(list(db.myCollection.find()))
```

```shell
                          _id                     a
  0  58056115119c0d2c13c0a7e9  [4.0, 5.0, 6.0, 7.0]
  1  58056115119c0d2c13c0a7ea      [8.0, 9.0, 10.0]
  2  58056115119c0d2c13c0a7eb          [11.0, 12.0]
```

## 从MongoDB优化数组的消耗

最大化将数据从MongoDB数组移动到熊猫动态列的实用性的技术很简单：而不是在find（）上调用list（），迭代游标并构建至少一个值列表（这将是一个数组列表 ），可选的一个用于列标签，另一个用于索引。 开始简单（没有list推导）：

```python
  values = []
  for cc in db.myCollection.find():
      values.append(cc['a'])

  print pd.DataFrame(values)
```

```shell
        0     1     2    3
  0   4.0   5.0   6.0  7.0
  1   8.0   9.0  10.0  NaN
  2  11.0  12.0   NaN  NaN
```

注意它不再只是列“a”; 列由数组a的大小动态驱动。 另外，因为我们正在显式地构建值数组（同样是一个数组列表），所以_id不会被拖入，pandas会自动将NaN分配给短数组。
每个文档通常都会具有与值数组相关联的某种标签。 我们补充说，作为字段n：

```python
  data = [
      { "a": [ 4,5,6,7], "n": "foo"},
      { "a": [ 8,9,10] , "n": "bar"},
      { "a": [ 11, 12] , "n": "baz"}
  ]
  db.myCollection.insert(data)
  values = []
  seriesLbls = []
  for cc in db.myCollection.find():
      values.append(cc['a'])
      seriesLbls.append(cc['n'])

  print pd.DataFrame(values, index=seriesLbls)
```

```shell
          0     1     2    3
  foo   4.0   5.0   6.0  7.0
  bar   8.0   9.0  10.0  NaN
  baz  11.0  12.0   NaN  NaN
```

请注意，0,1,2已被foo，bar和baz替代。 应该清楚的是，如果需要，可以使用cc ['_ id']，而不是使用cc ['n']创建索引。 在for循环中构建多个数组比使用列表推导更加清晰。
数据也从MongoDB流入大熊猫用作索引。 我们为每个记录添加一个日期，并使用它而不是名称:

```python

  data = [
      { "a": [ 4,5,6,7], "n": "foo", "d": datetime.datetime(2016,7,1) },
      { "a": [ 8,9,10] , "n": "bar", "d": datetime.datetime(2016,8,1) },
      { "a": [ 11, 12] , "n": "baz", "d": datetime.datetime(2016,9,1) }
  ]
  db.myCollection.insert(data)

  values = []
  dates = []
  for cc in db.myCollection.find():
      values.append(cc['a'])
      dates.append(cc['d'])

  didx = pd.DatetimeIndex(dates)  # not strictly necessary
  print pd.DataFrame(values, index=didx)
```

```shell
                 0     1     2    3
  2016-07-01   4.0   5.0   6.0  7.0
  2016-08-01   8.0   9.0  10.0  NaN
  2016-09-01  11.0  12.0   NaN  NaN
```

dataframe构造函数将接受datetime对象的常规数组作为索引的值，但是为了完整性，我们将显示一个DatetimeIndex对象的显式构造。
最后，有几种方法可以制作列标签，使dataframe不错，完整。 在这里，我们只需要查找最长的数组来驱动列名C0到Cn的生成（通过list comprehension）：

```python
  values = []
  seriesLbls = []
  max = 0
  for cc in db.myCollection.find():
      if len(cc['a']) > max:
          max = len(cc['a'])
      values.append(cc['a'])
      seriesLbls.append(cc['n'])

  df = pd.DataFrame(values, index=seriesLbls, columns=[ "C"+str(n) for n in range(max)])

  print df
  print df.T
```

```shell
         C0    C1    C2   C3
  foo   4.0   5.0   6.0  7.0
  bar   8.0   9.0  10.0  NaN
  baz  11.0  12.0   NaN  NaN

      foo   bar   baz
  C0  4.0   8.0  11.0
  C1  5.0   9.0  12.0
  C2  6.0  10.0   NaN
  C3  7.0   NaN   NaN
```

我们在这里显示标称和转置（df.T）dataframe。
矩阵

在dataframe之外，矩阵通常被实现为数组。 这个实现可以轻松地转换为MongoDB。 在这里，我们正在存储两个文档，每个文档具有3x3矩阵。 这是与上述示例略有不同的数据设计，因为矩阵是一个文档中的单个字段，而不是每个行作为2个或更多文档中的数组承载：

```python
  values = [
        { "name":"A", "v": [ [1,2,3], [4,5,6], [7,8,9] ] }
        ,{ "name":"B", "v": [ [2,3,4], [5,6,4], [8,9,10] ] }
  ]

  db.myCollection.drop()
  db.myCollection.insert(values)
```

与常规向量一样，只需将MongoDB文档拖放到框架中即可产生您可能不想要的表示。

```python
  print pd.DataFrame(list(db.myCollection.find()))
```

```shell
                          _id name                                       v
  0  587115a8ed58db9467d94d34    A       [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
  1  587115a8ed58db9467d94d35    B      [[2, 3, 4], [5, 6, 4], [8, 9, 10]]
```

幸运的是，pandas适用于常规数组，因此迭代地添加简单数组将产生我们寻求的3x3结构。 在下面的例子中，我们使用find_one方法只返回一个名为“B”的文档：

```python
  values = []
  item = db.myCollection.find_one({"name":"B"})
  for row in item['v']:   # item['v'] is array of array, so row is array
      values.append(row)
  print pd.DataFrame(values)
```

```shell
     0  1   2
  0  2  3   4
  1  5  6   4
  2  8  9  10
```

## 不要忘记MongoDB的力量

开发人员和分析师经常采取一种将数据拖出数据库（MongoDB或其他方式）并加载一个非常大的数据帧的方法 - 即使它们只需要一小部分数据。很多时候这是因为：
过滤或聚合功能需要在阵列上工作，熊猫环境提供数据提供者中不存在的功能
大熊猫环境更加优越或更方便
以上所有
但是很明显，非常希望不必在网络上移动不必要的数据，并尽可能地创建大型数据帧。
上面的例子集中在将数据从MongoDB有效地移动到数据帧中，但没有探索过滤和聚合。下面是一个强大的MongoDB聚合框架如何被带入的例子。这些过滤命令在服务器端以索引优化的方式执行，大大减少了填充数据帧的材料数量，并提高了性能：

```python
  values = []
  seriesLbls = []
  max = 0

  for cc in db.myCollection.aggregate([
  #  First, only get things after 2016-07-15.  Typically this would be on an 
  #  indexed field and will rapidly cut down the material to a fraction
  #  of what might be stored in the DB.  "First cut" filtering on dates, 
  #  portfolios, owners, compute run ids, etc. is a very important and useful 
  #  capability.
  #
  {"$match": {"d": {"$gt": datetime.datetime(2016,7,15)}}}
  #  Next, compute stdDevPop of the array.  MongoDB offers powerful array
  #  handling functions:
  ,{"$addFields": {"sdev": {"$stdDevPop": "$a"}}}

  #  Next, only permit those items where stdDevPop is <.75 to come through:
  ,{"$match": {"sdev": {"$lt": 0.75}}}
  ]):
      if len(cc['a']) > max:
          max = len(cc['a'])
      values.append(cc['a'])
      seriesLbls.append(cc['n'])
  df = pd.DataFrame(values, index=seriesLbls, columns=[ "C"+str(n) for n in range(max)])
  print df
```

```shell
       C0  C1
  baz  11  12
```

如果您注释掉最后一个match，您将看到两个项目将流过dataframe。 第三个仍然不存在，因为它被日期的第一个$match表达式滤除。.

```python
  #  Next, only permit those items where stdDevPop is <.75 to come through:
  # ,{"$match": {"sdev": {"$lt": 0.75}}}   # commented out
```

```shell
       C0  C1    C2
  bar   8   9  10.0
  baz  11  12   NaN
```

有时您不会将数据组织到数组中，而是会发现跨文档分散的值。 不要更pandas/数据框逻辑，而是使用MongoDB来构建数组。 我们将使用上面的数据将其分解成单个文档，使用标量字段v来承载（原始）数组的每个元素来降低这种情况。 然后，我们将使用$group阶段将其重新组合起来，构建一个数组，并将结果传递给与之前相同的代码逻辑：

```python

data = [
      { "v": 4, "n": "foo", "d": datetime.datetime(2016,7,1) },
      { "v": 5, "n": "foo", "d": datetime.datetime(2016,7,1) },
      { "v": 6, "n": "foo", "d": datetime.datetime(2016,7,1) },
      { "v": 7, "n": "foo", "d": datetime.datetime(2016,7,1) },
      { "v": 8, "n": "bar", "d": datetime.datetime(2016,8,1) },
      { "v": 9, "n": "bar", "d": datetime.datetime(2016,8,1) },
      { "v": 10, "n": "bar", "d": datetime.datetime(2016,8,1) },
      { "v": 11, "n": "baz", "d": datetime.datetime(2016,9,1) },
      { "v": 12, "n": "baz", "d": datetime.datetime(2016,9,1) }
]
db.myCollection.insert(data)

values = []
seriesLbls = []
max = 0
for cc in db.myCollection.aggregate([
{"$group": {"_id": "$n", "a": {"$push": "$v"}}}
]):
    print cc
    if len(cc['a']) > max:
        max = len(cc['a'])
    values.append(cc['a'])
    seriesLbls.append(cc['_id'])

print pd.DataFrame(values, index=seriesLbls, columns=[ "C"+str(n) for n in range(max)])
```

```shell
     C0  C1    C2   C3
baz  11  12   NaN  NaN
bar   8   9  10.0  NaN
foo   4   5   6.0  7.0
```

上面的粗体代码将从每个文档中的单个值v创建一个数组，按_id分组。
当然，我们可以从$ group操作符之前的前一个示例中添加过滤器，以减少传递给$ group的资料量，稍后再对$ stdDevPop进行过滤：

```python
  for cc in db.myCollection.aggregate([
  {"$match": {"d": {"$gt": datetime.datetime(2016,7,15)}}}
  ,{"$group": {"_id": "$n", "a": {"$push": "$v"}}}
  ,{"$addFields": {"sdev": {"$stdDevPop": "$a"}}}
  ,{"$match": {"sdev": {"$lt": 0.75}}}
  ]):
```

一般来说，策略应该先过滤，然后进行操作。
处理非常大的数据集时的最后提示

5-Liner的简单性隐藏了一个潜在的问题，数百万或数十亿的文档从数据库中拉出。 list（）运算符将导致创建一个巨大的数据结构，只能作为临时构造函数传递给DataFrame，然后构建基本上相同的巨大数据结构：

```python
  df = pd.DataFrame(list(db.myCollection.find()))
```

有许多方法来避免这个问题，但是大多数方法围绕从小组输入记录创建小帧，然后在最后连接帧。 这是一个来自stackoverflow的例子：

```python
def iterator2dataframes(iterator,chunk_size:int):
  """Turn an iterator into multiple small pandas.DataFrame
  This is a balance between memory and efficiency
  """
  records = []
  frames = []
  for i, record in enumerate(iterator):
    records.append(record)
    if i % chunk_size == chunk_size - 1:
      frames.append(pd.DataFrame(records))
      records = []
  if records:
    frames.append(pd.DataFrame(records))
  return pd.concat(frames)

df = iterator2dataframe(db.myCollection.find(), 10000)
```

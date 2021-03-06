---
title : 分类-clustering
---


## 无督导学习

前面介绍的几种机器学习算法都是监督机器的学习,在已知目标变量或标签的位置,我们试图预测基于输入特性的输出。
无监督学习是不同的一种没有标记数据的感觉,我们不会试图预测任何输出因此,但试图找到有趣的模式,并提出
数据中的组,类似的值被组合在一起。

**主要的方法**

* 量化观察对象,使用一个向量进行表示,如可以用年龄，身高，体重表示一个人     

|年龄|薪水|体重|身高|     
|----|-----|-----|-----| 
|30|15k|62|172|
|40|25k|75|170|

* 计算两个对象间的距离,通过用欧几里德法来测量距离,直截了当

![](分类-Clustering/distance.png)

=√((30-40)² + (15-25)² + (62-75)² + (172-170)²)
=√(100+100+169+4)
=19.3

还要其他计算距离的算法： Manhattan 距离 ； Mahalanobis距离 ；Chebyshev 距离等。

计算得到上述两个对象间的距离为19.3

## K-Means算法

K代表数据集中划分类别的个数,现在对K-Menas算法中的一些概念的说明。

* Centroid-图心   

图心指的是中心的数据点,在一个集群的中心或一个组。它也是集群中最具有代表性的点,最大距离等距点到集群中的其他点。
每个集群或组包含不同数量的数据点到图心的质心最近

![数据集划分的不同的图心](分类-Clustering/centroid.png)

聚类的整个想法是最小化内光距离,这是集群的图心的数据点的内部距离最大限度的相互聚系距离,即两个之间的图心之间不同分类

* Variance-方差
方差是图心所在数据集中的数据点距离的总和。

![variance-方差](分类-Clustering/var.png)


## 示例代码

用的测试数据是 iris data(鸢尾科话)数据，由于这份数据在统计计算测试中经常用到，所以在网络上搜索下，就可以找到。


~~~python
from pyspark.sql import SparkSession
import pyspark

from pyspark.sql.functions import * 
from pyspark.sql.types import *
from pyspark.sql.functions import rand, randn
from pyspark.ml.clustering import KMeans

spark = SparkSession.builder.appName('k_means').getOrCreate()  # 创建SparkSession对象

print("------------------读取数据-----------------")

df=spark.read.csv('iris_dataset.csv',inferSchema=True,header=True)  # 读取数据

print("------------------查看分析数据-----------------")

print((df.count(),len(df.columns)))         # 查看数据规模

# 查看列信息
df.printSchema()
df.columns            

df.orderBy(rand()).show(10,False)           # 查看数据，随机的方式

df.groupBy('species').count().orderBy('count',ascending=False).show(10,False) # 汇总查看数据

print("-----------------数据转换-------------------")

from pyspark.ml.linalg import Vectors
from pyspark.ml.feature import VectorAssembler    # 导入VerctorAssembler 将多个列合并成向量列的特征转换器,即将表中各列用一个类似list表示，输出预测列为单独一列。


input_cols=['sepal_length', 'sepal_width', 'petal_length', 'petal_width']

# 将所有的属性转换为转化为一个vector
vec_assembler = VectorAssembler(inputCols = input_cols, outputCol='features')
final_data = vec_assembler.transform(df)

print("------------设定不同的K值，进行分类,计算平方误差之和------------")

errors=[]

for k in range(2,10):
    kmeans = KMeans(featuresCol='features',k=k)
    model = kmeans.fit(final_data)
    intra_distance = model.computeCost(final_data)
    errors.append(intra_distance)
    print("With K={}".format(k))
    print("Within Set Sum of Squared Errors = " + str(errors))
    print('--'*30)

print("-----------使用mathplot计算，汇总不同K值-----------------")

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

cluster_number = range(2,10)
plt.scatter(cluster_number,errors)
plt.xlabel('Number of Clusters (K)')
plt.ylabel('SSE')
plt.show()

# 通过图形，查看k=4时较为合适
kmeans = KMeans(featuresCol='features',k=4,)
model = kmeans.fit(final_data)

predictions=model.transform(final_data)

predictions.groupBy('species','prediction').count().show()      # 查看分类的数据

print("---------将数据转换为panda结构，并查看空间3d图心-----------")

pandas_df = predictions.toPandas()
pandas_df.sample(5)

import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D

cluster_vis = plt.figure(figsize=(15,10)).gca(projection='3d')
cluster_vis.scatter(pandas_df.sepal_length, pandas_df.sepal_width, pandas_df.petal_length, c=pandas_df.prediction,depthshade=False)
plt.show()
~~~


![figure1](分类-Clustering/Figure_1.png)
![figure2](分类-Clustering/Figure_2.png)

![part1](分类-Clustering/part1.png)
![part2](分类-Clustering/part2.png)
![part3](分类-Clustering/part3.png)



---
title: cs61a-proj02
date: 2018-09-05 22:05:10
categories:
- CS61A
tags:
- python
- SICP
---

# Yelp Maps

## 简介

- 目的：运用机器学习和Yelp学术数据集创建餐馆评分可视化界面
- 描述：Berkeley被划分成不同的区域，每个区域由最近的餐厅的预测评分被描画出不同颜色的阴影，黄色是5星，蓝色是一星。每一个点代表一个餐厅，并且其颜色由所在位置决定。比如：市中心的餐厅是绿色的。
<!-- more -->
## 阶段0: 工具函数

- 问题0.1：用list comprehensions并且只用一行代码实现map_and_filter函数，接受序列s，一个参数的函数map_fn，一个参数的函数filter_fn，返回一个新list包含filter为true并且调用map_fn在每一个参数上

- 问题0.2：用min函数实现key_of_min_value,接受一个字典，返回一个key，这个key对应value的最小值。

- 问题0.3：用zip函数实现enumerate，接受一个序列s和一个开始索引，返回一个pairs的list，第i个元素是i+start和s的第i个元素。

- 问题1:实现mean函数求序列平均数，断言序列不为空，接受一个序列返回其平均数

## 阶段1：数据抽象

- 问题2：为餐厅构造器和选择器实现数据抽象，review和user已实现。

- 结构剖析：整个项目由三部分数据抽象组成，分别是User，Review和Restaurant，三种数据抽象都有对应的make_xxx来实例化一个新实例，接下来分别分析这三部分

- Review这个抽象底层由list实现，并且review通过两个参数新生成一个实例，分别是餐厅的名字和评分，数据类型对应str和float，并有与之对应的选择器选择其成员即restaurant_name和rating

- User这个抽象底层依然由list实现，但是其接受两个参数，分别是用户名name以及一个对应的reviews列表，这个reviews列表由一系列的上述review组合而成，而User的实现就是将这些个reviews用list comprehension的方式在底层转为字典，以餐厅名字：review实例一个个键值对的方式将其存储在列表的第一项中。而列表实现的第0项就是用户名，然后存在与之对应的选择器去实现user_name和user_reviews的获取。

- 接下来就是两个构建在数据抽象层之上的方法，此时不考虑底层User和Restaurant的实现。分别实现了user_reviewed_restaurants方法和user_rating方法，前者接受user和一个restaurant的列表，并返回一个被该用户review过的restaurant列表；后者接受user和一个restaurant_name，并返回与之对应的用户评分

- 接下来就是要实现的Restaurant抽象，一个Restaurant实例由name, location, categories, price, reviews五大部分组成，我选择底层依然采用列表实现，与之对应的五项就是上述五项。前面四项的选择器直接用list的索引选择即可，最后一个部分需要用list comprehension选择review数据的评分，并以list的形式返回。

## 阶段2：非监督学习

- 简介：餐厅表现出以集群分布，我们要设计一种方式将临近的餐厅组合起来。
- 描述：k-means算法是一种发现集群中心的算法，之所以称之为非监督学习，是因为我们不会告诉该算法正确的集群是什么，而是让算法自己从数据集中推测出集群。k-means算法会在一个数据集中寻找到k个重心点，每一个都对应一个输入样本的集群。为了实现这个，k-means算法开始随机选中k个重心点，然后交替执行一下两步骤：1. 将餐厅分组成集群，在每一个集群中包含所有临近于同一个重心点的餐馆；2.对每一个集群重新计算一个新的重心点。

- 问题3：实现find_closest，接受一个location（一个经纬度的对，由两元素的sequence实现，第一个是维度，第二个是经度）和一个重心序列（也表示为经纬度对），返回最接近location的重心元素，如果两距离相等，返回第一个重心（centroid）。提示可用min函数实现，只需要一行代码！

- 问题4：实现group_by_centroid，接受一个restaurants序列和重心序列，返回一个集群列表。每一个集群列表中返回的集群都是一个餐厅列表，并且这些餐厅都近于一个重心序列中的重心点。提示用group_by_first函数group相同的key在[key, value]这样的列表中。结合提示和上述已经实现的find_closest函数，我们能够利用list comprehension构造一个列表，这个列表的每一项都是一个列表，并且第一项是重心坐标，第二项是抽象的restaurant实例，这样结合group_by_first就可以实现该需求。

- 问题5：实现find_centroid，这个函数基于餐厅的位置寻找一个集群（一个餐厅列表）的重心，返回的重心是所有餐厅的经纬度的均值。提示运用mean函数。

- 问题6：实现kmeans算法，脚手架已经填充好了算法的第一步。为了完成该算法，在每一次while循环的迭代中做一下两件事。1. 将restaurants分组为集群，每一个集群包含所有临近于同一重心点的餐厅。2.绑定centroids为所有集群的新的重心点列表。提示利用group_by_centroid和find_centroid方法

## 阶段3：监督学习

- 简介：在这个阶段，你会预测用户将会给一个餐厅几分的评分。你将实现一个监督学习的算法尝试去从用户以往的评分去推广。通过分析过往得评分，我们可以预测用户将会给一个新的餐厅多的评分。
- 描述：为了预测评分，你将实现简单的最小二乘线性回归，一种常用的统计学策略用一条直线去估算某输入（比如价格）和某输出（比如评分）的关系，该算法接受一个输入对序列，并计算出一条最小化线和输出的平均方差的线的斜率和截距。

- 问题7：实现find_predictor函数，接受一个user，一个餐厅序列和一个特性函数feature_fn，这个函数返回两个值，一个是predictor函数，一个是r_squared值。predictor函数是一条y = ax + b的直线，回归系数计算有一系列的公式，可以参照官网。r平方表示了这条直线描述原数据的精准程度。Sxx和Syy计算类似，而Sxy的计算需要利用zip函数将xs序列和ys序列结合为一个[x,y]对的序列，然后利用list comprehension求得Sxy的值。

- 问题8：实现best_predictor，接受一个user参数和一个餐厅列表和一个feature_fns的序列，对于每个feature_fun计算出最佳的predictor函数并且返回拥有最高r方的predictor函数。这个问题也是利用了list comprehension，将reviewed数据传入find_predictor函数并生成一个predictor,r_squared对序列，然后借由此序列生成一个以predictor为键，r_squared为值的字典。再通过max函数接受生成的map，由key函数决定返回r_squared最大的predictor函数

- 问题9：实现rate_all，接受一个user,一个restaurants列表和一个feature_fns序列，返回一个字典，键为restaurants中每个restaurant的名字，而值是评分（数字）。若餐厅已被用户评分，则rate_all会为餐厅赋值为用户评分，若没有被评价过，则会利用最佳predictor函数计算评分，最佳predictor由feature_fns选出。我这里的做法是for in对restaurants中的所有restaurant遍历，若已被评分则用这个分数，否则用predictor计算。

- 问题10：为了集中注意力到特定的餐厅分类，实现search函数，这个函数接受一个分类查询和一个餐厅序列作为参数，返回所有包含这个分类的餐厅。提示利用list comprehension的条件筛选

# Google 推荐系统公平性论文解读

##困惑

- [ ] 什么是 **pairwise comparisons** ?
- [ ] 什么是**pairwise正则化**?
- [ ] 文中subgroup是什么意思?

----

## 图注

![Notation used throughout the paper](/Users/Brent/Library/Mobile Documents/com~apple~CloudDocs/推荐相关算法笔记/FIRP_table1.png)

## 随感

* 面对动态的系统，只能通过[^pairwise]才能的到相对公正的偏好数据，因为用户的偏好是动态的
* 是否可以在某种意义上通过泛品类的维度在协同的角度对item推荐，从而弱化item的[^客观指标]，从而实现某种意义上的正则化，从公平性的角度对系统进行提升
* 是否可以量化item j 的敏感属性？*sj∈{0,1}* [^1] ,从而对item j的得分在敏感度维度有敏感度权重 <font size=2>*Wj*</font>
* 在正则化掉用户的engagement之后可能会引发一些标题党之类的问题
* Pairwise Fairness概念的适用场景是什么？如果说为了measurement用户的偏好，那么遵从Pairwise Fairness是恰当的，但是是否能在生产环境使用Pairwise Fairness思想对推荐内容队列进行重排？包括这样做是否会与[^calibrated]概念产生冲突？



## 实验想法

* 改变现有的user engagement:*使用相对值*，然后对现有item使用group的方式进行满足pairwise fairness的训练

  *P(cq(j,j′)|yq,j>yq,j′,sj=0,zq,j=~z)=P(cq(j,j′)|yq,j>yq,j′,sj=1,zq,j=~z),∀~z*

* 







---------------------------
###personal概念

[^客观指标]:包括但不限于PV,UV,CTR,互动率
[^pairwise]:令观测组与对照组中的数据成对出现，实现无偏。举个例子就是令正负样本成对出现，从而进行校验。

---------

###第三方概念

[^Pairwise Fairness]:基于pairwise提出一系列新颖的评估推荐公平性的指标，并表明pairwise fairness metric与排序效果直接相关并分析与poinwise fairness metrics的关系。
[^Pairwise Regularization]:提出一个正则化方法在给定的指标上提高模型性能，同时对pointwise模型也有效
[^Real-world Experiments]:在大规模生产环境的推荐系统上实验，论证其在pairwise fairness上有显著提升
[^calibrated]:它是按比例推荐。也就是说用户看了99%的学习的。看了1%的娱乐的。如果是一般的推荐系统，就只给用户推荐他主要的兴趣。而这里按比例推荐的，就是遵从以前的用户看的这个比例。也给用户推99%学习的和1%娱乐的。这样做到了多样性。







-----

### 原文

[^1]:本文聚焦于under-recommended的groups of items的风险。比如，一个社交网络会将a given demographic group排得靠后，那么可能会限制该群组的可见度和参与度。如果一个网站的评论模块是个性化的，如果特定群体的评论排序靠后，那么他们将会在网站拥有更少的声音。更抽象的说，我们假设每个item j都有敏感属性sj∈{0,1}sj∈{0,1}。我们会去评测是否一个group的items被系统性地排后。

[^Pairwise Fairness]:对于使用排序公式g的模型f，如果在items被交互成都相同的情况下，其两个group里点击的item排在另一个未点击item之前的可能性在是相同的，则被认为满足pairwise fairnessP(cq(j,j′)|yq,j>yq,j′,sj=0,zq,j=~z)=P(cq(j,j′)|yq,j>yq,j′,sj=1,zq,j=~z),∀~z(2)P(cq(j,j′)|yq,j>yq,j′,sj=0,zq,j=z~)=P(cq(j,j′)|yq,j>yq,j′,sj=1,zq,j=z~),∀z~(2)此定义为我们提供了每个组中items的排名准确性的总体概念。但这块对于曝光不足group的items还是有问题的。假设有A、B两个group {Aj}3j=1∪{Bj}3j=1{Aj}j=13∪{Bj}j=13，都有3个items，第一种情况A1被点击，第二种B1被点击。 第一种情况，系统给出[A2,A3,B1,A1,B2,B3],第二种情况给出[A1,A2,A3,B1,B2,B3],我们可以看到整体pairwise accuracy都是2/5,但是第二种情况，B都在A后边。这个两个情况都有把被点击item排低的问题，但是后者显然更有问题，它独立于用户偏好，而系统性地偏好一个group。为了解决这个问题，我们将上述的pairwise fairness定义拆分成两个独立的标准：pairwise accuracy between items in the same group and pairwise accuracy between items from different groups。我们将这些metrics叫做`intra-group pairwise accuracy` and `inter-group pairwise accuracy` Intra-Group Acc. ≜P(cq(j,j′)|yq,j>yq,j′,sj=sj′,zq,j=~z)(3) Intra-Group Acc. ≜P(cq(j,j′)|yq,j>yq,j′,sj=sj′,zq,j=z~)(3) Inter-Group Acc. ≜P(cq(j,j′)|yq,j>yq,j′,sj≠sj′,zq,j=~z)(4) Inter-Group Acc. ≜P(cq(j,j′)|yq,j>yq,j′,sj≠sj′,zq,j=z~)(4)于是我们也可以定义`Intra-Group Pairswise Fairness` and `Inter-Group Pairwise Fairness`标准.
[^Intra-Group Pairwise Fairness]:对于使用排序公式g的模型f，如果在items被交互程度相同的情况下，两个group里内部点击的item排在另一个未点击item之前的可能性在是相同的，则被认为满足intra-group pairwise fairness
[^Inter-Group Pairwise Fairness]:对于使用排序公式g的模型f，如果在items被交互程度相同的情况下，两个group里点击的item排在另一个组里未点击item之前的可能性在是相同的，则被认为满足inter-group pairwise fairness






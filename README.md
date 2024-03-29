### 题目：波士顿房价预测

### 作者:
 * [🤓Diomchen](https://github.com/Diomchen)
 * [🐶yremp](https://github.com/yremp)
 * [😺haoyang7](https://github.com/haoyang7)

### 数据集摘要

-|-
---|---
名称|波士顿房价数据集（Boston House Prices dataset）
特征简介|CRIM - 城镇人均犯罪率<br>ZN - 占地面积超过25,000平方英尺的住宅用地比例<br>INDUS - 每个城镇非零售业务的比例<br>CHAS - Charles River虚拟变量<br>NOX - 一氧化氮浓度（每千万份）<br>RM - 每间住宅的平均房间数  <br>AGE - 1940年以前建造的自住单位比例 <br>DIS - 波士顿的五个就业中心加权距离<br>RAD - 径向高速公路的可达性指数<br>TAX - 每10,000美元的全额物业税率<br>PTRATIO - 城镇的学生与教师比例<br>B - $ 1000*(Bk - 0.63)^2$其中Bk是城镇黑人的比例  <br>LSTAT - 区域中被认为是低收入阶层的比率<br>MEDV - 自有住房的中位数报价, 单位1000美元
记录数|506条
分析目标|建立一个从已知特征预测波士顿未知房价的模型
分析思路和方法|采用线性回归和随机森林对房价进行预测，同时筛选出影响波士顿房价的主要因素

## 1. 简介

### 1.1 数据集概况

#### 1.1.1 数据来源
本文所用《波士顿房价数据集》数据来源于UCI的《Boston House Prices dataset》。

#### 1.1.2 属性
CRIM - 城镇人均犯罪率<br>ZN - 占地面积超过25,000平方英尺的住宅用地比例<br>INDUS - 每个城镇非零售业务的比例<br>CHAS - Charles River虚拟变量<br>NOX - 一氧化氮浓度（每千万份）<br>RM - 每间住宅的平均房间数  <br>AGE - 1940年以前建造的自住单位比例 <br>DIS - 波士顿的五个就业中心加权距离<br>RAD - 径向高速公路的可达性指数<br>TAX - 每10,000美元的全额物业税率<br>PTRATIO - 城镇的学生与教师比例<br>B - $ 1000*(Bk - 0.63)^2$其中Bk是城镇黑人的比例  <br>LSTAT - 区域中被认为是低收入阶层的比率<br>MEDV - 自有住房的中位数报价, 单位1000美元

#### 1.1.3 基本统计特征
![20191214194214.png](https://i.loli.net/2019/12/14/Gzkab6UulyXHRVj.png)

### 1.2 分析手段和方法

#### 1.2.1 分析手段
&emsp;&emsp;回归是一种运用广泛的统计分析方法，可以通过规定因变量和自变量来确定变量之间的因果关系，建立回归模型，并根据实测数据来求解模型的各参数，然后评价回归模型是否能够很好的拟合实测数据，如果能够很好的拟合，则可以根据自变量作进一步预测。

#### 1.2.2 分析方法
&emsp;&emsp;利用sklearn库，搭建线性回归模型和随机森林模型进行波士顿房价预测，同时比较二者相同数据集中的RMSE（均方根误差）的大小，对二者模型进行评估和挑选。利用随机森林对特征重要度进行排行，找出对波士顿房价影响最大的因素。

### 1.3 结论简述
&emsp;&emsp;线性回归模型拟合较好，其中LSTAT(人口状况下降百分数)和RM(每间住宅平均房间数)对波士顿房价影响较大。其次，PTRATIO(城镇的学生与教师比例),CRIM(城镇人均犯罪率),INDUS(每个城镇非零售业务的比例),DIS(波士顿的五个就业中心加权距离)这几个因素对房价也产生些许影响。

### 1.4 团队分工
* 陈书晗：负责线性回归模型和随机森林模型搭建
* 杨浩：负责分析图表的绘制
* 杨开友：负责文档的攥写

## 2. 数据处理过程

### 2.1 数据导入与基本信息查看

#### 2.1.1 导入数据
&emsp;&emsp;利用sklearn加载其自带的boston数据集。
~~~python
    from sklearn.datasets import load_boston
    boston = load_boston()
~~~

#### 2.1.2 数据初步处理
&emsp;&emsp;将原来数据中的data项和prices项合并成一个新数据集。
~~~python
    #数据提取
    data_df = pd.DataFrame(data,columns=feature_names)
    prices_df = pd.DataFrame(prices,columns=['MEDV'])
    boston_df = pd.DataFrame(data_df)
    boston_df['MEDV'] = prices
    boston_df.describe()
~~~

#### 2.1.3 数据空缺值检查
&emsp;&emsp;通过pandas的info()函数查看数据集中是否存在空缺值和数据集中各变量的属性。从结果可看出，无缺失值且各特征数值为float类型。
~~~python
    boston_df.info()
~~~
![20191214204847.png](https://i.loli.net/2019/12/14/kWOMrmJGXHwls2Z.png)

### 2.2 数据分析图表绘制

#### 2.2.1 Pairplot多变量图
~~~python
    import seaborn as sns
    sns.pairplot(boston_df)
    plt.tick_params(labelsize=25)
    plt.savefig('pairplot.png')
~~~
![pairplot.png](https://i.loli.net/2019/12/14/NoOajR6xwkcydzJ.png)

#### 2.2.2 各变量直方图
~~~python
    boston_df.hist(bins=15)
    plt.savefig('hist.png')
~~~
![hist.png](https://i.loli.net/2019/12/14/zPrEfmjbWVZd4QR.png)

#### 2.2.3 各变量直方图
~~~python
    boston_df.hist(bins=15)
    plt.savefig('hist.png')
~~~

#### 2.2.4 heatmap热力图及相关矩阵表
~~~python
    names = feature_names.tolist()
    names.append('MEDV')
    correction = boston_df.corr()
    fig = plt.figure(figsize=(15,15)) 
    ax = fig.add_subplot()
    ax = sns.heatmap(correction,cmap='YlGnBu', linewidths=0.05,vmax=1, vmin=-1 ,annot=True,annot_kws={'size':14,'weight':'bold'})
    plt.xticks(np.arange(15),names) 
    plt.yticks(np.arange(15),names)
    ax.set_title('相关系数矩阵图')
    plt.savefig('heatmap.png')
    plt.show()
    correction
~~~
![heatmap.png](https://i.loli.net/2019/12/14/lJavX1b7zBTPRIC.png)
![20191215163309.png](https://i.loli.net/2019/12/15/KHBwJMQ3kDApCyX.png)

#### 2.2.5 INDUS，RM，RAD，LSTAT,MEDV五特征箱型图
~~~python
    sns.boxplot(data=boston_df[['INDUS','RM','RAD','LSTAT']])
    plt.savefig("box.png")
~~~
![](https://i.loli.net/2019/12/14/BdkO5rDCwALxXv7.png)

#### 2.2.6 INDUS，RM，RAD，LSTAT,MEDV五特征小提琴图
~~~python
    sns.violinplot(data=boston_df[['INDUS','RM','RAD','LSTAT']])
    plt.savefig("violin.png")
~~~
![violin.png](https://i.loli.net/2019/12/14/Io27jLhB5b9xJwK.png)

#### 2.2.7 房价(MEDV)hist图以及正态分布验证
~~~python
    sns.distplot(boston_df['MEDV'])
    plt.savefig('MEDV.png')
    #正态分布验证图
    from scipy import stats
    res = stats.probplot(boston_df['MEDV'],plot=plt)
~~~
![MEDV.png](https://i.loli.net/2019/12/14/KhLwMYbjdEAf5yr.png)

![MEDV正态分布验证图.png](https://i.loli.net/2019/12/14/4zx1fIM8nqwSYWh.png)

### 2.3 波士顿房价预测

#### 2.3.1 线性回归

##### 2.3.1.1 数据初步划分
~~~python
    #导入所需库
    from sklearn.model_selection import train_test_split
    from sklearn.linear_model import LinearRegression
    from sklearn import metrics
    #数据自变量与因变量划分
    X = data_df.drop(['MEDV'],axis=1)
    y = data_df['MEDV']
    #训练集与测试集划分
    X_train,X_test,y_train,y_test = train_test_split(X,y,random_state=0)
    #模型拟合
    model = LinearRegression()
    model.fit(X_train,y_train)
~~~

##### 2.3.1.2 预测集拟合散点图绘制
~~~python
    predict = model.predict(X_test)
    #数据预测对比图
    y_index = [x for x in range(len(y_test))]
    plt.scatter(y_index,y_test,label="actual",c="orange")
    plt.scatter(y_index,predict,label="predict",c="green")
    plt.legend(fontsize=15,markerscale=1)
    plt.tick_params(labelsize=25)
    plt.grid()
    plt.savefig('线性回归预测值与真实值.png')
    plt.show()
~~~
![线性回归预测值与真实值.png](https://i.loli.net/2019/12/14/pqOJK4APCQUb2Z6.png)

##### 2.3.1.3 线性回归模型评估
~~~python
    #可决系数
    print("R Square : {}".format(model.score(X_test,y_test)))
    #均根方差
    RMSE = np.sum(np.sqrt((predict-y_test)**2))/len(data_df)
    print("RMSE : {}".format(RMSE))
~~~

~~~python
    R Square : 0.6354638433202131
    RMSE : 0.9207073691961186
~~~

#### 2.3.2 随机森林

##### 2.3.2.1 模型搭建
~~~python
    from sklearn.ensemble import RandomForestRegressor
    from sklearn.model_selection import GridSearchCV
    param_grid = {
        'n_estimators':[5,10,20,50,100,200],#决策树的个数
        'max_depth':[3,5,7],#最大树深
        'max_features':[0.6,0.7,0.8,1]#特征采用划分
    }
    rf = RandomForestRegressor()
    grid = GridSearchCV(rf,param_grid=param_grid,cv=3)
    grid.fit(X_train,y_train)
~~~

##### 2.3.2.2 作出最优树图
~~~python
    from sklearn import tree
    import pydotplus
    from IPython.display import Image,display

    rf_reg = grid.best_estimator_
    estimator = rf_reg.estimators_[3]
    dot_data = tree.export_graphviz(estimator,out_file=None,filled=True,rounded=True)

    graph = pydotplus.graph_from_dot_data(dot_data)

    graph.write_pdf('boston_price_predict_tree.pdf')
    display(Image(graph.create_png()))
~~~
![boston_price_predict_tree_page-0001.jpg](https://i.loli.net/2019/12/14/wmyJhPlM13bFtVv.jpg)

##### 2.3.2.4 预测集拟合散点图绘制
~~~python
    result['label'].plot(style='k.',c='orange',markersize=15)
    result['prediction'].plot(style='r.',c='green',markersize=15)
    plt.legend(fontsize=15,markerscale=1)
    plt.tick_params(labelsize=25)
    plt.grid()
    plt.savefig("随机森林拟合结果.png")
~~~
![](https://i.loli.net/2019/12/14/YtswfxKyZWm3FVi.png)

##### 2.3.2.5 特征重要度排行图
~~~python
    feature_importances = rf_reg.feature_importances_
    indices = np.argsort(feature_importances)[::-1]
    sns.barplot(feature_names[indices],feature_importances[indices])
    plt.savefig("features_importance.png")
~~~
![features_importance.png](https://i.loli.net/2019/12/14/ti2h3Tx7eUNGfk6.png)

##### 2.3.2.3 随机森林模型评估
~~~python
    #最优树模型参数
    print("Best Params : {}".format(grid.best_params_))
    #可决系数
    print("R Square : {}".format(grid.best_score_))
    #均根方差
    MSE = metrics.mean_squared_error(y,rf_reg.predict(X))
    RMSE = np.sqrt(MSE)
    print("RMSE : {}".format(RMSE))
~~~
~~~python
    Best Params : {'max_depth': 7, 'max_features': 0.6, 'n_estimators': 200}
    R Square : 0.8654507915978027
    RMSE : 2.5590515715912807
~~~

## 3. 结果分析

### 3.1 分析各特征与波士顿房价(MEDV)之间的相关性
1.城镇犯罪率(CRIM)越低位置，房价越高。<br>
2.占地面积超过25,000平方英尺的住宅用地比例(ZN)与房价之间相关性较低<br>
3.每个城镇非零售业务的比例(INDUS)较高位置，房价较低。但是，在INDUS较低的位置，也存在较低房价。<br>
4.Charles River虚拟变量(CHAS)流域附近房价有高有低。<br>
5.一氧化氮浓度(NOX)与房价相关性不明显。<br>
6.每间住宅的平均房间数(RM)与房价呈正相关，房间数越多，房价越高。<br>
7.1940年以前建造的自住单位比例(AGE)与房价相关性不高<br>
8.波士顿的五个就业中心加权距离(DIS)越高，房价越高，但大部分房子的DIS处于4以下。<br>
9.径向高速公路的可达性指数(RAD)与房价无相关性<br>
10.每10,000美元的全额物业税率(TAX)处于400以下，房价有高有低。<br>
11.城镇的学生与教师比例(PTRATIO)较高，低价房源也越多。<br>
12.1000（Bk - 0.63）^2其中Bk是城镇黑人的比例(B)大多处于400左右，房价也在集中在2~4万美元。<br>
13.区域中被认为是低收入阶层的比率(LSTAT)与房价呈负相关，LSTAT越高，房价越低。<br>

### 3.2 MEDV，RM分布情况

#### 3.2.1 波士顿房价(MEDV)
&emsp;&emsp;从箱型图和小提琴图可知，MEDV大部分分布于1.5-2.5万美元内。其中最低房价在5000美元左右，但也存在部分房价高达5万美元以上。同时，对MEDV进行正态分布检验，结果显示，房价也是呈正态分布，且期望值$\mu$约等于2万美元。

#### 3.2.2 房间数(RM)
&emsp;&emsp;大部分房子的房间数在6个左右，但最低不会低于3个，最多也不会超过10个。

### 3.3 预测结果分析

#### 3.3.1 线性回归预测结果分析
&emsp;&emsp;线性回归的均根方差(RMSE)为0.92，可决系数(R Square)为0.64。从数据可知，线性回归对数据拟合较好，且预测值与实际值之间的误差也较低。这说明线性回归在此数据集上的预测效果较好。

#### 3.3.2 随机森林预测结果分析
&emsp;&emsp;随机森林模型的均根方差(RMSE)为2.56，可决系数(R Square)为0.87。从数据可知，随机森林模型对数据拟合优于线性回归，但预测值与实际值的误差却要远高于线性回归。这表明随机森林模型虽能较好拟合原始数据，但其预测准确度却较差。

#### 3.3.3 各特征对房价的影响
&emsp;&emsp;从特征重要度排行图中可知，区域中被认为是低收入阶层的比率(LSTAT)与每间住宅的平均房间数(RM)对房价(MEDV)的影响较大，其为最主要的因素。当然，城镇的学生与教师比例(PTRATIO)，犯罪率(CRIM)，每个城镇非零售业务的比例(INDUS),波士顿的五个就业中心加权距离(DIS)这几个因素对房价影响也不容忽视。径向高速公路的可达性指数(RAD),占地面积超过25,000平方英尺的住宅用地比例(ZN),Charles River流域附近(CHAS)这几个因素对房价几乎不产生影响。

## 4.结论与展望

### 4.1 结论
&emsp;&emsp;波士顿房价主要由低收入阶层的比率和房间数决定的，低收入阶层能承受的房价有限，这也就决定了当地房价的上限和均值。同时，房间数越多，占地就越多，其所需面积就越大，因此房价也就越贵。当然，学区房、当地犯罪率、以及房子周边购物便捷程度也起着很大的影响因素。学区房其价格决定于学生的消费承受能力，所以学区房的价格一般也不会太高。犯罪率低的地区，人们会更乐意去买房，这也就导致当地房价的抬高。城镇非零售业务的比例，换个指标来说就是购物便捷程度，购物便捷程度越高，房价自然也就较高。

### 4.2 展望
&emsp;&emsp;上述结论都是从宏观上而言的，结论只具有一般性。若要具体到某个房子，则需要根据当地实际情况分析。同时，由于时间关系，我们只搭建并训练了线性回归和随机森林两个模型，希望在未来的研究中能加入更多模型，进行分析对比，选出最优模型，更好的进行预测。

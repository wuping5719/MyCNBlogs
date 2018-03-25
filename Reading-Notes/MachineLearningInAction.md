<h2>《机器学习实战》 :books: </h2> 

> Peter 著  人民邮电出版社 

```txt
1.知识表示可以采用规则集的形式，也可以采用概率分布的形式，甚至可以是训练样本集中的一个实例。

2.分类和回归属于监督学习，之所以称之为监督学习，是因为这类算法必须知道预测什么，即目标变量的分类信息。

3.与监督学习相对应的是非监督学习，此时数据没有类别信息，也不会给定目标值。在非监督学习中，
将数据集合分成由类似的对象组成的多个类的过程被称为聚类；将寻找描述数据统计值的过程称之为密度估计。

4.确定选择监督学习算法之后，需要进一步确定目标变量类型，如果目标变量是离散型，如是／否、1／2／3、A／B／C 或者
红／黄／黑等，则可以选择分类算法；如果目标变量是连续型的数值，如0.0～100.00、-999～999或者+∞～-∞等，则需要选择回归算法。

5.k近邻算法采用测量不同特征值之间的距离方法进行分类。
  (1) 优点：精度高、对异常值不敏感、无数据输入假定。
  (2) 缺点：计算复杂度高、空间复杂度高。   
  (3) 适用数据范围：数值型和标称型。
  (4) 工作原理：存在一个样本数据集合，也称作训练样本集，并且样本集中每个数据都存在标签，即我们知道样本集中每一数据
与所属分类的对应关系。输入没有标签的新数据后，将新数据的每个特征与样本集中数据对应的特征进行比较，
然后算法提取样本集中特征最相似数据(最近邻)的分类标签。一般来说，我们只选择样本数据集中前 k 个最相似的数据，
这就是 k-近邻算法中 k 的出处，通常 k 是不大于 20 的整数。最后，选择 k 个最相似数据中出现次数最多的分类，作为新数据的分类。
  (5) k 近邻算法的一般流程:
   ① 收集数据：可以使用任何方法。
   ② 准备数据：距离计算所需要的数值，最好是结构化的数据格式。
   ③ 分析数据：可以使用任何方法。
   ④ 训练算法：此步骤不适用于 k 近邻算法。
   ⑤ 测试算法：计算错误率。
   ⑥ 使用算法：首先需要输入样本数据和结构化的输出结果，然后运行 k 近邻算法。判定输入数据分别属于哪个分类，
最后应用对计算出的分类执行后续的处理。
```
```python
6.使用 Python 导入数据。
  科学计算包 NumPy；运算符模块: kNN.py
  from numpy import *
  import operator
  def createDataSet() :
     group = array ([[1.0, 1.1], [1.0, 1.0], [0, 0], [0, 0.1]])
     labels = ['A', 'A', 'B', 'B']
     return group, labels

7.实施 kNN 分类算法: classify0()。
  def classify0(inX,dataSet,labels,k):
    dataSetSize = dataSet.shape[0]
    # (以下三行)距离计算
    diffMat = tile(inX,(dataSetSize,1)) - dataSet
    sqDiffMat = diffMat**2
    sqDisstances = sqDiffMat.sum(axis=1)
    distances = sqDisstances**0.5
    sortedDistIndicies = distances.argsort()
    classCount = {}
    # (以下两行)选择距离最小的k个点
    for i in range(k):
        voteIlable = labels[sortedDistIndicies[i]]
        classCount[voteIlable] = classCount.get(voteIlable, 0) + 1
    sortedClassCount = sorted(classCount.iteritems(), 
    # 排序
        key = operator.itemgetter(1), reverse=True)
    return sortedClassCount[0][0]
  classify0() 函数有 4 个输入参数：用于分类的输入向量是 inX，输入的训练样本集为 dataSet，标签向量为labels，
最后的参数 k 表示用于选择最近邻居的数目，其中标签向量的元素数目和矩阵 dataSet 的行数相同。

8.准备数据：从文本文件中解析数据。
  def file2matrix(filename) :
     fr = open(filename)
     arrayOlines = fr.readlines()
     numberOfLines = len(arrayOlines)
     # 得到文件行数
     returnMat = zeros((numberOfLines, 3))
     # 创建返回的 Numpy 矩阵
     classLabelVector = []
     returnMat = zeros((numberOfLines, 3))  
     classLabelVector = []  
     index =0  
     # 解析文件数据到列表  
     for line in arrayOlines:  
         line = line.strip()  
         listFormLine = line.split('\t')  
         returnMat[index,:] = listFormLine[0:3]  
         classLabelVector.append(int(listFormLine[-1]))  
         index += 1  
     return returnMat, classLabelVector

9.准备数据：归一化特征值。
  def autoNorm(dataSet):
     minVals = dataSet.min(0)      # 每一列的最小值
     maxVals = dataSet.max(0)      # 每一列的最大值
     ranges = maxVals - minVals    # 幅度
     normDataSet = zeros(shape(dataSet))  # 创建一个一样规模的零数组
     m = dataSet.shape[0]          # 取数组的行
     normDataSet = dataSet - tile(minVals, (m,1))  # 减去最小值
     normDataSet = normDataSet / tile(ranges, (m,1))   # 特征值相除
     # 再除以幅度值，实现归一化，tile 功能是创建一定规模的指定数组
     return normDataSet, ranges, minVals

10.测试分类器效果。
   def datingClassTest():     # 分类器针对约会网站的测试代码
     hoRatio = 0.10
     datingDataMat, datingLabels = file2matrix('datingTestSet.txt') # load data set from file
     normMat, ranges, minVals = autoNorm(datingDataMat)
     # 计算测试向量的数量，此步决定哪些数据用于分类器的测试和训练样本
     m = normMat.shape[0]
     numTestVecs = int(m * hoRatio)
     errorCount = 0.0
     # 计算错误率，并输出结果
     for i in range(numTestVecs):
        classifierResult = classify0(normMat[i,:], normMat[numTestVecs:m,:], datingLabels[numTestVecs:m], 3)
        print("the classifier came back with: %d, the real answer is: %d" % (classifierResult, datingLabels[i]))
        if (classifierResult != datingLabels[i]): errorCount += 1.0
     print("the total error rate is: %f" % (errorCount / float(numTestVecs)))
     print(errorCount)
    
   datingDataMat, datingLabels = file2matrix('datingTestSet.txt') # 读取文件数据

   datingClassTest()
```

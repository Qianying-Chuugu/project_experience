# scikit-learn（sklearn）

**是什么**：Python 里最常用的**传统机器学习**工具库，通常简称 sklearn。一句话：一套现成的分类、聚类、文本特征、相似度计算和模型评估算法。定位是"传统机器学习工具箱"。

**能干嘛**：分类（判断一条数据属于哪个类别）、聚类（自动发现相似数据的分组）、回归（预测连续数值）、文本向量化（把文字转成数字）、降维（压缩高维数据）、异常检测（找出与大多数数据不同的样本）、模型训练与验证、准确率 / 召回率 / F1 等指标计算。

**适合什么**：中小规模数据、课程项目、传统机器学习实验、建立基线方案、快速比较多种算法。

**在本项目里具体负责**：TF-IDF 文本表示、文档相似度、主题聚类、后期训练资料类型分类器、模型效果评估，以及**为中文语义模型提供一个可比较的基线**。

## 统一 API

所有模型都是同一套写法，换算法基本只换类名：

```python
model.fit(X_train, y_train)      # 训练
model.predict(X_test)            # 预测
model.transform(X)               # 转换（向量化 / 降维类）
model.fit_transform(X)           # 一步到位
model.score(X_test, y_test)      # 打分
```

## 常用 API

| 用途 | API |
| --- | --- |
| 文本向量化 | `TfidfVectorizer`、`CountVectorizer` |
| 相似度 | `sklearn.metrics.pairwise.cosine_similarity` |
| 聚类 | `KMeans`、`DBSCAN`、`AgglomerativeClustering` |
| 分类 | `LogisticRegression`、`LinearSVC`、`MultinomialNB`、`RandomForestClassifier` |
| 降维 | `TruncatedSVD`、`PCA` |
| 评估 | `accuracy_score`、`precision_score` / `recall_score` / `f1_score`、`classification_report`、`confusion_matrix`、`silhouette_score` |
| 流程 | `train_test_split`、`cross_val_score`、`Pipeline` |

## 注意事项

1. **中文必须先分词**。`TfidfVectorizer` 默认按空格和标点切词，中文整句会被当成一个词。常见做法是先用 jieba 切好、用空格拼回字符串，或传 `tokenizer=` 自定义切分。
2. **只吃数值**，不能直接喂字符串；缺失值要自己处理（`SimpleImputer`），列类型混杂的数据要先清洗。
3. **量纲差异大时要标准化**（`StandardScaler` / `MinMaxScaler`），否则距离类算法（KMeans、KNN）会被数值大的特征主导。
4. `KMeans` 要事先指定 `n_clusters`，且结果受初始点影响——固定 `random_state` 才能复现；类别数可以用轮廓系数试。
5. 类别不均衡时 `train_test_split` 要加 `stratify=y`。
6. **多步骤处理用 `Pipeline` 串起来**，且不要在划分训练集之前做全量向量化 / 标准化——那会把测试集信息泄漏进去，评估分数虚高。
7. 数据要能装进内存，它不做分布式。规模一大就该换方案。

它的强项是中小规模数据上的传统算法，定位是基线和轻量方案，不是深度学习 / 大模型的替代品——项目里需要更强语义能力时要换模型，它留在原地当对照。

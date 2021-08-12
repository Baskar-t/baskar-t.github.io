---
layout: post
title:  " Python lazy predict to evaluate all models frrom scikit-learn"
author: baskar
image: assets/images/lazypredict.jfif
categories: [Python]
tags: [lazy predict,scikit-learn]
featured: false
hidden: false
---



lazy predict : fit and evaluate all the models from scikit-learn with a single line of code

The dataset used for the example to show the comparison of different classification models is breast cancer dataset from the sklearn library


```python
from lazypredict.Supervised import LazyClassifier, LazyRegressor
from sklearn.model_selection import train_test_split
from sklearn import datasets
```


```python
# load data
data = datasets.load_breast_cancer()
X, y = data.data, data.target
print(X[0])
print(y[0])
```



Output:
[1.799e+01 1.038e+01 1.228e+02 1.001e+03 1.184e-01 2.776e-01 3.001e-01
 1.471e-01 2.419e-01 7.871e-02 1.095e+00 9.053e-01 8.589e+00 1.534e+02
 6.399e-03 4.904e-02 5.373e-02 1.587e-02 3.003e-02 6.193e-03 2.538e+01
 1.733e+01 1.846e+02 2.019e+03 1.622e-01 6.656e-01 7.119e-01 2.654e-01
 4.601e-01 1.189e-01]
0


The Lazyclassifier method returns dataframes models and predictions.the models dataframe has the summary of the performance of all the clasiification models and predictions dataframe has the details of all predictions of the models.


```python
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=.2, random_state=42)
# fit all models
clf = LazyClassifier(predictions=True)
models, predictions = clf.fit(X_train, X_test, y_train, y_test)
```


```python
print(models.to_markdown()) 
```



Summary of the performance of different models is detailed below,



```python
| Model                         |   Accuracy |   Balanced Accuracy |   ROC AUC |   F1 Score |   Time Taken |
|:------------------------------|-----------:|--------------------:|----------:|-----------:|-------------:|
| BernoulliNB                   |   0.982456 |            0.98133  |  0.98133  |   0.982456 |    0.0218644 |
| PassiveAggressiveClassifier   |   0.982456 |            0.98133  |  0.98133  |   0.982456 |    0.018441  |
| SVC                           |   0.982456 |            0.976744 |  0.976744 |   0.982369 |    0.0226231 |
| Perceptron                    |   0.973684 |            0.974288 |  0.974288 |   0.973742 |    0.0169129 |
| AdaBoostClassifier            |   0.973684 |            0.969702 |  0.969702 |   0.973621 |    0.168462  |
| LogisticRegression            |   0.973684 |            0.969702 |  0.969702 |   0.973621 |    0.0589025 |
| SGDClassifier                 |   0.964912 |            0.967245 |  0.967245 |   0.96506  |    0.0248206 |
| ExtraTreeClassifier           |   0.964912 |            0.967245 |  0.967245 |   0.96506  |    0.0126221 |
| CalibratedClassifierCV        |   0.973684 |            0.965116 |  0.965116 |   0.973481 |    0.0479393 |
| RandomForestClassifier        |   0.964912 |            0.958074 |  0.958074 |   0.964738 |    0.264529  |
| LGBMClassifier                |   0.964912 |            0.958074 |  0.958074 |   0.964738 |    0.13135   |
| GaussianNB                    |   0.964912 |            0.958074 |  0.958074 |   0.964738 |    0.0200663 |
| ExtraTreesClassifier          |   0.964912 |            0.958074 |  0.958074 |   0.964738 |    0.155032  |
| QuadraticDiscriminantAnalysis |   0.95614  |            0.955617 |  0.955617 |   0.956237 |    0.0200706 |
| LinearSVC                     |   0.95614  |            0.955617 |  0.955617 |   0.956237 |    0.0488999 |
| BaggingClassifier             |   0.95614  |            0.951032 |  0.951032 |   0.956036 |    0.0660677 |
| XGBClassifier                 |   0.95614  |            0.951032 |  0.951032 |   0.956036 |    0.218896  |
| LinearDiscriminantAnalysis    |   0.95614  |            0.946446 |  0.946446 |   0.955801 |    0.0357475 |
| NearestCentroid               |   0.95614  |            0.946446 |  0.946446 |   0.955801 |    0.0160651 |
| NuSVC                         |   0.95614  |            0.946446 |  0.946446 |   0.955801 |    0.0313258 |
| RidgeClassifier               |   0.95614  |            0.946446 |  0.946446 |   0.955801 |    0.0149305 |
| RidgeClassifierCV             |   0.95614  |            0.946446 |  0.946446 |   0.955801 |    0.0297892 |
| KNeighborsClassifier          |   0.947368 |            0.94399  |  0.94399  |   0.947368 |    0.0254056 |
| DecisionTreeClassifier        |   0.947368 |            0.94399  |  0.94399  |   0.947368 |    0.0191224 |
| LabelSpreading                |   0.938596 |            0.932362 |  0.932362 |   0.93845  |    0.0399294 |
| LabelPropagation              |   0.938596 |            0.932362 |  0.932362 |   0.93845  |    0.0328009 |
| DummyClassifier               |   0.570175 |            0.531117 |  0.531117 |   0.564219 |    0.0123785 |
```

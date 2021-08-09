---
layout: post
title:  " Sentiment analysis zero-shot classification using huggingface transformers"
author: baskar
image: assets/images/mask_image.jpeg
categories: [ML & DL projects]
tags: [Huggingface,tranformers]
featured: false
hidden: false
---

Learn how to do zero-shot classification of text using the Huggingface transformers pipeline


```python
# Install hugging face transformers 
!pip install transformers==3.1.0
```

Import the transformers pipeline,


```python
from transformers import pipeline
```

Set the zero-shot-classfication pipeline,


```python
classifier = pipeline("zero-shot-classification")
```

For GPU usage,


```python
classifier = pipeline("zero-shot-classification", device=0)
```

The classifer takes two arguments the sequence text and candidate labels , for sentiment classification the arguments similar to the below code are passed, 


```python
sequence = "Tech Companies in India are having problem raising funds. Nevertheless, they are doing great with customer acquisition"
candidate_labels = ["positive", "negative"]
classifier(sequence, candidate_labels)
```

##### Output:
{'labels': ['negative', 'positive'],
 'scores': [0.5736649632453918, 0.42633509635925293],
 'sequence': 'Tech Companies in India are having problem raising funds. Nevertheless, they are doing great with customer acquisition'}


```python
The hypothesis template argument 'The sentiment of this review is {}.' improves the sentiment score.
```


```python
classifier = pipeline("zero-shot-classification")
sequence = "Tech Companies in India are having problem raising funds. Nevertheless, they are doing great with customer acquisition"
candidate_labels = ["positive", "negative"]
hypothesis_template = "The sentiment of this review is {}."
classifier(sequence, candidate_labels,hypothesis_template=hypothesis_template)
```

#### Output:
'labels': ['positive', 'negative'],
 'scores': [0.6742060780525208, 0.32579392194747925],
 'sequence': 'Tech Companies in India are having problem raising funds. Nevertheless, they are doing great with customer acquisition'}

The below code snippers is a sentiment classifier function , given a text as input the sentiment of that text is generated.


```python
class classifier():
  def __init__(self,device):
    self.device=device
    self.pipeline = pipeline("zero-shot-classification",device=self.device)
    self.candidate_labels = ["positive", "negative","neutral"]

  def sentiment_scores(self,review_text):
    zero_shot_labels = []
    zero_shot_CI=[]
    for each in review_text.tolist():
      Result=self.pipeline(each,self.candidate_labels)
      label_list=Result.get('labels')
      scores_list=Result.get('scores')
      label_dict = dict(zip(label_list, scores_list))
      Negative = label_dict.get('negative')
      Positive = label_dict.get('positive')
      Neutral  = label_dict.get('neutral')
      if Negative > 0.5:
        zero_shot_labels.append(-1)
        zero_shot_CI.append(Negative)
      elif Positive > 0.5:
        zero_shot_labels.append(1)
        zero_shot_CI.append(Positive)
      else:
        zero_shot_labels.append(0)
        zero_shot_CI.append(Neutral)
      
    return zero_shot_labels,zero_shot_CI


#GPU device=0,CPU device=-1
%%time
sentiment_classifier=classifier(device=0)
pred_label,CI = sentiment_classifier.sentiment_scores('text')
```

---
layout: post
title:  " Python Beautiful Soup data scrapping"
author: baskar
image: assets/images/web_scrapping.jfif
categories: [python]
tags: [beaufitul soup,web scrapping]
featured: false
hidden: false
---



Data scrapping using python beautiful soup

In the blog, we would see an example to scrape user reviews from Comparably reviews website, the below snapshot has the deatils of employee reviews.
We would scrape the review text and write it to a Pandas dataframe.

![Capture.PNG](attachment:Capture.PNG)

The HTML tags associated with the review text in the website , can be identified using F12 page debugging mode.The tag class for review text identified as 'class_="cppRH-review-quote'.


```python
# import required libraries
from bs4 import BeautifulSoup
import requests
import time
```


```python
#function to get all the review pages
def page_request(review_link):
  page = requests.get(review_link[0])
  soup = BeautifulSoup(page.content, 'html.parser')

  return soup
```


```python
url_list=[ ["https://www.comparably.com/companies/tcs/reviews"]]
```


```python
# funtion to fetch the review texts
def review_text(soup):
  review_list=soup.find_all(class_="cppRH-review-wrapper")
  for i in range(len(review_list)):
    review_rec_temp=[]
    review_text=review_list[i].find(class_="cppRH-review-quote").get_text()    
    review_rec_temp.append(review_text) 
    review_rec.append(review_rec_temp) 
    
  return review_rec
```


```python
rec_list=[]
review_rec=[]
review_link=[]
for url in url_list:
  time.sleep(3)
  soup=page_request(url)  
  rec_list=review_text(soup)
```


```python
import pandas as pd
df = pd.DataFrame(rec_list, columns =['Review'])
df.head(5)
```

![Capture1.PNG](attachment:Capture1.PNG)


```python

```

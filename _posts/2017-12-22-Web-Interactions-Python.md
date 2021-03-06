---
layout: post
title: "Web Interactions with Python"
date: "December 21, 2017"
categories: Software
tags: Pipelines
---

* TOC
{:toc}




# Web Scraping

## Requests

{% highlight python %}
import requests

# get a webpage
r = requests.get(url)
r.url
r.status_code
r.history

# get cookies from a site
requests.get(url).cookies['requests-is']

# send cookies
cookies = {'cookies_are':'working'}
requests.get(url, cookies = cookies)

# logins
requests.get(url, auth = ('user', 'pass'))

# parameters
r = requests.get('http://google.com/search', param = ('q': 'how long does a walrus lives?', 'btnl': 'I'm feeling lucking))

# timeout
requests.get(url, timeout = secs)
{% endhighlight %}

## BeautifulSoup

This module is used for web scraping static sites.

{% highlight python %}
import requests
from bs4 import BeautifulSoup

page = requests.get(url, timeout = 5)
soup = BeautifulSoup(page.content, 'html.parser')
{% endhighlight %}

Tags, classes, ids, etc can be extracted from the object.

{% highlight python %}
list(soup.children)
soup.find(tag)
soup.find_all(tag).get_text()
soup.find_all(tag, "class", id = "id")
soup.find_all(tag, class_="class", id = "id", string="pattern or pattern_function")
soup.find_all(tag, {'class': "class_name"})
{% endhighlight %}

Nested elements can be extracted by looking at the parent
{% highlight python %}
links = soup.find_all('a', href = True)
article_link = [x.get('href') for x in links if x.parent.name = 'article']
{% endhighlight %}

## Selenium

Selenium allows you to disguise your webscraping as activity similar to a human.

{% highlight python %}
from selenium import webdriver
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.ui import Select
from selenium.common.exceptions import *

# open/ close webdriver
def init_driver():
  driver = webdriver.Chrome("/home/chromedriver")
  
  # TimeoutException after 5 secs; 2 options
  driver.implicitly_wait(5)
  driver.wait = WebDriverWait(driver, 5)
  
  return driver
  
driver = init_driver()
driver.get(url)

# closes window and quits program
driver.close()
driver.quit()
{% endhighlight %}

You can go to a page and extract the html source into BeautifulSoup.

{% highlight python %}
driver.get(url)
page = driver.page_source
soup = BeautifulSoup(page, 'html.parser')
{% endhighlight %}

You can also perform actions. Here is an example of performing a google search. 

{% highlight python %}
driver.get("http://www.google.com")
try:
  box = driver.wait.until(EC.presence_of_element_located((By.NAME, "q")))
  button = driver.wait.until(EC.element_to_be_clickable((By.NAME, "btnK")))
  box.clear()
  box.send_keys(query)
  button.click()
except:
  print("Something went wrong")
{% endhighlight %}

Here are a few options

{% highlight python %}
# public version
element = driver.find_element_by_name("name")
element = driver.find_elements_by_name("name")

# private versions
element = driver.find_element(By.NAME, "name")
element = driver.find_elements(By.CLASS_NAME, "class")
{% endhighlight %}

You can set the driver to wait for loading with the following

* `EC.presence_of_element_located`
* `EC.element_to_be_clickable`
* `EC.visibility_of_element_located`

And these are things on the website to look for

* `By.NAME`
* `By.ID`
* `By.LINK_TEXT`
* `By.PARTIAL_LINK_TEXT`
* `By.TAG_NAME` (html tag)
* `By.CLASS_NAME`
* `By.CSS_SELECTOR`
* `By.XPATH`

A `NoSuchElementException` is raised if the element doesn't exist.

You can navigate dropdown bars with the following code

{% highlight python %}
select = Select(driver.find_element_by_id("sortSelection"))
select.select_by_visible_text("option 1")
{% endhighlight %}

You may need to switch frames to find your element

{% highlight python %}
driver.switch_to_frame('name')
{% endhighlight %}

You can switch windows with the following code

{% highlight python %}
driver.switch_to_window(driver.window_handles[i])
{% endhighlight %}

# Sending Emails

You can automate Python to send emails with the modules `smtplib` and `email`.

{% highlight python %}
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
{% endhighlight %}

{% highlight python %}
# email contents
sender = 'mail.autobot1@gmail.com'
receivers = 'your_email@email.com'
message = MIMEMultipart()
message['From'] = sender
message['To'] = receivers
message['Subject'] = 'Message Subject'
body = 'Message Body'
message.attach(MIMEText(body, 'plain'))

# send email: login and send
try:
  password = 'PASSWORD'
  with smtplib.SMTP('smtp.gmail.com', 587) as server:
    server.ehlo()
    server.starttls()
    server.login(sender, password)
    server.sendmail(sender, [receivers], message.as_string())
  logging.info('Email sent successfully')
except:
  logging.info('Error: email failed')
{% endhighlight %}


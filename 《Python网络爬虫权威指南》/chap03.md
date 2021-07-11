[toc]

# 编写网络爬虫

代码示例

- 使用产生的随机数随机的访问页面上的链接

  ```python
  from urllib.request import urlopen
  from bs4 import BeautifulSoup
  import datetime
  import random
  import re
  
  random.seed(datetime.datetime.now())
  def getLinks(articleUrl):
      html = urlopen("http://en.wikipedia.org"+articleUrl)
      bsObj = BeautifulSoup(html, "html.parser")
      return bsObj.find("div", {"id":"bodyContent"}).findAll("a", href=re.compile("^(/wiki/)((?!:).)*$"))
  links = getLinks("/wiki/Kevin_Bacon")
  while len(links) > 0:
      newArticle = links[random.randint(0, len(links)-1)].attrs["href"]
      print(newArticle)
      links = getLinks(newArticle)
  ```

- 如果要访问页面上的所有链接，则需要考虑去重。这里采用的是使用set集合的方式

  ```python
  from urllib.request import urlopen
  from bs4 import BeautifulSoup
  import re
  
  pages = set()
  def getLinks(pageUrl):
      global pages
      html = urlopen("http://en.wikipedia.org"+pageUrl)
      bsObj = BeautifulSoup(html, "html.parser")
      try:
          print(bsObj.h1.get_text())
          print(bsObj.find(id ="mw-content-text").findAll("p")[0])
          print(bsObj.find(id="ca-edit").find("span").find("a").attrs['href'])
      except AttributeError:
          print("This page is missing something! No worries though!")
      
      for link in bsObj.findAll("a", href=re.compile("^(/wiki/)")):
          if 'href' in link.attrs:
              if link.attrs['href'] not in pages:
                  #We have encountered a new page
                  newPage = link.attrs['href']
                  print("----------------\n"+newPage)
                  pages.add(newPage)
                  getLinks(newPage)
  getLinks("") 
  ```

- 随机访问网络上的链接

  ```python
  from urllib.request import urlopen
  from bs4 import BeautifulSoup
  import re
  import datetime
  import random
  
  pages = set()
  random.seed(datetime.datetime.now())
  
  #Retrieves a list of all Internal links found on a page
  def getInternalLinks(bsObj, includeUrl):
      internalLinks = []
      #Finds all links that begin with a "/"
      for link in bsObj.findAll("a", href=re.compile("^(/|.*"+includeUrl+")")):
          if link.attrs['href'] is not None:
              if link.attrs['href'] not in internalLinks:
                  internalLinks.append(link.attrs['href'])
      return internalLinks
              
  #Retrieves a list of all external links found on a page
  def getExternalLinks(bsObj, excludeUrl):
      externalLinks = []
      #Finds all links that start with "http" or "www" that do
      #not contain the current URL
      for link in bsObj.findAll("a", href=re.compile("^(http|www)((?!"+excludeUrl+").)*$")):
          if link.attrs['href'] is not None:
              if link.attrs['href'] not in externalLinks:
                  externalLinks.append(link.attrs['href'])
      return externalLinks
  
  def splitAddress(address):
      addressParts = address.replace("http://", "").split("/")
      return addressParts
  
  def getRandomExternalLink(startingPage):
      html = urlopen(startingPage)
      bsObj = BeautifulSoup(html, "html.parser")
      externalLinks = getExternalLinks(bsObj, splitAddress(startingPage)[0])
      if len(externalLinks) == 0:
          internalLinks = getInternalLinks(startingPage)
          return getNextExternalLink(internalLinks[random.randint(0, 
                                    len(internalLinks)-1)])
      else:
          return externalLinks[random.randint(0, len(externalLinks)-1)]
      
  def followExternalOnly(startingSite):
      externalLink = getRandomExternalLink("http://oreilly.com")
      print("Random external link is: "+externalLink)
      followExternalOnly(externalLink)
              
  followExternalOnly("http://oreilly.com")
  ```

- 获取链接

  ```python
  from urllib.request import urlopen
  from urllib.parse import urlparse
  from bs4 import BeautifulSoup
  import re
  import datetime
  import random
  
  pages = set()
  random.seed(datetime.datetime.now())
  
  #Retrieves a list of all Internal links found on a page
  def getInternalLinks(bsObj, includeUrl):
      includeUrl = urlparse(includeUrl).scheme+"://"+urlparse(includeUrl).netloc
      internalLinks = []
      #Finds all links that begin with a "/"
      for link in bsObj.findAll("a", href=re.compile("^(/|.*"+includeUrl+")")):
          if link.attrs['href'] is not None:
              if link.attrs['href'] not in internalLinks:
                  if(link.attrs['href'].startswith("/")):
                      internalLinks.append(includeUrl+link.attrs['href'])
                  else:
                      internalLinks.append(link.attrs['href'])
      return internalLinks
              
  #Retrieves a list of all external links found on a page
  def getExternalLinks(bsObj, excludeUrl):
      externalLinks = []
      #Finds all links that start with "http" or "www" that do
      #not contain the current URL
      for link in bsObj.findAll("a", href=re.compile(
                                  "^(http|www)((?!"+excludeUrl+").)*$")):
          if link.attrs['href'] is not None:
              if link.attrs['href'] not in externalLinks:
                  externalLinks.append(link.attrs['href'])
      return externalLinks
  
  def getRandomExternalLink(startingPage):
      html = urlopen(startingPage)
      bsObj = BeautifulSoup(html, "html.parser")
      externalLinks = getExternalLinks(bsObj, urlparse(startingPage).netloc)
      if len(externalLinks) == 0:
          print("No external links, looking around the site for one")
          domain = urlparse(startingPage).scheme+"://"+urlparse(startingPage).netloc
          internalLinks = getInternalLinks(bsObj, domain)
          return getRandomExternalLink(internalLinks[random.randint(0,len(internalLinks)-1)])
      else:
          return externalLinks[random.randint(0, len(externalLinks)-1)]
      
  def followExternalOnly(startingSite):
      externalLink = getRandomExternalLink(startingSite)
      print("Random external link is: "+externalLink)
      followExternalOnly(externalLink)
  
  followExternalOnly("http://oreilly.com")
  ```

- 示例

  ```python
  from urllib.request import urlopen
  from urllib.parse import urlparse
  from bs4 import BeautifulSoup
  import re
  import datetime
  import random
  
  pages = set()
  random.seed(datetime.datetime.now())
  
  #Retrieves a list of all Internal links found on a page
  def getInternalLinks(bsObj, includeUrl):
      includeUrl = urlparse(includeUrl).scheme+"://"+urlparse(includeUrl).netloc
      internalLinks = []
      #Finds all links that begin with a "/"
      for link in bsObj.findAll("a", href=re.compile("^(/|.*"+includeUrl+")")):
          if link.attrs['href'] is not None:
              if link.attrs['href'] not in internalLinks:
                  if(link.attrs['href'].startswith("/")):
                      internalLinks.append(includeUrl+link.attrs['href'])
                  else:
                      internalLinks.append(link.attrs['href'])
      return internalLinks
              
  #Retrieves a list of all external links found on a page
  def getExternalLinks(bsObj, excludeUrl):
      externalLinks = []
      #Finds all links that start with "http" or "www" that do
      #not contain the current URL
      for link in bsObj.findAll("a", href=re.compile("^(http|www)((?!"+excludeUrl+").)*$")):
          if link.attrs['href'] is not None:
              if link.attrs['href'] not in externalLinks:
                  externalLinks.append(link.attrs['href'])
      return externalLinks
  
  
  def getRandomExternalLink(startingPage):
      html = urlopen(startingPage)
      bsObj = BeautifulSoup(html, "html.parser")
      externalLinks = getExternalLinks(bsObj, urlparse(startingPage).netloc)
      if len(externalLinks) == 0:
          print("No external links, looking around the site for one")
          domain = urlparse(startingPage).scheme+"://"+urlparse(startingPage).netloc
          internalLinks = getInternalLinks(bsObj, domain)
          return getRandomExternalLink(internalLinks[random.randint(0,len(internalLinks)-1)])
      else:
          return externalLinks[random.randint(0, len(externalLinks)-1)]
      
  def followExternalOnly(startingSite):
      externalLink = getRandomExternalLink(startingSite)
      print("Random external link is: "+externalLink)
      followExternalOnly(externalLink)
              
  #Collects a list of all external URLs found on the site
  allExtLinks = set()
  allIntLinks = set()
  def getAllExternalLinks(siteUrl):
      html = urlopen(siteUrl)
      domain = urlparse(siteUrl).scheme+"://"+urlparse(siteUrl).netloc
      bsObj = BeautifulSoup(html, "html.parser")
      internalLinks = getInternalLinks(bsObj,domain)
      externalLinks = getExternalLinks(bsObj,domain)
  
      for link in externalLinks:
          if link not in allExtLinks:
              allExtLinks.add(link)
              print(link)
      for link in internalLinks:
          if link not in allIntLinks:
              allIntLinks.add(link)
              getAllExternalLinks(link)
  
  followExternalOnly("http://oreilly.com")
  
  allIntLinks.add("http://oreilly.com")
  getAllExternalLinks("http://oreilly.com")
  ```

  
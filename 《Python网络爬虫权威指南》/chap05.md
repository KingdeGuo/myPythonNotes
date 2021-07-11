[toc]

# Scrapy

- 使用`scrapy startroject proj_name`来对项目初始化

  可以得到以下目录结构

  ![image-20210711154659809](https://raw.githubusercontent.com/KingdeGuo/myPictureBed/main/img_upload202107/11/154712-684576.png)

- 为了创建爬虫，需要在`spiders`文件夹下创建一个新的文件。这里名命为`article.py`

  注意在下面的代码中

  `start_requests`是Scrapy定义的程序入口，用于生成用来抓取网站的Request对象

  `parse`是用户定义的一个回调函数

  ```python
  import scrapy
  
  class ArticleSpyder(scrapy.Spider):
      name = 'article'
      
      def start_requests(self):
          urls = [
                  'http://en.wikipedia.org/wiki/Python_'
              ]
          return [scrapy.Request(url=url, callback=self.parse) for url in urls]
      
      def parse(self, response):
          url = response.url
          title = response.css('h1::text').extract_first()
          print('URL is: {}'.format(url))
          print('Title isL {}'.format(title))
  ```

- 然后在虚拟环境中输入`scrapy runspider article.py`

  可以看到命令行有类似于如下的结果

  ![image-20210711160826886](https://raw.githubusercontent.com/KingdeGuo/myPictureBed/main/img_upload202107/11/160828-605802.png)

- 
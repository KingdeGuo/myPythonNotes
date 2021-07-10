[toc]

# 初见网络爬虫

- 一个实例及注释

  ```python
  # Python3中的用法
  from urllib.request import urlopen
  
  # urlopen() 用来获取并打开一个获取的远程对象
  html = urlopen("https://blog.csdn.net/weixin_44895666")
  # 注意此时获取到的该链接的html文件, 而没有包含这个页面里面的图片等
  # 这需要额外的代码来处理
  print(html.read())
  ```

- 使用BeautifulSoup()

  ```python
  BeautifulSoup(html.read(), 'html.parser')
  ```

  两个参数，一个是基于HTML的文本，另一个是解析器

  - 默认解析器是` html.parser`。无需额外单独安装。

  - `lxml`：额外安装，可以容忍并修复一些错误，例如标签没有闭合、未正确嵌套等。

    一般而言`lxml`可能会更快些，但是速度并不是必要优势。

  - `html5lib`容错性更好，但速度更慢.

  示例

  ```python
  # Python3中的用法
  from urllib.request import urlopen
  # 使用BeautifulSoup
  from bs4 import BeautifulSoup
  
  # urlopen() 用来获取并打开一个获取的远程对象
  html = urlopen("https://blog.csdn.net/weixin_44895666")
  # html = urlopen("http://www.kingdeguo.com")
  # 注意此时获取到的该链接的html文件, 而没有包含这个页面里面的图片等
  # 这需要额外的代码来处理
  # print(html.read())
  
  # 使用 html.parser 解析器
  # bs = BeautifulSoup(html.read(), 'html.parser')
  # 使用 lxml 解析器
  # bs = BeautifulSoup(html.read(), 'lxml')
  # 使用 html5lib 解析器
  bs = BeautifulSoup(html.read(), 'html5lib')
  # 通过bs.head.title来获取对象
  print(bs.head.title)
  ```

- 加入异常处理

  有以下几个地方可能需要处理

  - URL不存在
  - 对方服务器异常
  - 输出的像`bs.head.title`这样的标签不存在。

  代码示例

  ```python
  # Python3中的用法
  from urllib.request import urlopen
  # 使用BeautifulSoup
  from bs4 import BeautifulSoup
  # 加入异常处理
  from urllib.error import HTTPError
  from urllib.error import URLError
  
  try:
      # urlopen() 用来获取并打开一个获取的远程对象
      html = urlopen("https://blog.csdn.net/weixin_44895666")
      
      # 这是一个不存在的网站, 用于展示错误处理
      # html = urlopen("https://blog.kingdeguo.com")
      
      # html = urlopen("http://www.kingdeguo.com")
      # 注意此时获取到的该链接的html文件, 而没有包含这个页面里面的图片等
      # 这需要额外的代码来处理
      # print(html.read())
      
      # 使用 html.parser 解析器
      # bs = BeautifulSoup(html.read(), 'html.parser')
      # 使用 lxml 解析器
      # bs = BeautifulSoup(html.read(), 'lxml')
      # 使用 html5lib 解析器
      bs = BeautifulSoup(html.read(), 'html5lib')
      
      # 通过bs.head.title来获取对象
      try:
          title = bs.head.title
      except AttributeError as e:
          print("the tag don not exist")
      else:
          if title == None:
              print("tag was not found")
          else:
              print(title)
      
  except HTTPError as e:
      print(e)
  except URLError as e:
      print(e)
  else:
      print("It works")
  ```

- 写爬虫时，要注意让代码有好的可读性，同时又可以很好的处理异常。
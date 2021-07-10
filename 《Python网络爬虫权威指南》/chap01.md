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

- 
[toc]

# 复杂HTML解析

- 要尽量避免像`bs.find_all('table')[4].find_all('tr')[2]`这样的代码
  - 不美观
  - 当网站结构变化时，爬虫可能会失效

- CSS对爬虫来说是福音，可以根据class属性轻松的区分类似于这样的标签`<span class="class1"></span>`、`<span class="class2"></span>`

  示例

  ```python
  from urllib.request import urlopen
  from bs4 import BeautifulSoup
  
  html = urlopen("https://blog.csdn.net/weixin_44895666/article/details/115049190")
  
  bs = BeautifulSoup(html.read(), 'html5lib')
  
  # 借助 class属性 来查找
  # paras保存的是一个列表
  paras = bs.findAll('div', {'class':'markdown_views prism-tomorrow-night-eighties'})
  # 通过遍历列表来输出
  for para in paras:
      # 使用 get_Text() 函数来获取不包含html标签的部分
      print(para.get_text())
  ```

- `find()`和`find_all()`方法

  函数签名分别是

  ```python
  findAll(tag, attributes, recursive, text, limit, keywords)
  find(tag, attributes, recursive, text, keywords)
  ```

  举例

  ```python
  .find_all(['h1', 'h2', 'h3', 'h4', 'h5', 'h6'])
  .find_all('span', {'class':{'green', 'red'}})
  .find_all(text='the price')
  .find_all(id='title', class_='text')
  ```

  递归参数`recursive`是一个布尔属性，如果是`true`，就会根据要求去查找所有子标签，如果是`false`则只会查找一层。默认是支持递归的。

- 处理相关标签

  - 获取直接后代标签，使用`.children`

  - 使用`next_siblings`函数来处理兄弟标签。例如对于表格，选择标题行，然后调用函数，就可以选择表格中除标题之外的所有行。

    例如`bs.find('table', {'id':'gift}).tr.next_siblings`

  - 使用`.previous_siblings`可以获取之前的标签，类似的有`.next_sibling`和`.previous_sibling`

  - 使用`.parents`或者`.parent`获取父类标签

- 获取属性

  - 获取全部属性`myTag.attrs`
  - 获取单一属性`myTag.attrs['src']`

- 使用正则表达式

  关于正则表达式可以参考：[正则表达式](https://github.com/KingdeGuo/myEfficiencyToolsNotes/tree/main/myRegexNotes)

  代码示例

  ```python
  from urllib.request import urlopen
  from bs4 import BeautifulSoup
  import re
  
  html = urlopen("https://blog.csdn.net/weixin_44895666/article/details/115049190")
  
  bs = BeautifulSoup(html.read(), 'html5lib')
  
  # 使用正则表达式
  paras = bs.find_all('img', {'src':re.compile('https://csdnimg.cn*')})
  
  for para in paras:
      # 使用属性来打印
      print(para['src'])
  ```

- 使用lambda表达式

  - `labmda`本质上是一个函数，可以作为变量传入另一个函数

  - 一个函数不是定义为`f(x,y)`，而是可以定义为`f(g(x), y)`或`f(g(x), h(x))`的行式

  - 代码示例

    ```python
    # 获取属性长度为2的标签
    bs.find_all(lambda tag:len(tag.attrs) == 2)
    # 获取内容相关
    bs.find_all(lambda tag: tag.get_text() == 'hello world')
    ```


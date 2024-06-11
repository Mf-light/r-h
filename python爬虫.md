python爬虫

## 一、基础知识

1. 获取网页内容
   1. http请求： 学习requests库
      1. 方法：
         1. get方法：活得数据
         2. post方法：创建数据
2. 解析网页内容
   1. 学习html网页结构
   2. 学习beautiful soup库：解析获取到的html内容，提取信息
3. 储存或者分析数据

---

## 二、http协议：

(1) http、Hypertext Transfer Protocol，超文本传输协议http是一个基于“请求与响应”模式的、无状态的应用层协议

(2)基本格式：

 scheme://host[:post#]/path/……/[?query-string] [#anchor]

| 名称         | 解释                                             |
| ------------ | ------------------------------------------------ |
| scheme       | 协议（例如：http，https，ftp）                   |
| host         | 服务器的IP地址或者域名                           |
| port#        | 服务器的端口（如果是走协议默认端口，缺省端口80） |
| path         | 访问资源的路径                                   |
| query-string | 参数，发送给http服务器的数据                     |
| anchor       | 锚（跳转到网页的指定锚点位置）                   |

- 当获取的网页代码出现乱码时，可能就是编码格式不一样，手动设置编码格式： 请内容 . encoding=’ 编码格式 ‘
- URL是通过HTTP协议存取资源的Internet路径，一个URL对应一个数据资源

---

## 三、解析网页

### (1)  xpath的用法：

|  表达式  |                描述                |     用法     |                   说明                   |
| :------: | :--------------------------------: | :----------: | :--------------------------------------: |
| nodename |       选取此节点的所有子节点       |     div      |           选取div下的所有标签            |
|    //    | 从全局节点中选择节点，任意位置均可 |    //div     |      选取整个HTML页面的所有div标签       |
|    /     |       选取某个节点下 的节点        | //head/title |        选取head标签下的title标签         |
|    @     |         选取某个属性的节点         |  //div[@id]  |         选取带有id属性的div标签          |
|    .     |             当前节点下             |    ./span    | 选取当节点下的span标签【代码中威力强大】 |

1. etree.HTML(内容)：将不是html的格式的内容转换成html

2. etree.tostring(内容，encoding='UTF-8').decode('UTF-8'):如果不是UTF-8编码格式的内容，这里可以更改成UTF-8的内容

3. etree.parse(文件路径)：parse对html导入python并解析

4. 自定解析器：

   如果在浏览器上保存网页到本地，在python中获取.html文件需要利用自定解析器来解析文件内容

```python
# 自定解析器
parser=etree.HTMLParser(encoding='UTF-8')
html=etree.parse(路径,parser=parser)
result=etree.tostring(html,encoding='UTF-8').decode('UTF-8')
```

xpath中的[1]表示第一个元素，而python中的第一个是从0开始,例如：[0]

---

### (2)  bs4解析器的解释：

|     解析器      |                  使用方法                  |                             优势                             |                       劣势                        |
| :-------------: | :----------------------------------------: | :----------------------------------------------------------: | :-----------------------------------------------: |
|  python标准库   | soup = BeautifulSoup(htmlt, 'html.parser') |                python内置标准库;执行速度适中                 | python2.x或者python3.2x前的版本中文文档容错能力差 |
| lxml HTML解析器 |     soup = BeautifulSoup(html, 'lxml')     |                    速度快；文档容错能力强                    |                  需要安装c语言库                  |
| lxml XML解析器  |     soup = BeautifulSoup(html, 'xml')      |                 速度快；唯一支持XML的解析器                  |                  需要安装c语言库                  |
|    html5lib     |   soup = BeautifulSoup(html, 'html5lib')   | 最好的容错性；以浏览器的方式解析文档；生成HTML5格式的文档；不依赖外部扩展库 |                      速度慢                       |

可以使用列表的强制转换：

> （1）bs4的使用时，soup获取的时bs4的元素类型
>
> （2）使用list强制转换

1. 获取全部的单个标签：soup.find_all('标签')

2. 获取拥有指定属性的标签：

   1. soup.find_all('标签'，属性的键值对)
   2. soup.find_all('标签'，attrs={键值对1，键值对2})        attrs是存储的是字典，里面可以包含html的多个属性

3. 获取多个指定属性的标签：

   - soup.find_all('标签'，属性的键值对1，属性的键值对2)

   - 如果在获取时，出现python关键字与属性冲突时，在获取的时候添加一个下划线 ' _ '  ，例如：

     ```python
     soup.find_all('div'，class_='position')
     ```

4. 获取标签属性值：

​		方法1:通过下标方式提取

```python
1.先锁定标签
alist=soup.find_all('a')
for a in alist:
    href=a['href']
    print(href)
```

​		方法2：利用attrs参数提取

```python
for a in alist:
	href=a.attrs['href']
	print(href)
```

5. 获取标签内的文本信息:使用string方法

```python
# 获取html的所有div标签，从第二个开始
divs=soup.find_all('div')[1:]
# 利用循环输出每个标签
for div in divs:
    # 只提取标签下的字符串
    a=div.find_all('a')[0].string
    
# 提取整个div下的字符串
divs=soup.find_all('div')[1:]
for div in divs:
    infos=list(div.stripped_strings)  # stripped_strings方法是删除列表中的制表符，例如: "\n,\t"等
```

6. .get_text(strip=True):

   去除文本内容的前后空白

---

### (3)  python字符编码的错误：

​	UnicodeDecodeError: 'utf-8' codec can't decode byte 0xca in position 339: invalid continuation byte——错误的原因是，尝试将字节数据解码为UTF-8编码的字符串时，遇到了一个无效的起始字节。UTF-8是一种流行的Unicode字符编码，它使用1到4个字节表示不同的Unicode字符。如果字节数据不符合UTF-8编码规则，就会引发此错误。

解决方法：

 1. 尝试使用其他编码格式

 2. 使用错误处理策略

    'ignore' 是忽略无效字符

    ```python
    content = response.content.decode('UTF-8',errors='ignore')
    ```

 3. 尝试不同的编码方式

​	如果无法确定字节数据的正确编码方式，可以尝试使用不同的编码方式进行解码，直到找到一个可以成功解码的方式。可以使用try-except语句来捕获解码错误，并尝试不同的编码方式：

```python
pythonCopy codedata = b'\xbc\xde\xcf\xbc'
encodings = ['utf-8', 'gbk', 'latin-1']  # 按照可能的编码方式顺序进行尝试
decoded_data = None
# 找到合适的编码
for encoding in encodings:
    try:
        decoded_data = data.decode(encoding)
        break
    except UnicodeDecodeError:
        continue
# 如果找不到则为None      
if decoded_data is None:
    print("无法解码字节数据")
else:
    print(decoded_data)
```

4. 使用chardet库自动检测编码方式

如果无法确定字节数据的正确编码方式，可以使用chardet库来自动检测编码方式。chardet是一个第三方库，可以根据字节数据的特征自动检测编码方式。

安装库：

```
pythonCopy codepip install chardet
```

使用：

```python
pythonCopy codeimport chardet
data = b'\xbc\xde\xcf\xbc'
result = chardet.detect(data)
encoding = result['encoding']
decoded_data = data.decode(encoding)
```

排错：

在Python爬虫中，即使代码看起来没有明显语法错误，爬取的数据仍然可能为空，这通常与以下因素有关：

1. **目标网站结构改变**：
   - 如果爬虫是基于HTML结构编写的，而目标网站进行了改版或更新，原有的选择器（如XPath或CSS Selector）可能不再有效，导致找不到预期的数据。

2. **动态加载内容**：
   - 网页上的数据可能是通过JavaScript动态加载的，直接爬取HTML源代码可能无法获取这些数据。此时需要分析网页加载逻辑，使用如Selenium、Pyppeteer等工具模拟浏览器行为，或者通过分析Ajax请求来间接获取数据。

3. **反爬策略**：
   - 目标网站可能启用了反爬虫策略，比如Cookies验证、User-Agent限制、IP封锁、验证码、登录验证等。这时，需要针对这些策略进行相应的处理，比如设置更真实的User-Agent、使用代理IP池、处理验证码或模拟登录。

4. **请求参数不正确**：
   - 请求头信息（headers）、cookies、POST数据等参数可能需要特殊配置才能获取数据，如果缺少必要参数或参数不正确，服务器可能不会返回有效数据。

5. **网络问题**：
   - 即使代码看似没问题，网络连接不稳定或服务器端出现问题也可能导致无法获取数据。

6. **解析逻辑错误**：
   - 数据解析环节可能出现问题，例如正则表达式匹配不正确，或者在解析HTML或JSON时引用了不存在的键或属性。

7. **API调用权限或频率限制**：
   - 若爬取的是API接口，可能存在调用频率限制、API密钥失效或没有必要的授权。

8. **数据缓存问题**：
   - 如果爬虫有缓存机制并且缓存了错误的结果，新的爬取可能会直接读取缓存而非从服务器获取新数据。

要解决这个问题，可以从以下几个步骤入手：

- 检查并确认请求网址是否正确且能够正常访问；
- 使用开发者工具查看网页加载过程，确认数据是如何加载和呈现的；
- 检查请求头和请求体是否符合目标网站的要求；
- 检查解析代码逻辑，特别是提取数据的部分；
- 检测网络状况以及是否有反爬措施，调整爬虫策略；
- 对于动态加载内容，确保相应脚本能够正确执行或模拟；
- 针对可能出现的API限制，合理安排请求间隔，遵循网站的使用协议。

---



### (4) 正则表达式：

1. 语法：reslut=re.match('匹配内容'，内容文件)
   - 输出匹配：reslut.group()

```python
"""
    正则表达式，又称规则表达式（Regular Expression），是使用单个字符串来描述、匹配某个句法规则的字符串，常被用来检索、替换那些符合某个模式（规则）的文本
    正则的三个基础方法
        import re
        1.1从头开始搜索，开头第一个如果不匹配则匹配不成功
            re.match(匹配规则， 被匹配字符串)
        1.2 搜索整个容器，匹配则成功，不匹配返回None
            re.search(匹配规则， 被匹配字符串)
        1.3 匹配整个字符串，找出全部匹配项
            re.findall(匹配规则， 被匹配字符串)
        1.4 字符串的 r 标记，表示当前字符串是原始字符串，即内部的转义字符无效而是普通字符
        1.5 数量匹配：
            （1）* ：匹配前一个规则的字符出现0至无数次
            （2）+ ：匹配前一个规则的字符出现1至无数次 就是至少有一次
            （3）？ ：匹配前一个规则的字符出现0次或者1次
            （4）{m}:匹配前一个规则的字符出现m次
            （5）{m,}：匹配前一个规则的字符出现最少m次
            （6）{m,n}：匹配前一个规则的字符出现m到n次
        1.6 边界匹配
            （1）^ :匹配字符串开头
            （2）$ :匹配字符串结尾
            （3）\b :匹配一个单词的边界
            （4）\B :匹配非单词边界
       1.7 分组匹配
            （1）| :匹配左右任意一个表达式
            （2）() :将括号字符串作为一个分组
"""
import re
# 练习
s = " itheima1. @ @ python2 !!666  ##itcast3"
# 匹配 . 使用\.
re1=re.findall(r'\.',s)
print(re1)
# 自定义匹配字符 使用[ ]
re2=re.findall(r'[A-Z,\W]',s)
print(re2)
# 匹配整数 使用\d
re3=re.findall(r'\d',s)
print(re3)
# 匹配非数字 使用\D
re4=re.findall(r'\D',s)
print(re4)
# 匹配空格 和tab 使用 \s
re5=re.findall(r'\s',s)
print(re5)
# 匹配非空白 \S
re6=re.findall(r'\S',s)
print(re6)
# 匹配单字符 如：a-z A-Z 0—9 _\
re7=re.findall(r'\w',s)
print(re7)
# 匹配非单词字符 \W
re8=re.findall(r'\W',s)
print(re8)
# 匹配账号，只能由字母和数字组成，长度限制6到10位
# 规则为： ^[0-9a-zA-Z]{6, 10}$
# 匹配QQ号，要求纯数字，长度5-11，第一位不为0
# 规则为：^[1-9][0-9]{4, 10}&
# [1-9]匹配第一位，[0-9]匹配后面4到10位
```



---

### (5) 拓展知识

1. 爬取图片是不需要解码的

```python
content=response.content
```

2. 保存数据：wb是写入比特流数值

```python
with open('路径','wb') as f:
	f.write(content)
```

----

## 四、Requests库

[Python123](https://python123.io/student/courses/10673/groups)

`学习流程图`：

![image-20240506202700278](C:\Users\29496\AppData\Roaming\Typora\typora-user-images\image-20240506202700278.png)

---

`python的集成工具：`

| 文本工具类IDE |   集成工具类IDE   |
| :-----------: | :---------------: |
|     IDLE      |      Pycharm      |
|   Notepad++   |       Wing        |
| Sublime Text  |  PyDev & Eclipse  |
|  Vim & Emacs  |   Visual Studio   |
|     Atom      | Anaconda & Spyder |
|  Komodo Edit  |      Canopy       |

推荐使用的是：Sublime Text 、Wing 、Pycharm 、Anaconda (科学计算机和数据分析的专题)

### (1) Requests

- 自动爬取HTML页面，自动网络请求提交

- 最常用的是get请求方法

|        方法        | 说明                                           |
| :----------------: | :--------------------------------------------- |
| requests.request() | 构造一个请求，支撑以下各方法的基础方法         |
|   requests.get()   | 获取html网页的主要方法，对应于http的get        |
|  requests.head()   | 获取html网页头信息的方法，对应于http的head     |
|  requests.post()   | 向html网页提交post请求的方法，对应于http的post |
|   requests.put()   | 向html网页提交put请求的方法，对应于http的put   |
|  requests.patch()  | 向html网页提交局部修改请求，对应于http的patch  |
| requests.delete()  | 向html页面提交删除请求，对应http的delete       |

- 理解patch和put的区别

  ```python
  '''
  假设url位置有一组数据UserInfo，包括UserID、UserName等20个字段
  需求：用户修改了UserName，其他不变
  - 采用patch，仅向url提交userName的局部更新请求
  - 采用put，必须将所有20个字段一并提交到url，未提交字段被删除
  patch的最主要好处：节省网络带宽
  '''
  ```

  

  

### (2) Response对象的属性

- 请求：获取html网页

|        属性         | 说明                                                         |
| :-----------------: | :----------------------------------------------------------- |
|    r.statu_code     | http请求的返回状态，200表示连接成功，其他表示失败或者另有原因 |
|       r.text        | http响应内容的字符串形式，即，url对应的页面内容              |
|     r.encoding      | 从http header中猜测的响应内容编码方式                        |
| r.apparent_encoding | 从内容中分析出的响应内容编码方式(备选编码方式)               |
|      r.content      | http响应内容的二进制形式                                     |



### (3) 理解Requests库的异常：

- 网络连接有风险

- 异常处理很重要


| 异常                              | 说明                                          |
| --------------------------------- | --------------------------------------------- |
| requests.ConnectionError          | 网络连接错位于异常，如DNS查询失败，拒绝连接等 |
| requests.HTTPError                | http错误异常                                  |
| requests.URLRequired              | url缺失异常                                   |
| requests.TooManyRedirects         | 超过最大重定向次数，产生重定向异常            |
| requests.ConnectTimeout           | 连接远程服务器超时异常                        |
| requests.Timeout                  | 请求URL超时，产生超时异常                     |
| r.raise_for_status() 【response】 | 如果不是200，产生异常requests.HTTPError       |

- 使用try……except排错框架

```bash
import requests

def getHTMLText(url):
    try:
        r=requests.get(url,timeout=30)
        # 如果状态不是200，引发HTTPError异常
        r.raise_for_status()
        r.encoding=r.apparent_encoding
        return r.text
    except:
        return "产生异常"

if __name__ == "__mian__":
    url="http://www.baidu.com"
    print(getHTMLText(url))
```

---

### (4) 请求数据 (** kwargs参数)

- requests.request(method,url,** kwargs)

  `**kwargs:控制访问的参数，均为可选项`

  method：请求方式

|      | 参数            | 说明                                                         |
| :--: | --------------- | ------------------------------------------------------------ |
|  1   | params          | 跟在url连接后面，查询（搜索）的含义，字典或者字流格式        |
|  2   | data            | 终点作为向服务器提供或提交资源时使用，字典、字节序列或文件对象，作为Request的内容 |
|  3   | json            | JSON格式的数据，作为Request的内容                            |
|  4   | headers         | 字典、HTTP定制头                                             |
|  5   | cookies         | 字典或CookieJar，Request中的cookie                           |
|  6   | auth            | 元组，支持HTTP认证功能                                       |
|  7   | files           | 字典类型，传输文件                                           |
|  8   | timeout         | 设定超时时间，秒为单位                                       |
|  9   | poroxies        | 字典类型，设定访问代理服务器，可以增加登录认证               |
|  10  | allow_redirects | True/False,默认为True，重定向开关                            |
|  11  | stream          | True/False,默认为True，获取内容立即下载开关                  |
|  12  | verify          | True/False,默认为True，认证SSL证书开关                       |
|  13  | cert            | 本地SSL证书路径                                              |

1. params

```python
import requests
url = "https://example.com/search"
params = {
    "query": "Python爬虫",
    "page": 1
}
response = requests.get(url, params=params) # 最终得到url = https://example.com/search?query=Python爬虫&page=1
```

2. data

```python
import requests
url = "https://example.com/login"
# 相当于填写表单数据，如登录表单，post请求
data = {
    "username": "your_username",
    "password": "your_password"
}
response = requests.post(url, data=data) 
```

3. json

   `json`参数的作用是简化向API发送JSON数据的过程，确保了数据的正确序列化和HTTP头部的恰当设置，非常适合与那些期望接收JSON输入的现代Web服务交互。

```python
import requests
import json
url = "https://api.example.com/data"
data = {
    "key": "value",
    "another_key": "another_value"
}
response = requests.post(url, json=data)
# 注意：requests库内部会自动将data转换为JSON字符串，
# 并设置Content-Type为application/json
# data是一个Python字典，通过json=data传递给requests.post方法后，requests会将其转换为JSON字符串{"key": "value", "another_key": "another_value"}并设置请求头，以表明发送的是JSON格式的数据。
```

4. headers

   实际上是http头的相关域，它对应了向某一个url访问时所发起的http头字段，利用这个字段定制某个访问url的http的协议头

   - `User-Agent`: 指定客户端的信息，很多网站会根据这个字段判断访问者是浏览器还是爬虫，有时需要将其设置为常见的浏览器字符串来避免被识别为爬虫。
   - `Accept-Language`: 指定客户端接受的语言种类，可以帮助获取特定语言的网页内容。
   - `Content-Type`: 当发送POST请求且包含请求体时，这个字段指定了数据的格式，如`application/x-www-form-urlencoded`、`application/json`等。
   - `Authorization`: 如果网站需要认证，可以通过这个字段提供Token或其他认证信息。

   ```python
   import requests
   url = "https://example.com"
   headers = {
       "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3",
       "Accept-Language": "en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7",
   }
   response = requests.get(url, headers=headers) # 模仿浏览器，请求数据
   ```

5. cookies

   从http中解析cookies，它可以是字典，也可以是cookieJar形式；

   解释：通过`cookies`参数携带相应的cookie信息，可以使爬虫模拟已登录用户的行为，访问那些需要登录后才能查看的内容。这在爬取需要身份验证的网站数据时尤为重要。

   作用：`cookies`参数在Python爬虫中的作用是模拟浏览器的cookie机制，帮助爬虫程序绕过登录限制，访问受保护的内容，或是维持与服务器的会话状态，提高数据抓取的准确性和效率。

   ```python
   import requests
   url = "https://example.com/userinfo"
   cookies = {
       "sessionid": "abcdef123456",
       "user": "example_user"
   }
   response = requests.get(url, cookies=cookies)
   ```

6. auth

   字段是一个元组类型，它是支持http认证功能的，`auth`参数可以是一个元组，通常包含用户名和密码，或者是一个`AuthBase`的子类实例，用于自定义认证方案。

   ```python
   # 基本HTTP认证
   from requests.auth import HTTPBasicAuth
   url = "https://api.example.com/private"
   username = "your_username"
   password = "your_password"
   response = requests.get(url, auth=HTTPBasicAuth(username, password))
   
   # API密钥认证
   class APITokenAuth(requests.auth.AuthBase):
       def __init__(self, token):
           self.token = token
       def __call__(self, r):
           r.headers['Authorization'] = f'Token {self.token}'
           return r
   url = "https://api.example.com/data"
   token = "your_api_token"
   response = requests.get(url, auth=APITokenAuth(token))
   
   # OAuth认证
   对于支持OAuth的API，虽然直接通过auth参数处理可能较为复杂（通常需要先通过一系列步骤获取访问令牌），但也可以根据具体流程封装认证逻辑到自定义的AuthBase子类中。
   通过合理使用auth参数，Python爬虫能够安全有效地访问那些需要认证的资源，确保了数据请求的合法性与安全性。
   ```

   

7. files

   如何使用`files`参数上传一个图片文件

   ```python
   import requests
   url = "https://example.com/upload"
   file_path = "/path/to/your/image.jpg"
   with open(file_path, 'rb') as file:
       files = {'image': (file_path, file, 'image/jpeg')}  # 文件名，文件对象，MIME类型
       response = requests.post(url, files=files)
   print(response.text)
   ```

   在这个例子中，我们首先打开要上传的图片文件，并以二进制模式读取(`'rb'`)。然后，我们将文件信息构造成一个字典，其中键 `'image'` 是服务器端预期接收文件的字段名，值是一个元组，包含文件名（这里也可以是任意字符串，服务器端可能会用作文件名）、文件对象和文件的MIME类型。最后，通过`requests.post()`方法发送POST请求，并将这个字典作为`files`参数传入。

   `files`参数的使用让Python爬虫能够执行涉及文件上传的任务，如图片上传、文件分享网站的数据抓取等场景。

8. timeout

   用于设置网络请求的超时时间，如果一个请求超过指定的秒数还没有得到响应，`requests`库将会抛出一个异常，而不是无限期地等待下去

   ```python
   import requests
   url = "https://example.com"
   timeout = 5  # 设置超时时间为5秒
   try:
       response = requests.get(url, timeout=timeout)
       # 处理响应数据
   except requests.exceptions.Timeout:
       # 超时处理逻辑
       print("请求超时")
   # 请求https://example.com在5秒内没有得到服务器的响应，程序将不会一直等待，而是立即执行except块中的代码，打印出“请求超时”的信息
   ```

9. proxies

   解释：`proxies` 参数用于配置HTTP或HTTPS代理服务器。代理服务器作为中间人，可以接收你的爬虫程序发出的网络请求，然后转发给目标服务器，并将响应结果再返回给你的爬虫。

   目的：

   1. **匿名性**：隐藏真实IP地址，防止被目标网站识别和封锁，尤其是在进行大量请求时，减少被封禁的风险。
   2. **地域限制绕过**：通过选择不同地区的代理服务器，可以访问地理位置受限的内容或服务，比如某些网站仅对特定国家或地区开放。
   3. **性能优化**：如果目标服务器对你的物理位置响应较慢，使用地理位置更近的代理服务器可以加快访问速度。
   4. **负载均衡和带宽管理**：企业级应用中，可能会利用代理服务器来分配请求，优化网络资源使用。

   ```python
   import requests
   proxies = {
       "http": "http://代理服务器地址:端口",
       "https": "https://代理服务器地址:端口",
   }
   response = requests.get("http://example.com", proxies=proxies)
   ```

   代码解释：`proxies`参数是一个字典，其中键为协议名（"http" 或 "https"），值为代理服务器的URL（包括协议、地址和端口）。这样，所有通过`requests`发起的请求都会通过指定的代理服务器进行。

   注意：使用代理时应遵守目标网站的使用条款和服务协议，合法合规地进行数据抓取，尊重网站的Robots协议，并尽量减少对目标服务器的负担。同时，选择稳定可靠的代理服务对于爬虫的成功运行至关重要。

   

10. allow_redirects

    `requests`库会自动处理重定向，即自动向新的URL发送请求。当设置为`False`时，则不自动处理重定向，而是直接返回原始的重定向响应。

```python
import requests
# 允许重定向
response = requests.get('http://example.com/redirect', allow_redirects=True)
print(response.url)  # 最终重定向后的URL
# 禁止重定向
response = requests.get('http://example.com/redirect', allow_redirects=False)
print(response.status_code)  # 可能会得到一个重定向的状态码，如301或302
print(response.headers['location'])  # 获取重定向的目标URL，而不是自动访问
```

11. stream

    - 解释：在使用Python的`requests`库进行网络请求时，`stream`参数是一个非常实用的选项，它的主要作用是控制是否立即下载响应内容。当设置`stream=True`时，`requests`不会立即下载整个响应体，而是等到你需要时才按需读取，这对于大文件下载或者仅需处理部分响应内容的场景非常有用
    - `stream`参数的作用：
      1. 节省内存：对于大型文件的下载，如果直接下载整个响应体到内存中，可能会消耗大量内存资源。使用`stream=True`可以让数据边下载边处理，减少内存占用。
      2. 按需读取：当你只想读取响应的一部分内容，而不是全部时，使用流式处理可以更加高效。例如，你可能只需要检查响应的前几行来决定是否继续下载剩余内容。
      3. 长时间运行的连接：在某些情况下，保持连接打开并逐步处理响应内容是有益的，比如实时数据流处理。

    ```python
    import requests
    url = "http://example.com/large_file.zip"
    response = requests.get(url, stream=True)
    # 检查请求是否成功
    if response.status_code == 200:
        # 打开一个本地文件用于保存下载的内容
        with open('large_file.zip', 'wb') as f:
            for chunk in response.iter_content(chunk_size=1024): 
                # 如果chunk不是空的，才写入文件
                if chunk: 
                    f.write(chunk)
    ```

12. verify

    - `verify=True`，这意味着`requests`会验证服务器的SSL证书，确保与之建立的HTTPS连接是安全的，可以防止中间人攻击。
    - 有时候你可能需要关闭这个验证，比如在测试环境中，或者当遇到自签名证书（self-signed certificate）或证书链不完整的情况，这时可以将`verify`设置为`False`。不过，这样做会降低安全性，应该谨慎考虑，并仅在确信不会导致安全问题的情况下使用。

    ```python
    import requests
    url = "https://example.com"
    response = requests.get(url, verify=False)
    ```

13. cert

    - `cert`参数可以接收一个表示客户端证书文件路径的字符串，或者一个包含证书文件路径和私钥文件路径的元组。
    - `cert` 参数用于指定HTTPS请求时的客户端证书。当目标网站或API需要客户端提供安全证书进行身份验证时，就需要用到这个参数。这对于访问那些启用了客户端证书认证的HTTPS服务尤为重要，比如一些内部系统、银行接口或是高度安全的API。

    ```python
    import requests
    url = "https://example.com/api/secure-endpoint"
    cert = "/path/to/client.pem"  # 单个文件包含证书和私钥
    # 或者，如果证书和私钥分开：
    # cert = ("/path/to/cert.pem", "/path/to/key.pem")
    response = requests.get(url, cert=cert)
    ```

---

## 五、网络爬虫的“盗亦有道”

### (1) 网络爬虫的尺寸

| 1                                           | 2                                        | 3                                    |
| :------------------------------------------ | ---------------------------------------- | ------------------------------------ |
| 小规模，数据量爬取速度不敏感Requests库 >90% | 中规模，数据规模较大爬取速度敏感Scrapy库 | 大规模，搜索引擎爬取速度关键定制开发 |
| 爬取网页  玩转网页                          | 爬取网站 爬取系列网站                    | 爬取全网                             |



### (2) 网络爬虫引发的问题

1. 骚扰问题

   网络爬虫的“性能骚扰”：web服务器默认接收人类访问。受限于编写水平和目的，网络爬虫将会为Web服务带来巨大的资源开销

2. 法律风险

   服务器上的数据有产权归属，网络爬虫获取数据后牟利将带来法律风险

3. 隐私泄露

   网络爬虫可能具备突破简单访问控制的能力，获得被保护数据从而泄露个人隐私







### (3) 网络爬虫的限制

- 来源审查：判断User-Agent进行限制
  - 检查来访Http协议头的User-Agent域，只响应浏览器或友好爬虫的访问
- 发布公告：Robots协议
  - 告知所有爬虫网站的爬取策略，要求爬虫遵守





### (4) Robots协议

- Robots（Robots Exclusion Standard，网络爬虫排除标准协议）是机器人的意思。robots协议是放在网站的根目录上的，如果一个网站无robots协议，则是该网站允许所有爬虫爬取该网站，所有的爬虫都要遵循robots协议。

- 作用：网站告知网络爬虫哪些页面可以抓取，哪些不行
- 形式：在网站根目录下的robots.txt文件
- robots协议基本语法：

```python
# disallow：表示不允许爬取的内容
# *代表所有，/代表根目录
```



- Robots协议的使用
  	1. 网络爬虫：自动或人工识别robots.txt，再进行内容爬取。
  	1. 约束性：Robots协议是建议但非约束性，网络爬虫可以不遵守，但存在法律风险。

### (5) 对Robots协议的理解

| 1                                            | 2                                            | 3        |
| -------------------------------------------- | -------------------------------------------- | -------- |
| 访问量很小：可以遵守 ； 访问量较大：建议遵守 | 非商业且偶尔：建议遵守 ； 商业利益：必须遵守 | 必须遵守 |
| 爬取网页 玩转网页                            | 爬取网站 爬取系列网站                        | 爬取全网 |



例如：

```bash
http://www.baidu.com/robots.txt
http://www.news,sina.com.cn/robots.txt
http://www.qq.com/robots.txt
http://news.qq.com/robots.txt
http://www.moe.edu.cn/robots.txt (无robots协议)
```





## 六、实战案例

### (1) 京东商品页面的爬取

```python
import requests
url="https://item.jd.com/100077414769.html"
try:
    r=requests.get(url)
    r.raise_for_status() # 如果状态码是200则正常运行
    r.encoding=r.apparent_encoding
    print(r.text[:1000])
except:
    print("爬取失败")
```



### (2) 亚马逊商品页面的爬取

```python
import requests

url="https://www.amazon.cn/gp/product/B01M8L5Z3Y"
headers={
    "User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36"
}
try:
    r=requests.get(url,headers=headers)
    r.raise_for_status() # 如果状态码是200则正常运行
    r.encoding=r.apparent_encoding
    print(r.text)
except:
    print("爬取失败")
```



### (3) 百度360搜索关键词提交

- 百度的关键词接口：

http://www.baidu.com/s?wd=`keyword`

```python
import requests

url="http://www.baidu.com/s"
try:
    kv={
        'wd':'Python'
    }
    r=requests.get(url,params=kv)
    print(r.request.url)
    r.raise_for_status() # 如果状态码是200则正常运行
    print(r.request.url)
    print(r.text)
except:
    print("爬取失败")
```

- 360的关键词接口

http://www.so.com/s?q=`keyword`

```python
import requests

url="http://www.so.com/s"
try:
    kv={
        'q':'Python'
    }
    r=requests.get(url,params=kv)
    print(r.request.url)
    r.raise_for_status() # 如果状态码是200则正常运行
    print(r.text)
except:
    print("爬取失败")
```

### (4) 网络图片的爬取和存储

```python
import requests
import os
url="http://image.nationalgeograhic.com.cn/2017/20170211061910157.jpg"
root="F://music//"
path = root +url.split('/')[-1]
try:
    if not os.path.exists(root):
        os.mkdir(root)
    if not os.path.exists(path):
        r= requests.get(url)
        with open(path,"wb") as f:
            f.write(r.content)
            f.close()
            print("文件保存成功")
    else:
        print("文件已存在")
except:
    print("爬取失败")
```

### (5) ip地址归属地的自动获取

```python
import requests

url="http://m.ip138.com/ip.asp?ip="
try:
    r=requests.get(url+'202.204.80.112')
    r.raise_for_status()
    r.encoding=r.apparent_encoding
    print(r.text[-500:])
except:
    print("爬取失败")
```



## 七、Beautiful Soup库入门

### (1) 标签基本元素



| 基本元素        | 说明                                                    |
| --------------- | ------------------------------------------------------- |
| Tag             | 标签，最基本的信息组织单元，分别用<>和</>标明开头和结尾 |
| Name            | 标签的名字，<p>……</p>的名字是‘p’，格式：<tag>.name      |
| Attributes      | 标签的属性，字典形式组织，格式：<tag>.attrs             |
| NavigableString | 标签内非属性字符串，<>……</>中字符串，格式：<tag>.string |
| Comment         | 标签内字符串的注释部分，一种特殊的Comment类型           |



1. 最基本的信息单元

   ```python
   import requests
   from bs4 import BeautifulSoup
   url="http://python123.io/ws/demo.html"
   try:
       response=requests.get(url)
       response.raise_for_status()
       html=response.text
       soup=BeautifulSoup(html,"html.parser")
       title=soup.title
       a=soup.a
       print(title,a)
   except:
       print("爬取失败")
   ```

   

2. 标签的名字

   ```python
   import requests
   from bs4 import BeautifulSoup
   url="http://python123.io/ws/demo.html"
   try:
       response=requests.get(url)
       response.raise_for_status()
       html=response.text
       soup=BeautifulSoup(html,"html.parser")
       title=soup.title.name # 获取标签的尖括号里面的内容
       print(title)
   except:
       print("爬取失败")
   ```

3. 标签属性

   ```python
   import requests
   from bs4 import BeautifulSoup
   url="http://python123.io/ws/demo.html"
   try:
       response=requests.get(url)
       response.raise_for_status()
       html=response.text
       soup=BeautifulSoup(html,"html.parser")
       a=soup.a
       id=a.attrs # 查看a标签里的属性
       id_class=a.attrs['href'] # 获取属性的键值对，就是属性内容
       print(id_class)
   except:
       print("爬取失败")
   ```

4. 标签内非属性字符串

   ```python
   import requests
   from bs4 import BeautifulSoup
   url="http://python123.io/ws/demo.html"
   try:
       response=requests.get(url)
       response.raise_for_status()
       html=response.text
       soup=BeautifulSoup(html,"html.parser")
       p=soup.p.string
       print(p)
   except:
       print("爬取失败")
   ```

5. 标签内字符串的注释部分

   ```python
   from bs4 import BeautifulSoup
       soup=BeautifulSoup("<b><!--This is a comment--></b><p>This is not a comment</p>","html.parser")
       print(type(soup.b.string))
   except:
       print("爬取失败")
   ```



### (2) 标签树下的下行遍历

| 属性         | 说明                                                    |
| ------------ | ------------------------------------------------------- |
| .contents    | 子节点的列表，将<tag>所有儿子节点存入列表               |
| .children    | 子节点的迭代类型，与.contents类似，用于循环遍历儿子节点 |
| .descendants | 子孙节点的迭代类型，包含所有子孙节点，用于循环遍历      |

1. 遍历下行

   ```python
   import requests
   from bs4 import BeautifulSoup
   url="http://python123.io/ws/demo.html"
   response=requests.get(url)
   soup=BeautifulSoup(response.text,"html.parser")
   head=soup.head.contents
   body=soup.body.contents
   print(head)
   print(body)
   ```

2. 遍历下行

   ```python
   import requests
   from bs4 import BeautifulSoup
   url="http://python123.io/ws/demo.html"
   response=requests.get(url)
   soup=BeautifulSoup(response.text,"html.parser")
   # 遍历儿子节点
   for child in soup.body.children:
       print(child)
   # 遍历子孙节点
   for child in soup.body.descendants:
       print(child)
   ```

### (3) 标签树的上行遍历

| 属性     | 说明                                     |
| -------- | ---------------------------------------- |
| .parent  | 节点的父亲标签                           |
| .parents | 节点先辈标签的迭代类型，用于循环遍历辈点 |

```python
import requests
from bs4 import BeautifulSoup
url="http://python123.io/ws/demo.html"
response=requests.get(url)
soup=BeautifulSoup(response.text,"html.parser")
title=soup.title.parent
print(title)
for parent in soup.a.parents:
    if parent is None:
        print(parent)
    else:
        print(parent.name)
```

### (4) 标签树的平衡遍历

| 属性               | 说明                                                 |
| ------------------ | ---------------------------------------------------- |
| .next_sibling      | 返回按照HTML文本顺序的下一个平行节点标签             |
| .previous_sibling  | 返回按照HTML文本顺序的上一个平行节点标签             |
| .next_siblings     | 迭代类型，返回按照HTML文本顺序的后续所有平行节点标签 |
| .previous_siblings | 迭代类型，返回按照HTML文本顺序的前续所有平行节点标签 |

```python
import requests
from bs4 import BeautifulSoup
url="http://python123.io/ws/demo.html"
response=requests.get(url)
soup=BeautifulSoup(response.text,"html.parser")
a0=soup.a.next_sibling
print(a0)
a1=soup.a.next_sibling.next_sibling
print(a1)
a2=soup.a.previous_sibling
print(a2)
a3=soup.a.previous_sibling.previous_sibling
print(a3)
a4=soup.a.parent
print(a4)
# 遍历后续节点
for sibling in soup.a.next_sibling:
    print(sibling)
# 遍历前续节点
for sibling in soup.a.previous_sibling:
    print(sibling)
```

- .prettify()为HTML文本<>及其内容增加更加”\n“

- .prettofuy()可用与标签，方法：<tag>.prettift()

```python
import requests
from bs4 import BeautifulSoup
url="http://python123.io/ws/demo.html"
response=requests.get(url)
soup=BeautifulSoup(response.text,"html.parser")
print(soup.prettify())
```

## 八、信息组织与提取方法

### (1) 信息标记的三种形式

json

```json
"key":"value"
"key":["value1","value2"]
"key":{"subkey":"subvalue"}
```

- 信息有类型，适合程序处理(js)，较XML简洁
- 移动应用云端和节点的信息通信，无注释

yaml

```yaml
key:vlaue
key:#Comment
-vlaue1
-vlaue2
key:
	subkey:subvlaue
```

- 信息无类型，文本信息比例最高，可读性好
- 各类系统的配置文件，有注释易读

xml

```xml
<name>……</name>
注释：<!-- -->
```

- 最早的通用信息标记语言，可扩展性好，但繁琐。
- Internet上的信息交互与传递



### (2) 信息提取的一般方法

- 方法一：

  完整解析信息的标记形式，再提取关键信息。XML、JSON、YAML ，需要标记解析器；

  优点：信息解析准确；缺点：提取过程繁琐，速度慢。

- 方法二：

  无视标记形式，直接搜索关键信息。对信息的文本查找函数即可。

  优点：提取过程简洁，速度较快。缺点：提取结果准确性与信息内容相关

- 融合方法：

  结合形式解析与搜索方法，提取关键信息。XML、JSON、YAML、搜索。需要标记解析器及文本查找函数。

### (3) find_all()方法

```python
<>.find_all(name,attrs,recursive,string,**kwargs)
"""
返回一个列表类型，存储查找的结果
name：对标签名称的检索字符串
attrs：对标签属性值的检索字符串，可标注属性检索
recursive：是否对子孙全部检索，默认True
string：<>……</>中字符串区域的检索字符串
"""
```

1. name：

```python
import requests
from bs4 import BeautifulSoup
r=requests.get("http://python123.io/ws/demo.html").text
soup=BeautifulSoup(r,"html.parser")
# 单个标签查找
a=soup.find_all('a')
# 多个标签查找用列表
b=soup.find_all(['a','b'])
```

```python
import requests
from bs4 import BeautifulSoup
r=requests.get("http://python123.io/ws/demo.html").text
soup=BeautifulSoup(r,"html.parser")
for tag in soup.find_all(True):
    print(tag.name)

# 返回结果
"""
html
head
title
body
p
b
p
a
a
"""
```

2. attrs

```python
import requests
from bs4 import BeautifulSoup
r=requests.get("http://python123.io/ws/demo.html").text
soup=BeautifulSoup(r,"html.parser")
# 属性的查找
link1=soup.find_all(id=['link1','class'])
print(link1)
p=soup.find_all('p','course')
print(p)
```

```python
import requests
from bs4 import BeautifulSoup
import re
r=requests.get("http://python123.io/ws/demo.html").text
soup=BeautifulSoup(r,"html.parser")
# 利用正则表达式的函数compile搜索属性单词搜索
link=soup.find_all(id=re.compile('lin'))
print(link)
```

3. recursive

```python
import requests
from bs4 import BeautifulSoup
import re
r=requests.get("http://python123.io/ws/demo.html").text
soup=BeautifulSoup(r,"html.parser")
# 是否对子孙全部检索
a=soup.find_all('a',recursive=False)
print(a)
```

4. string

```python
import requests
from bs4 import BeautifulSoup
import re
r=requests.get("http://python123.io/ws/demo.html").text
soup=BeautifulSoup(r,"html.parser")
# 搜索字符串
str=soup.find_all(string='Basic Python')
print(str)
# 利用正则表达式搜索关键词
python=soup.find_all(string=re.compile('py'))
print(python)
```

5. 拓展：

| 方法                        | 说明                                                      |
| --------------------------- | --------------------------------------------------------- |
| <>.find()                   | 搜索且只返回一个结果，字符串类型，同.find_all参数         |
| <>.find_parents()           | 在先辈节点中搜索，返回列表类型，同.find_all参数           |
| <>.find_parent()            | 在先辈节点中返回一个结果，字符串类型，同.find_all参数     |
| <>.find_next_siblings()     | 在后续平行节点中搜索，返回列表类型，同.find_all参数       |
| <>.find_next_sibling()      | 在后续平行节点中返回一个结果，字符串类型，同.find_all参数 |
| <>.find_previous_siblings() | 在前序平行节点中搜索，返回列表类型，同.find_all参数       |
| <>.find_previous_sibling()  | 在前序平行节点中返回一个结果，字符串类型，同.find_all参数 |

提取属性的方法：

```python
import requests
from bs4 import BeautifulSoup
r=requests.get("http://python123.io/ws/demo.html").text
soup=BeautifulSoup(r,"html.parser")
for link in soup.find_all('a'):
    """
    提取属性的三种方法
    """
    print(link['href'])
    print(link.get('href'))
    print(link.attrs['href'])
```

`特`：soup(…) 等价于 soup.find_all(…)



## 九、实例：中国大学排名爬虫



### (1) 程序的结构设计

- 步骤1：从网络上获取大学排名网页内容——getHTMLText()
- 步骤2：提取网页内容中信息到合适的数据结构——fillUnivList()
- 步骤3：利用数据结构展示并输出结果——printUnivList()

[【软科排名】2024年最新软科中国大学排名|中国最好大学排名 (shanghairanking.cn)](https://www.shanghairanking.cn/rankings/bcur/2024)

### (2) 中文对齐的问题的原因

![image-20240520100132695](C:\Users\29496\AppData\Roaming\Typora\typora-user-images\image-20240520100132695.png)

|    :     |       <填充>       |            <对齐>             |      <宽度>      |                  ,                   |                  <.精度>                   |                <类型>                |
| :------: | :----------------: | :---------------------------: | :--------------: | :----------------------------------: | :----------------------------------------: | :----------------------------------: |
| 引导符号 | 用于填充的单个字符 | <左对齐 ；> 右对齐；^剧中对齐 | 槽的设定输出宽度 | 数字的千位分隔符，适用于整数和浮点数 | 浮点数小数部分的精度或字符串的最大输出长度 | 整数类型b,c,d,o,x,X浮点数类型e,E,f,% |

- 中文对齐问题的解决：当中文字符宽度不够时，采用西文字符填充；中西文字符占用宽度不同，采用中文字符的空格填充`chr(12288)`.

代码块：[Python网络爬虫与信息提取_中国大学MOOC(慕课) (icourse163.org)](https://www.icourse163.org/learn/BIT-1001870001?tid=1472329486#/learn/content?type=detail&id=1258134685&cid=1292474335)

```python
import bs4
import requests
from bs4 import BeautifulSoup
import re

def getHTMLText(url):
    try:
        r=requests.get(url,timeout=30)
        r.raise_for_status()
        r.encoding=r.apparent_encoding
        return r.text
    except:
        return "网页信息爬取错误"

def fillUnivList(ulist, html):
    soup = BeautifulSoup(html, "html.parser")
    for tr in soup.find_all('tbody').children:
        # 检测tr标签的类型的类型，如果tr标签的类型不是bs4库定义的tag类型，将过滤掉
        if isinstance(tr,bs4.element.Tag):
            tds=tr('td')
            ulist.append([tds[0].string,tds[1].string,tds[2].string])

def printUnivList(ulist, num):
    tplt="{0:10}\t{1:{3}^10}\t{2:^10}"
    print("{:10}\t{:^6}\t{:^10}".format("排名","学校名称","总分"))
    for i in range(num):
        u=ulist[i]
        print(tplt.format(u[0],u[1],u[2],chr(12288)))

def main():
    uinfo=[]
    url=""
    html=getHTMLText(url)
    fillUnivList(uinfo,html)
    printUnivList(uinfo,20)

```

---

## 十、正则表达式

### (1) 正则表达式的概述

- 通用的字符串表达框架
- 简洁表达一组字符串的表达式
- 针对字符串表达“简洁”和“特征”思想的工具
- 判断某字符串的特征归属

正则表达式在文本处理中十分常用

1. 表达式文本类型的特征（病毒、入侵等）
2. 同时查找或替换一组字符串
3. 匹配字符串的全部或部分

### (2) 正则表达式的语法和使用

1. 语法：

   正则表达式语法由字符和操作符构成

```python
P(Y|YT|YTH|YTHO)?N
```

2. 使用

   编译：将符合正则表达式语法的字符串转换成正则表达式特征。

<img src="C:\Users\29496\AppData\Roaming\Typora\typora-user-images\image-20240520102557667.png" alt="image-20240520102557667"  />



### (3) 正则表达式的常用操作符

| 操作符 | 说明                             | 实例                                    |
| ------ | -------------------------------- | --------------------------------------- |
| .      | 表示任何单个字符，除”\n“以外     |                                         |
| [ ]    | 字符集，对单个字符给出取值范围   | [abc]表示a、b、c，[a-z]表示a到z单个字符 |
| [^ ]   | 非字符集，对单个字符给出排除范围 | [^abc]表示非a或b或c的单个字符           |
| *      | 前一个字符0次或无限次扩展        | abc*表示ab、abc、abcc、abcc等           |
| +      | 前一个字符1次或无限次扩展        | abc+表示abc、abcc、abccc等              |
| ?      | 前一个字符0次或1次扩展           | abc？表示av、abc                        |
| \|     | 左右表达式任意一个               | abc\|def表示abc、def                    |
| {m}    | 扩展前一个字符m次                | ab{2}c表示abbc                          |
| {m,n}  | 扩展前一个字符m至n次(含n)        | ab{1，2}c表示abc、abbc                  |
| ^      | 匹配字符串开头                   | ^abc表示abc且在一个字符串的开头         |
| $      | 匹配字符串结尾                   | abc$表示abc且在一个字符串的结尾         |
| ()     | 分组标记，内部只能使用\|操作符   | (abc)表示abc，(abc\|def)表示abc、def    |
| \d     | 数字，等价于[0-9]                |                                         |
| \w     | 单词字符，等价于[A-Za-z0-9]      |                                         |

### (4) re库主要功能函数

| 函数                         | 说明                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| re.search(pattern, string)   | 在一个字符串中搜索匹配正则表达式的第一个位置，返回match对象  |
| re.match(pattern, string)    | 从一个字符串的开始位置起匹配正则表达式，返回match对象        |
| re.findall(pattern, string)  | 搜索字符串，以列表类型返回全部能匹配的子串                   |
| re.split(pattern, string)    | 将一个字符串按照正则表达式匹配结果进行分割，返回列表类型     |
| re.finditer(pattern, string) | 搜索字符串，返回一个匹配结果的迭代类型，每个迭代元素是match对象 |
| re.sub(pattern, string)      | 在一个字符串中替换所有匹配正则表达式的字串，返回替换后的字符串 |
| re.compile(pattern[, flags]) |                                                              |
| re.escape(string)            |                                                              |

---

`re`模块中常用函数的简单介绍：

语法：

```python
re.函数(pattern, string，flags=0)
```

- pattern：正则表达式的字符串或原生字符串表示
- string：待匹配字符串
- flags：正则表达式使用时的控制标记

1. **re.search(pattern, string，flags=0)**: 
   在字符串中搜索匹配正则表达式`pattern`的第一个位置，返回一个匹配对象，如果没有找到匹配的，则返回None。

   ```python
   import re
   match = re.search(r'\d+', 'hello 123 world')
   if match:
       print('找到匹配:', match.group())
   else:
       print('未找到匹配')
   ```

2. **re.match(pattern, string，flags=0)**:
   从字符串的起始位置匹配正则表达式`pattern`，如果起始位置没有匹配到，则返回None。注意这与`search()`不同，`search()`会扫描整个字符串以查找匹配项。

   

   ```python
   match = re.match(r'\d+', '123 hello world')
   if match:
       print('找到匹配:', match.group())
   else:
       print('未找到匹配')
   ```

3. **re.findall(pattern, string)**:
   返回字符串中所有与正则表达式`pattern`相匹配的所有非重叠匹配项的列表。如果未找到匹配项，则返回空列表。

   ```python
   matches = re.findall(r'\b\w+\b', 'Hello World! This is a test.')
   print(matches)
   # 输出：['Hello', 'World', 'This', 'is', 'a', 'test']
   ```

4. **re.sub(pattern, repl, string, count=0, flags)**:
   将字符串中所有与正则表达式`pattern`匹配的部分替换为`repl`，并返回修改后的字符串。`count`参数可以指定替换的最大次数，默认为0，表示替换所有匹配项。

   ```python
   result = re.sub(r'\d+', 'NUMBER', 'hello 123 world 456')
   print(result)
   # 输出：'hello NUMBER world NUMBER'
   ```

5. **re.compile(pattern[, flags])**:
   编译正则表达式字符串为一个正则表达式对象，这样可以提高使用相同模式进行多次匹配的效率。

   ```python
   pattern = re.compile(r'\d+')
   match = pattern.match('123 hello')
   if match:
       print('找到匹配:', match.group())
   ```

6. **re.escape(string)**:
   转义字符串中的特殊字符，使得它们在正则表达式中作为字面值字符对待。

   ```python
   pattern = re.compile(re.escape('[') + r'\d+' + re.escape(']'))
   match = pattern.search('[123]')
   if match:
       print('找到匹配:', match.group())
   ```

7. **re.split(pattern,string,maxsplit=0,flags=0)**:

   将一个字符串按照正则表达式匹配进行分割返回列表类型

   - maxsplit：最大分割数，剩余部分作为最后一个元素输出

   ```python
   import re
   result1=re.split(r'[1-9]\d{5}','BIT100081 TSU100084')
   print(result1)
   result2=re.split(r'[1-9]\d{5}','BIT100081 TSU100084',maxsplit=1)
   print(result2)
   ```

8. **re.finditer(pattern, string)**:

   搜索字符串，返回一个匹配结果的迭代类型，每个迭代元素是，match对象

   ```python
   import re
   for m in re.finditer(r'[1-9]\d{5}','BIT100081 TSU100084'):
       if m:
           print(m.group(0))
   ```

`拓展`:

1. re库的另一种等价用法：

```python
# 第一种 函数式用法：一次性操作
rst=re.search(r'[1-9]\d{5}','BIT 100081')
# 第二种 面对对象用法：编译后的多次操作
pat=ree.compile(r'[1-9]\d{5}')
rst=pat.search('BIT 100081')
```

2. match对象介绍

   Match对象一次匹配的结果，包含匹配的很多信息

   ```python
   import re
   match=re.search(r'[1-9]\d{5}','BIT 100081')
   if match:
       print(match.group(0))
   
   print(type(match))
   ```

   match对象的属性：

   | 属性    | 说明                                 |
   | ------- | ------------------------------------ |
   | .string | 待匹配的文本                         |
   | .re     | 匹配时使用的patter对象（正则表达式） |
   | .pos    | 正则表达式搜索文本的开始位置         |
   | .endpos | 正则表达式搜索文本的结束位置         |

   match对象的方法：

   | 方法      | 说明                             |
   | --------- | -------------------------------- |
   | .group(0) | 获得匹配的字符串                 |
   | .start()  | 匹配字符串在原始字符串的开始位置 |
   | .end()    | 匹配字符串在原始字符串的结束位置 |
   | .span()   | 返回(.start(),.end())            |

   ```python
   import re
   match=re.search(r'[1-9]\d{5}','BIT100081 TSU100084')
   print(match.string)
   print(match.re)
   print(match.pos)
   print(match.endpos)
   print(match.group(0))
   print(match.start())
   print(match.end())
   print(match.span())
   ```

3. 贪婪匹配

   Re默认采用贪婪匹配，即输出匹配最长的字串

   ```python
   import re
   match=re.search(r'PY.*N','PYANBNCNDN')
   print(match.group(0))
   ```

   最小匹配：如何输出最短的子串呢？

   ```python
   import re
   match=re.search(r'PY.*?N','PYANBNCNDN')
   print(match.group(0))
   ```

   最小匹配操作符

   | 操作符   | 说明                                  |
   | -------- | ------------------------------------- |
   | *？      | 前一个字符0次或无限次扩展，最小匹配   |
   | +？      | 前一个字符1次或无限次扩展，最小匹配   |
   | ？？     | 前一个字符0次或1次扩展，最小匹配      |
   | {m，n}？ | 扩展前一个字符m至n次（含n），最小匹配 |

4. 典型例子：

   在Python中，可以使用`re`模块（正则表达式模块）来匹配IP地址。一个基本的IPv4地址由四个0到255之间的数字组成，每部分之间用点(".")分隔。下面是一个简单的例子，展示了如何编写一个正则表达式来匹配这样的IP地址：

```python
import re

def is_valid_ip(ip):
    # 定义IP地址的正则表达式
    ip_pattern = re.compile(r'^((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$')
    # 使用正则表达式匹配IP地址
    if ip_pattern.match(ip):
        return True
    else:
        return False
    
if __name__ == '__main__':
    # 测试函数
    ips = ["192.168.1.1", "255.255.255.255", "123.456.789.0", "1.2.3"]
    for ip in ips:
        print(f"{ip}: {is_valid_ip(ip)}")
```

这段代码首先导入了`re`模块，并定义了一个函数`is_valid_ip`，该函数使用一个正则表达式来检查输入的字符串是否符合IPv4地址的格式。正则表达式的详细解释如下：

- `^`：表示字符串的开始。
- `((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}`：这部分匹配前三部分的数字，每部分数字范围是0到255，后面跟着一个点(".")。其中，
  - `25[0-5]` 匹配从250到255的数字，
  - `2[0-4][0-9]` 匹配从200到249的数字，
  - `[01]?[0-9][0-9]?` 匹配0到199的数字，包括前导零的情况。
  - `\.` 表示匹配点字符本身（因为点在正则表达式中有特殊含义，所以需要用反斜杠转义）。
- `(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$`：这部分匹配第四部分的数字，后面跟上字符串的结束标志`$`。

经典正则表达式实例：

| 表达式                  | 解释                         |
| ----------------------- | ---------------------------- |
| ^[A-Za-z]+$             | 由26个字母组成的字符串       |
| ^[A-Za-Z0-9]+$          | 由26个字母和数字组成的字符串 |
| ^-?\d+$                 | 整数形式的字符串             |
| ^[0-9]*[1-9] [0-9] * $  | 正整数形式的字符串           |
| [1-9] \d{5}             | 中国境内邮政编码，6位        |
| [\u4e00-\u9fa5]         | 匹配中文字符                 |
| \d{3}-d{8}\|\d{4}-\d{7} | 国内电话号码，010-68913536   |



---

## 十一、实例：淘宝商品信息定向爬虫

### (1) 功能描述

- 目标：获取淘宝搜索页面的信息，提取其中的商品名称和价格
- 理解：淘宝的搜索接口、翻页的处理
- 技术路线：requests-re

###  (2) 程序的结构设计

步骤1：提交商品搜索请求，循坏获取页面。

```bash
https://s.taobao.com/search?initiative_id=staobaoz_20240525&page=1&q=%E4%B9%A6%E5%8C%85&tab=all
https://s.taobao.com/search?initiative_id=staobaoz_20240525&page=2&q=%E4%B9%A6%E5%8C%85&tab=all
https://s.taobao.com/search?initiative_id=staobaoz_20240525&page=3&q=%E4%B9%A6%E5%8C%85&tab=all
https://s.taobao.com/search?page=2&q=%E4%B9%A6%E5%8C%85&tab=all
https://s.taobao.com/search?page=1&q=%E4%B9%A6%E5%8C%85&tab=all
https://s.taobao.com/search?page=3&q=%E4%B9%A6%E5%8C%85&tab=all
```

步骤2：对于每个页面，提取商品名称和价格信息

步骤3：将信息输出到屏幕上。

`代码块`：

```python
#CrowTaobaoPrice.py
import requests
import re

def getHTMLText(url):
    try:
        r = requests.get(url, timeout=30)
        r.raise_for_status()
        r.encoding = r.apparent_encoding
        return r.text
    except:
        return ""
    
def parsePage(ilt, html):
    try:
        plt = re.findall(r'\"view_price\"\:\"[\d\.]*\"',html)
        tlt = re.findall(r'\"raw_title\"\:\".*?\"',html)
        for i in range(len(plt)):
            price = eval(plt[i].split(':')[1])
            title = eval(tlt[i].split(':')[1])
            ilt.append([price , title])
    except:
        print("")

def printGoodsList(ilt):
    tplt = "{:4}\t{:8}\t{:16}"
    print(tplt.format("序号", "价格", "商品名称"))
    count = 0
    for g in ilt:
        count = count + 1
        print(tplt.format(count, g[0], g[1]))
        
def main():
    goods = '书包'
    depth = 3
    start_url = 'https://s.taobao.com/search?q=' + goods
    infoList = []
    for i in range(depth):
        try:
            url = start_url + '&s=' + str(44*i)
            html = getHTMLText(url)
            parsePage(infoList, html)
        except:
            continue
    printGoodsList(infoList)
    
main()
```

---

## 十二、实例：股票数据定向爬虫

### (1) 功能描述：

- 目标：获取上交所和深交所所有股票的名称和交易信息
- 输出：保存到文件中
- 技术路线：requests-bs4-re

候选数据网站的选择

```bash
新浪股票:http://finance.sina.com.cn/stock/
百度股票:https://gupiao.baidu.com/stock/
```

- 选取原则：股票信息静态存在于HTML页面中，非js代码生成，没有Robots协议限制。
- 选取方法：浏览器F12，源代码查看等。
- 选取心态：不要纠结于某个网络，多找信息源，选择尝试

### (2) 程序的结构设计

```python
东方财富网：http://quote.eastmoney.com/stocklist.html
百度股票：https://gupiao.baidu.com/stock/
单个股票：https://gupiao.baidu.com/stock/sz002439.html
```

- 步骤1：从东方财富网获取股票列表

  步骤2：根据股票列表逐个到百度股票获取个股信息

- 步骤3：将结果存储到文件



`代码块`：

```python
#CrawBaiduStocksB.py
import requests
from bs4 import BeautifulSoup
import traceback
import re

def getHTMLText(url, code="utf-8"):
    try:
        r = requests.get(url)
        r.raise_for_status()
        r.encoding = code
        return r.text
    except:
        return ""

def getStockList(lst, stockURL):
    html = getHTMLText(stockURL, "GB2312")
    soup = BeautifulSoup(html, 'html.parser') 
    a = soup.find_all('a')
    for i in a:
        try:
            href = i.attrs['href']
            lst.append(re.findall(r"[s][hz]\d{6}", href)[0])
        except:
            continue

def getStockInfo(lst, stockURL, fpath):
    count = 0
    for stock in lst:
        url = stockURL + stock + ".html"
        html = getHTMLText(url)
        try:
            if html=="":
                continue
            infoDict = {}
            soup = BeautifulSoup(html, 'html.parser')
            stockInfo = soup.find('div',attrs={'class':'stock-bets'})

            name = stockInfo.find_all(attrs={'class':'bets-name'})[0]
            infoDict.update({'股票名称': name.text.split()[0]})
            
            keyList = stockInfo.find_all('dt')
            valueList = stockInfo.find_all('dd')
            for i in range(len(keyList)):
                key = keyList[i].text
                val = valueList[i].text
                infoDict[key] = val
            
            with open(fpath, 'a', encoding='utf-8') as f:
                f.write( str(infoDict) + '\n' )
                count = count + 1
                print("\r当前进度: {:.2f}%".format(count*100/len(lst)),end="")
        except:
            count = count + 1
            print("\r当前进度: {:.2f}%".format(count*100/len(lst)),end="")
            continue

def main():
    stock_list_url = 'http://quote.eastmoney.com/stocklist.html'
    stock_info_url = 'https://gupiao.baidu.com/stock/'
    output_file = 'D:/BaiduStockInfo.txt'
    slist=[]
    getStockList(slist, stock_list_url)
    getStockInfo(slist, stock_info_url, output_file)

main()
```



---

## 十三、网络爬虫框架

### (1) Scrapy爬虫框架结构

#### 1. 爬虫框架

- 爬虫框架是实现爬虫功能的一个软件结构和功能组件集合
- 爬虫框架是一个半成品，能够帮助用户实现专业网络爬虫

#### 2. 5+2结构

![image-20240526144623486](C:\Users\29496\AppData\Roaming\Typora\typora-user-images\image-20240526144623486.png)

数据流的三个路径

> 1. Engine从Spider处获得爬取请求（Requests）
> 2. Engine将爬取请求转发给Scheduler，用于调度
> 3. Engine从Scheduler处获得下一个要爬取的请求
> 4. Engine将爬取请求通过中间件发送给Downloader
> 5. 爬取网页后，Downloader形成响应（Response）通过中间件发给Engine
> 6. Engine将收到的响应通过中间件发送给Spider处理
> 7. Spider处理响应后产生爬取项（scraped Item）和新的爬取请求（requests）给Engine
> 8. Engine将爬取项发送给Item Pipeline（框架出口）
> 9. Engine将爬取请求发送给Scheduler 

数据流的出入口

> Engine控制各模块数据流，不间断从Scheduler处获得爬取请求，直至请求为空
>
> - 框架入口：Spider的初始爬取请求
>
> - 框架出口：Item Pipeline

---

1. Engine：不需要用户修改
   - 控制所有模块之间的数据流
   - 根据条件触发事件
2. Downloader：不需要用户修改
   - 根据请求下载页面
3. Scheduler：不需要用户修改
   - 对所有爬取请求进行调度管理
4. Spider：需要用户编写配置代码
   - 解析Downloader返回的响应（Response）
   - 产生爬取项（scraped item）
   - 产生额外的爬取请求（Request）
5. Item Pipelines
   - 以流水线方式处理Spider 产生的爬取项。
   - 由一组操作顺序组成，类似流水线，每一个操作是一个Item Pipeline类型
   - 可能操作包括：清理、检验和查重爬取项中的HTML数据、将数据存储到数据库。

Downloader Middleware : 用户可以编写配置代码

```python
# 目的：实施Engine、Scheduler和Downloader之间进行用户可配置的控制
# 功能：修改、丢弃、新增请求或响应
```

Spider Middleware：用户可以编写配置代码

```python
# 目的：对请求和爬取项的再处理
# 功能：修改、丢弃、新增请求或爬取项
```



---



#### 3. requests库和scrapy库的比较

1. 相同点：
   - 两者都可以进行页面请求和爬取，Python爬虫的两个重要技术路线。
   - 两者可用性都好，文档丰富，入门简单。
   - 两者都没有处理js、提交表单、应对验证码等功能（可扩展）

2. 不同点

| requests                 | Scrapy                     |
| ------------------------ | -------------------------- |
| 页面级爬虫               | 网站级爬虫                 |
| 功能库                   | 框架                       |
| 并发性考虑不足，性能较差 | 并发性，性能较好           |
| 终点在于页面下载         | 终点在于爬虫结构           |
| 定制灵活                 | 一般定制灵活，深度定制困难 |
| 上手十分简单             | 入门稍难                   |

3. 选用哪个技术路线开发爬虫
   - 非常小的需求，requests库
   - 不太小的需求，Scrapy框架
   - 定制程度很高的需求（不考虑规模），自搭框架，requests > Scrapy

---

#### 4. Scrapy 命令行：大部分操作都是通过命令行来实现

1. Scrapy是为持续运行设计的专业爬虫框架，提供操作的Scraoy命令行

2. Scrapy命令行格式：

```python
>scrapy <command>[options][args]
```

| 序号 | 命令         | 说明               | 格式                                       |
| ---- | ------------ | ------------------ | ------------------------------------------ |
| 1    | startproject | 创建一个新工程     | scrapy startproject <name> [dir]           |
| 2    | genspider    | 创建一个爬虫       | scrapy gebspider [options] <name> <domain> |
| 3    | settings     | 获得爬虫配置信息   | scrapy settings [options]                  |
| 4    | crawl        | 运行一个爬虫       | scrapy crawl <spider>                      |
| 5    | list         | 列出工程中所有爬虫 | scrapy list                                |
| 6    | shell        | 启动URL调试命令行  | scrapy shell [url]                         |

`注`：1、2、4常用

3. Scrapy爬虫的命令行逻辑

   为什么Scrapy采用命令行创建和运行爬虫？

   - 命令行（不是图形界面）更容易自动化，适合脚本控制
   - 本质上，Scrapy是给程序用的，功能（而不是界面）更重要。

---



### (2) Scrapy爬虫基本使用

Scrapy爬虫的使用步骤

>1. 步骤1：创建一个工程和Spider模板
>2. 步骤2：编写Spider
>3. 步骤3：Item Pipeline
>4. 步骤4：优化配置策略

---

#### 1. 步骤1：建立一个Scrapy爬虫工程

创建工程，生成工程目录（python123demo/【外层目录】，包含scrapy.cfg【部署Scrapy爬虫的配置文件】）生成的工程目录

![image-20240526185318697](C:\Users\29496\AppData\Roaming\Typora\typora-user-images\image-20240526185318697.png)

![image-20240526185401625](C:\Users\29496\AppData\Roaming\Typora\typora-user-images\image-20240526185401625.png)

`python123demo/`：外层目录

`scrapy.cfg`：部署Scrapy爬虫的配置文件

`python123demo/`：Scrapy框架的用户自定义的配置文件

`__int__.py`：初始化脚本

`items.py`：Items代码模块（继承类）

`middlewares.py`：Middlewares代码模块（继承类）

`pipelines.py`：Pipelines代码模块（继承类）

`settings.py`：Scrapy爬虫的配置文件

`spiders/`：Spiders代码模块目录（继承类）

```bash
D:\>E:
E:\> cd \Hui\爬虫\自修案例
# 创建工程
E:\Hui\爬虫\自修案例>scrapy startproject python123demo
New Scrapy project 'python123demo', using template directory 'D:\Software\python3.12.2\Lib\site-packages\scrapy\templates\project', created in:
    E:\Hui\爬虫\自修案例\python123demo

You can start your first spider with:
    cd python123demo
    scrapy genspider example example.com
    

```

#### 2. 步骤2：在工程中产生一个Scrapy爬虫

```bash
# 创建demo文件
E:\Hui\爬虫\自修案例>cd python123demo
E:\Hui\爬虫\自修案例\python123demo>scrapy genspider demo python123.io
Created spider 'demo' using template 'basic' in module:
  python123demo.spiders.demo
```

`spiders/`：Spiders代码模块目录（继承类）

`__init__.py`：初始文件，无需修改

`__pycache__`：缓存目录，无需修改

```python
import scrapy

class DemoSpider(scrapy.Spider):
    # 当前爬虫的名字
    name = "demo"
    # 最开始用户提交给命令行的域名：指的是爬虫在爬取网站的时候，它只能爬取这个域名以下的相关链接
    allowed_domains = ["python123.io"]
    # 以列表形式包含的一个或多个url，就是scrapy框架所要爬取的初始页面
    start_urls = ["https://python123.io"]

    #解析页面的空方法，用于处理响应，解析内容形成字典，发现新的url爬取请求
    def parse(self, response):
        pass

```

#### 3. 步骤3：配置产生的spider 爬虫

- 初始化url地址
- 获取页面后的解析方式

```python
import scrapy

class DemoSpider(scrapy.Spider):
    # 当前爬虫的名字
    name = "demo"
    # 最开始用户提交给命令行的域名：指的是爬虫在爬取网站的时候，它只能爬取这个域名以下的相关链接
    # allowed_domains = ["python123.io"]
    # 以列表形式包含的一个或多个url，就是scrapy框架所要爬取的初始页面
    start_urls = ["http://python123.io/ws/demo.html"]

    #解析页面的空方法，用于处理响应，解析内容形成字典，发现新的url爬取请求
    def parse(self, response):
        # 将response响应的内容写入html文件中
        fnaem=response.url.split('/')[-1]
        with open(fnaem,'wb') as f:
            f.write(response.body)
        self.log(f"Saved file {name}")
        pass
```



#### 4. 步骤4：运行爬虫，获取网页

保存在`E:\Hui\爬虫\自修案例\python123demo` 路径下为`demo.html`

```python
E:\Hui\爬虫\自修案例\python123demo>scrapy crawl demo
2024-05-26 15:39:40 [scrapy.utils.log] INFO: Scrapy 2.11.2 started (bot: python123demo)
2024-05-26 15:39:40 [scrapy.utils.log] INFO: Versions: lxml 5.2.1.0, libxml2 2.11.7, cssselect 1.2.0, parsel 1.9.1, w3lib 2.1.2, Twisted 24.3.0, Python 3.12.2 (tags/v3.12.2:6abddd9, Feb  6 2024, 21:26:36) [MSC v.1937 64 bit (AMD64)], pyOpenSSL 24.1.0 (OpenSSL 3.2.1 30 Jan 2024), cryptography 42.0.7, Platform Windows-11-10.0.22631-SP0
2024-05-26 15:39:40 [scrapy.addons] INFO: Enabled addons:
[]
2024-05-26 15:39:40 [asyncio] DEBUG: Using selector: SelectSelector
2024-05-26 15:39:40 [scrapy.utils.log] DEBUG: Using reactor: twisted.internet.asyncioreactor.AsyncioSelectorReactor
2024-05-26 15:39:40 [scrapy.utils.log] DEBUG: Using asyncio event loop: asyncio.windows_events._WindowsSelectorEventLoop
2024-05-26 15:39:40 [scrapy.extensions.telnet] INFO: Telnet Password: 77de2e6d491b1e81
2024-05-26 15:39:40 [scrapy.middleware] INFO: Enabled extensions:
['scrapy.extensions.corestats.CoreStats',
 'scrapy.extensions.telnet.TelnetConsole',
 'scrapy.extensions.logstats.LogStats']
2024-05-26 15:39:40 [scrapy.crawler] INFO: Overridden settings:
{'BOT_NAME': 'python123demo',
 'FEED_EXPORT_ENCODING': 'utf-8',
 'NEWSPIDER_MODULE': 'python123demo.spiders',
 'REQUEST_FINGERPRINTER_IMPLEMENTATION': '2.7',
 'ROBOTSTXT_OBEY': True,
 'SPIDER_MODULES': ['python123demo.spiders'],
 'TWISTED_REACTOR': 'twisted.internet.asyncioreactor.AsyncioSelectorReactor'}
2024-05-26 15:39:40 [scrapy.middleware] INFO: Enabled downloader middlewares:
['scrapy.downloadermiddlewares.offsite.OffsiteMiddleware',
 'scrapy.downloadermiddlewares.robotstxt.RobotsTxtMiddleware',
 'scrapy.downloadermiddlewares.httpauth.HttpAuthMiddleware',
 'scrapy.downloadermiddlewares.downloadtimeout.DownloadTimeoutMiddleware',
 'scrapy.downloadermiddlewares.defaultheaders.DefaultHeadersMiddleware',
 'scrapy.downloadermiddlewares.useragent.UserAgentMiddleware',
 'scrapy.downloadermiddlewares.retry.RetryMiddleware',
 'scrapy.downloadermiddlewares.redirect.MetaRefreshMiddleware',
 'scrapy.downloadermiddlewares.httpcompression.HttpCompressionMiddleware',
 'scrapy.downloadermiddlewares.redirect.RedirectMiddleware',
 'scrapy.downloadermiddlewares.cookies.CookiesMiddleware',
 'scrapy.downloadermiddlewares.httpproxy.HttpProxyMiddleware',
 'scrapy.downloadermiddlewares.stats.DownloaderStats']
2024-05-26 15:39:40 [scrapy.middleware] INFO: Enabled spider middlewares:
['scrapy.spidermiddlewares.httperror.HttpErrorMiddleware',
 'scrapy.spidermiddlewares.referer.RefererMiddleware',
 'scrapy.spidermiddlewares.urllength.UrlLengthMiddleware',
 'scrapy.spidermiddlewares.depth.DepthMiddleware']
2024-05-26 15:39:40 [scrapy.middleware] INFO: Enabled item pipelines:
[]
2024-05-26 15:39:40 [scrapy.core.engine] INFO: Spider opened
2024-05-26 15:39:40 [scrapy.extensions.logstats] INFO: Crawled 0 pages (at 0 pages/min), scraped 0 items (at 0 items/min)
2024-05-26 15:39:40 [scrapy.extensions.telnet] INFO: Telnet console listening on 127.0.0.1:6023
2024-05-26 15:39:41 [scrapy.downloadermiddlewares.redirect] DEBUG: Redirecting (301) to <GET https://python123.io/robots.txt> from <GET http://python123.io/robots.txt>
2024-05-26 15:39:41 [scrapy.core.engine] DEBUG: Crawled (404) <GET https://python123.io/robots.txt> (referer: None)
2024-05-26 15:39:41 [scrapy.downloadermiddlewares.redirect] DEBUG: Redirecting (301) to <GET https://python123.io/ws/demo.html> from <GET http://python123.io/ws/demo.html>
2024-05-26 15:39:41 [scrapy.core.engine] DEBUG: Crawled (200) <GET https://python123.io/ws/demo.html> (referer: None)
2024-05-26 15:39:41 [scrapy.core.scraper] ERROR: Spider error processing <GET https://python123.io/ws/demo.html> (referer: None)
Traceback (most recent call last):
  File "D:\Software\python3.12.2\Lib\site-packages\twisted\internet\defer.py", line 1078, in _runCallbacks
    current.result = callback(  # type: ignore[misc]
                     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\Software\python3.12.2\Lib\site-packages\scrapy\spiders\__init__.py", line 82, in _parse
    return self.parse(response, **kwargs)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "E:\Hui\爬虫\自修案例\python123demo\python123demo\spiders\demo.py", line 18, in parse
    self.log(f"Saved file {name}")
                           ^^^^
NameError: name 'name' is not defined. Did you mean: 'self.name'?
2024-05-26 15:39:41 [scrapy.core.engine] INFO: Closing spider (finished)
2024-05-26 15:39:41 [scrapy.statscollectors] INFO: Dumping Scrapy stats:
{'downloader/request_bytes': 896,
 'downloader/request_count': 4,
 'downloader/request_method_count/GET': 4,
 'downloader/response_bytes': 1981,
 'downloader/response_count': 4,
 'downloader/response_status_count/200': 1,
 'downloader/response_status_count/301': 2,
 'downloader/response_status_count/404': 1,
 'elapsed_time_seconds': 0.954325,
 'finish_reason': 'finished',
 'finish_time': datetime.datetime(2024, 5, 26, 7, 39, 41, 875924, tzinfo=datetime.timezone.utc),
 'log_count/DEBUG': 7,
 'log_count/ERROR': 1,
 'log_count/INFO': 10,
 'response_received_count': 2,
 'robotstxt/request_count': 1,
 'robotstxt/response_count': 1,
 'robotstxt/response_status_count/404': 1,
 'scheduler/dequeued': 2,
 'scheduler/dequeued/memory': 2,
 'scheduler/enqueued': 2,
 'scheduler/enqueued/memory': 2,
 'spider_exceptions/NameError': 1,
 'start_time': datetime.datetime(2024, 5, 26, 7, 39, 40, 921599, tzinfo=datetime.timezone.utc)}
2024-05-26 15:39:41 [scrapy.core.engine] INFO: Spider closed (finished)
```

---

#### 5. `yield` 关键字【生成器】

- 生成器是一个不断产生值的函数
- 包含yield语句的函数时一个生成器
- 生成器每次产生一个值（yield语句），函数被冻结，被唤醒后再产生一个值。

1. 普通写法：

```python
import scrapy

class DemoSpider(scrapy.Spider):
    # 当前爬虫的名字
    name = "demo"
    # 最开始用户提交给命令行的域名：指的是爬虫在爬取网站的时候，它只能爬取这个域名以下的相关链接
    # allowed_domains = ["python123.io"]
    # 以列表形式包含的一个或多个url，就是scrapy框架所要爬取的初始页面
    start_urls = ["http://python123.io/ws/demo.html"]
    #解析页面的空方法，用于处理响应，解析内容形成字典，发现新的url爬取请求
    def parse(self, response):
        # 将response响应的内容写入html文件中
        fnaem=response.url.split('/')[-1]
        with open(fnaem,'wb') as f:
            f.write(response.body)
        self.log(f"Saved file {name}")
        pass
```

2. 生成器`yield`写法：

```python
import scrapy

class DemoSpider(scrapy.Spider):
    name = "demo"
    def start_requests(self):
        urls=[
            'http://python123.io/ws/dem.html'
        ]
        for url in urls:
            yield scrapy.Request(url=url,callback=self.parse)

    def parse(self,response):
        fname=response.url.split('')[-1]
        with open(fname,'wb') as f:
            f.write(response.body)
        self.log(f"Saved file {fname}")
```

3. yield一般都是于for循环连用，例如：

```python
# 生成器写法
def gen(n):
    for i in range(n):
        yield i**2

for i in gen(5):
    print(i," ",end='')
    
#普通写法
def square(n):
    ls= [i**2 for i in range(n)]
    return ls

for i in square(5):
    print(i," ",end='')
```





#### 6. Scrapy爬虫的数据类型

1. Request类

   `class scrapy.http.Request()`:

   - Request对象表示一个HTTP请求
   - 由Spider生成，由Downloader执行

   | 属性或方法 | 说明                                               |
   | ---------- | -------------------------------------------------- |
   | .url       | Request对应的请求url地址                           |
   | .method    | 对应的请求方法，‘GET’‘PODT’等                      |
   | .headers   | 字典类型风格的请求头                               |
   | .body      | 请求内容主体，字符串类型                           |
   | .meta      | 用户添加的扩展信息，在Scrapy内部模块间传递信息使用 |
   | .copy()    | 复制该请求                                         |

2. Response类

   `class scrapy.http.Response()`:

   - Response对象表示一个HTTP响应。
   - 由Downloader生成，由Spider处理

   | 属性或方法 | 说明                               |
   | ---------- | ---------------------------------- |
   | .url       | Response对应的请求url地址          |
   | .status    | HTTP状态码，默认是200              |
   | .headers   | Respnse对应的头部信息              |
   | .body      | Response对应的内容信息，字符串类型 |
   | .flags     | 一组标记                           |
   | .request   | 产生Response类型对应的Request对象  |
   | .copy()    | 复制该响应                         |

3. Item类

   `class scrapy.item.Item()`:

   - Item对象表示一个从HTML页面中提取的信息内容
   - 由Spider生成，由Item Pipeline处理。
   - Item类型字典类型，可以按照字典类型操作。

4. Scrapy爬虫提取信息的方法

   Scrapy爬虫支持多种HTML信息提取方法

   - Beautiful Soup

   - lxml

   - re

   - Xpath Selector

   - CSS Selector

     ```python
     # CSS Selector的基本使用（CSS Selector由W3C组织维护并规范）
     <HTML>.css('a::attr(href)').extract() #a：标签名称  href：标签属性
     ```

---

### (3) 实例：股票数据Scrapy实例

#### 1. 步骤1：建立工程和Spider模板

打开终端：`win+R`，输入`cmd`，建立工程

```cmd
# 进入文件
D:\>cd \Software\pycharm\web_request
# 创建工程，其文件中包含：BaiduStocks 文件和 scrapy.cfg 文件
D:\Software\pycharm\web_request>scrapy startproject BaiduStocks
New Scrapy project 'BaiduStocks', using template directory 'D:\Software\python3.12.2\Lib\site-packages\scrapy\templates\project', created in:
    D:\Software\pycharm\web_request\BaiduStocks
    
You can start your first spider with:
    cd BaiduStocks
    scrapy genspider example example.com
# 切换视图
D:\Software\pycharm\web_request>cd BaiduStocks
# 创建爬虫 spiders模板
D:\Software\pycharm\web_request\BaiduStocks>scrapy genspider stocks baidu.com
Created spider 'stocks' using template 'basic' in module:
  BaiduStocks.spiders.stocks
```

进一步修改`spiders/stocks.py`文件



#### 2. 步骤2：编写Spider

配置`stocks.py`文件；修改对返回页面的处理；修改对新增URL爬取请求的处理

```python
import scrapy
import re


class StocksSpider(scrapy.Spider):
    name = "stocks"
    allowed_domains = ["baidu.com"]
    start_urls = ["http://quote.eastmoney.com/stocklist.html"]

    def parse(self,response):
        # 对这个页面的所有a标签进行提取
        # 使用CSS选择器来定位HTML中的元素
        # .extract()这个方法应用于前面的选择器表达式结果上，用于从选择器匹配到的所有元素中提取出实际的文本内容。
        for href in response.css('a::attr(href)').extract():
            try:
                # 匹配正确的url链接
                stock=re.findall(r"[s][hz]\d{6}",href)[0]
                # 组成个股链接
                url="https://gupiao.baidu.com/stock/"+stock+".html"
                yield scrapy.Request(url,callback=self.parse_stock)
            except:
                continue

    def parse_stock(self, response):
        infoDict = {}
        stockInfo = response.css('.stock-bets')
        name = stockInfo.css('.bets-name').extract()[0]
        keyList = stockInfo.css('dt').extract()
        valueList = stockInfo.css('dd').extract()
        for i in range(len(keyList)):
            key = re.findall(r'>.*</dt>', keyList[i])[0][1:-5]
            try:
                val = re.findall(r'\d+\.?.*</dd>', valueList[i])[0][0:-5]
            except:
                val = '--'
            infoDict[key] = val

        infoDict.update(
            {'股票名称': re.findall('\s.*\(', name)[0].split()[0] + \
                         re.findall('\>.*\<', name)[0][1:-1]})
        yield infoDict
```



####  3.  步骤3：编写Pipelines

配置`pipelines.py`文件；定义对爬虫取项（Scraped Item）的处理类

```python
class BaidustocksPipeline(object):
    def process_item(self, item, spider):
        return item

class BaidustocksInfoPipeline(object):
    def open_spider(self, spider):
        self.f = open('BaiduStockInfo.txt', 'w')

    def close_spider(self, spider):
        self.f.close()

    def process_item(self, item, spider):
        try:
            line = str(dict(item)) + '\n'
            self.f.write(line)
        except:
            pass
        return item
```

#### 4. 配置ITEM_PIPELINES选项

配置`settings.py`文件

```python
ITEM_PIPELINES = {
    'BaiduStocks.pipelines.BaidustocksInfoPipeline': 300,
}
```

程序运行

```cmd
D:\Software\pycharm\web_request\BaiduStocks>scrapy crawl stocks
D:\Software\pycharm\web_request\BaiduStocks\BaiduStocks\spiders\stocks.py:39: SyntaxWarning: invalid escape sequence '\s'
  {'股票名称': re.findall('\s.*\(', name)[0].split()[0] + \
D:\Software\pycharm\web_request\BaiduStocks\BaiduStocks\spiders\stocks.py:40: SyntaxWarning: invalid escape sequence '\>'
  re.findall('\>.*\<', name)[0][1:-1]})
2024-05-27 18:01:29 [scrapy.utils.log] INFO: Scrapy 2.11.2 started (bot: BaiduStocks)
2024-05-27 18:01:29 [scrapy.utils.log] INFO: Versions: lxml 5.2.1.0, libxml2 2.11.7, cssselect 1.2.0, parsel 1.9.1, w3lib 2.1.2, Twisted 24.3.0, Python 3.12.2 (tags/v3.12.2:6abddd9, Feb  6 2024, 21:26:36) [MSC v.1937 64 bit (AMD64)], pyOpenSSL 24.1.0 (OpenSSL 3.2.1 30 Jan 2024), cryptography 42.0.7, Platform Windows-11-10.0.22631-SP0
2024-05-27 18:01:29 [scrapy.addons] INFO: Enabled addons:
[]
2024-05-27 18:01:29 [asyncio] DEBUG: Using selector: SelectSelector
2024-05-27 18:01:29 [scrapy.utils.log] DEBUG: Using reactor: twisted.internet.asyncioreactor.AsyncioSelectorReactor
2024-05-27 18:01:29 [scrapy.utils.log] DEBUG: Using asyncio event loop: asyncio.windows_events._WindowsSelectorEventLoop
2024-05-27 18:01:29 [scrapy.extensions.telnet] INFO: Telnet Password: 3876dc1b1227236a
2024-05-27 18:01:29 [scrapy.middleware] INFO: Enabled extensions:
['scrapy.extensions.corestats.CoreStats',
 'scrapy.extensions.telnet.TelnetConsole',
 'scrapy.extensions.logstats.LogStats']
2024-05-27 18:01:29 [scrapy.crawler] INFO: Overridden settings:
{'BOT_NAME': 'BaiduStocks',
 'FEED_EXPORT_ENCODING': 'utf-8',
 'NEWSPIDER_MODULE': 'BaiduStocks.spiders',
 'REQUEST_FINGERPRINTER_IMPLEMENTATION': '2.7',
 'ROBOTSTXT_OBEY': True,
 'SPIDER_MODULES': ['BaiduStocks.spiders'],
 'TWISTED_REACTOR': 'twisted.internet.asyncioreactor.AsyncioSelectorReactor'}
2024-05-27 18:01:29 [scrapy.middleware] INFO: Enabled downloader middlewares:
['scrapy.downloadermiddlewares.offsite.OffsiteMiddleware',
 'scrapy.downloadermiddlewares.robotstxt.RobotsTxtMiddleware',
 'scrapy.downloadermiddlewares.httpauth.HttpAuthMiddleware',
 'scrapy.downloadermiddlewares.downloadtimeout.DownloadTimeoutMiddleware',
 'scrapy.downloadermiddlewares.defaultheaders.DefaultHeadersMiddleware',
 'scrapy.downloadermiddlewares.useragent.UserAgentMiddleware',
 'scrapy.downloadermiddlewares.retry.RetryMiddleware',
 'scrapy.downloadermiddlewares.redirect.MetaRefreshMiddleware',
 'scrapy.downloadermiddlewares.httpcompression.HttpCompressionMiddleware',
 'scrapy.downloadermiddlewares.redirect.RedirectMiddleware',
 'scrapy.downloadermiddlewares.cookies.CookiesMiddleware',
 'scrapy.downloadermiddlewares.httpproxy.HttpProxyMiddleware',
 'scrapy.downloadermiddlewares.stats.DownloaderStats']
2024-05-27 18:01:29 [scrapy.middleware] INFO: Enabled spider middlewares:
['scrapy.spidermiddlewares.httperror.HttpErrorMiddleware',
 'scrapy.spidermiddlewares.referer.RefererMiddleware',
 'scrapy.spidermiddlewares.urllength.UrlLengthMiddleware',
 'scrapy.spidermiddlewares.depth.DepthMiddleware']
2024-05-27 18:01:29 [scrapy.middleware] INFO: Enabled item pipelines:
['BaiduStocks.pipelines.BaidustocksInfoPipeline']
2024-05-27 18:01:29 [scrapy.core.engine] INFO: Spider opened
2024-05-27 18:01:29 [scrapy.extensions.logstats] INFO: Crawled 0 pages (at 0 pages/min), scraped 0 items (at 0 items/min)
2024-05-27 18:01:29 [scrapy.extensions.telnet] INFO: Telnet console listening on 127.0.0.1:6023
2024-05-27 18:01:29 [scrapy.downloadermiddlewares.offsite] DEBUG: Filtered offsite request to 'quote.eastmoney.com': <GET http://quote.eastmoney.com/robots.txt>
2024-05-27 18:01:29 [scrapy.downloadermiddlewares.redirect] DEBUG: Redirecting (302) to <GET http://quote.eastmoney.com/center/gridlist.html#hs_a_board> from <GET http://quote.eastmoney.com/stocklist.html>
2024-05-27 18:01:30 [scrapy.core.engine] DEBUG: Crawled (200) <GET http://quote.eastmoney.com/center/gridlist.html#hs_a_board> (referer: None)
```



#### 5. 配置并发连接选项

`settings.py`文件

| 选项                           | 说明                                         |
| ------------------------------ | -------------------------------------------- |
| CONCURRENT_REQUESTS            | Downloader最大并发请求下载数量，默认为32     |
| CONCURRENT_ITEMS               | Item Pipeline最大并发ITEM处理数量，默认100   |
| CONCURRENT_REQUESTS_PER_DOMAIN | 每个目标域名最大的并发请求数量，默认8        |
| CONCURRENT_REQUESTS_PER_IP     | 每个目标IP最大的并发请求数量，默认0，非0有效 |


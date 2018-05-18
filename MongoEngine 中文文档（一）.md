# MongoEngine 中文文档（一）
标签（空格分隔）： Mongodb

---
## 近来用Flask做了一个小小的[Demo][1]（目前还在做），用的是MongoDB，ORM采用的是时Flask-MongoEngine，虽然是叫做“Flask-MongoEngine”，但其实只是对“MongoEngine”的一种封装，让其成为了Flask的扩展，所以在查找资料的时候直接找MongoEngine的资料就可以了，然而在开发的过程中，突然发现有关的MongoEngine的中文资料实在是太少了，作为当前比较流行NoSQL数据库，大部分的Python Web开发者其实都是比较钟爱NoSQL的，所以今天打算翻译一下MongoEngine的官方文档 ##
### 这里我只会翻译我个人认为比较重要的部分 ，翻译不好还请原谅###


----------


 1. 安装
  `$ pip install mongoengine`

 2. 连接数据库
   > flask-mongoengine 

    我们先来看一下flask-mongoengine中的源码，其实它就是调用的mongoengine中的connect方法
    
 ```python
    #flask-mongoengine/flask_mongoengine/connection.py
    def _connect(conn_settings):
        
            """Given a dict of connection settings, create a connection to
            MongoDB by calling mongoengine.connect and return its result.
            """
        db_name = conn_settings.pop('name')
        return mongoengine.connect(db_name, **conn_settings)
 ```
 关于flask-mongoengine的连接数据库的方式，我就不一一列举了，可以去看它的[文档][2]，很简单
 


    > mongoengine 
    
    1. 第一种方式
    ```
        from mongoengine import connect
        connect('project1')  #project1为你要连接的数据库的名称
    ```
    2. 第二种方式
    ```
        #如果mongo服务是在其他地方运行，可以使用这种方法
        connect （'project1' ， host = '192.168.1.35' ， port = 12345 ）
    ```
    3. 第三种方式
    ```
        #如果数据库需要身份验证，username并password 应提供的参数
        connect （'project1' ， username = 'webapp' ， password = 'pwd123' ）
    ```
    3. 第四种方式（推荐）
    ```
        #URI样式连接也被支持 - 只需提供URI作为host：
        connect （'project1' ， host = 'mongodb：// localhost / database_name' ）
    ```
    

  [1]: https://github.com/AngleMAXIN/FlyBlog
  [2]: http://docs.mongoengine.org/projects/flask-mongoengine/en/latest/#installing-flask-mongoengine
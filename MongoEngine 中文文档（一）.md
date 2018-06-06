# MongoEngine 中文文档（一）
标签（空格分隔）： Mongodb

---
## 近来用Flask做了一个小小的[Demo][1]（目前还在做），用的是`MongoDB`，ORM采用的是时`Flask-MongoEngine`，虽然是叫做`Flask-MongoEngine`，但其实只是对`MongoEngine`的一种封装，让其成为了`Flask`的扩展，所以在查找资料的时候直接找`MongoEngine`的资料就可以了，然而在开发的过程中，突然发现有关的`MongoEngine`的中文资料实在是太少了，作为当前比较流行`NoSQL`数据库，大部分的Python Web开发者其实都是比较钟爱NoSQL的，所以今天打算翻译一下`MongoEngine`的官方文档 ##
### 这里我只会翻译我个人认为比较重要的部分 ，翻译不好还请原谅###

----------


##（一）. 安装 ##
  `$ pip install mongoengine`

----------


## （二）. 连接数据库 ##
   > flask-mongoengine 

    我们先来看一下flask-mongoengine中的源码，其实它就是调用的mongoengine中的connect方法
```
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
    from mongoengine import connect
    connect （'project1' ， host = '192.168.1.35' ， port = 12345 ）
```
3. 第三种方式
```
    #如果数据库需要身份验证，username并password 应提供的参数
    from mongoengine import connect
    connect （'project1' ， username = 'webapp' ， password = 'pwd123' ）
```
3. 第四种方式（推荐）
```
    #URI样式连接也被支持 - 只需提供URI作为host：
    from mongoengine import connect
    connect （'project1' ， host = 'mongodb：// localhost / database_name' ）
```

----------
##（三). 定义文件 ##

MongoEngine允许您为文档定义模式，因为这有助于减少编码错误，并允许在可能存在的字段上定义实用方法   
下面来一个给一个例子,要为文档定义模式，请创建一个继承自其的类 `Document`。通过将字段对象作为类属性添加到文档类来指定字段
```
from mongoengine import *
import datetime
    
class Page(Document):
    title = StringField(max_length=200, required=True)
    date_modified = DateTimeField(default=datetime.datetime.utcnow)
```
### 3.1. 字段 ###
**默认情况下，字段不是必需的。要使字段成为必填字段，请将字段的`required`关键字参数设置 为`True`。字段也可能有可用的验证约束（`max_length`例如上面的示例中）。字段也可以采用默认值，如果没有提供值，将使用默认值。默认值可以可选地是可调用的，将被调用来检索该值（例如在上面的示例中）。可用的字段类型如下所示**

`BinaryField`              		**二进制数据字段**
`BooleanField`     			**布尔型字段类型 **
`ComplexDateTimeField`	**精准时间类型**
`DateTimeField`   			**日期时间字段 **
`DecimalField`			**定点十进制数字段 **
`DictField`			**包装标准Python字典的字典字段。这与嵌入式文档类似，但结构没有定义。 **
`DynamicField`
`EmailField`     			**将输入验证为电子邮件地址的字段 **
`EmbeddedDocumentField`  				**嵌入式文档字段 **
`EmbeddedDocumentListField`
`FileField` 					**GridFS存储字段**
`FloatField`					**浮点数字段 **
`GenericEmbeddedDocumentField`
`GenericReferenceField`			**对任何文档子类的引用，在访问时自动取消引用(延迟地)**
`GenericLazyReferenceField`
`GeoPointField`
`ImageField` 						**图像文件存储区域**
`IntField`					**32位整数字段 **
`ListField`					**包装标准字段的列表字段，允许将该字段的多个实例用作数据库中的列表 **
`MapField`
`ObjectIdField`
`ReferenceField`				**对文档的引用**
`LazyReferenceField`
`SequenceField`
`SortedListField`				**一个ListField，在写入数据库之前对列表的内容进行排序。**
`StringField`					**一个unicode字符串字段 **
`URLField`
`UUIDField`
`PointField`
`LineStringField`
`PolygonField`
`MultiPointField`
`MultiLineStringField`
`MultiPolygonField`

----------


#### 3.2. 字段参数 ####
每个字段类型可以通过关键字参数进行定制。以下关键字参数可以在所有字段上设置：
> * `db_field` （默认：无） 
MongoDB字段名称。
> * `required` （默认：False）
如果设置为True，并且该字段未在文档实例上设置，ValidationError则在验证文档时将引发a 。
> * `default` （默认：无）
未为此字段设置值时使用的值。

默认参数的定义遵循Python的一般规则，这意味着在处理默认的可变对象时应该小心一些（如in `ListField`或`DictField`：
```
class ExampleFirst(Document):
    # 默认是一个空列表
    values = ListField(IntField(), default=list)

class ExampleSecond(Document):
    values = ListField(IntField(), default=lambda: [1,2,3])

class ExampleDangerous(Document):
    values = ListField(IntField(), default=[1,2,3])
```
> * `unique` （默认：False）
如果为True，则该集合中的任何文档都不会具有该字段的相同值。
>* `unique_with` （默认：无）
与此字段一起使用的字段名称（或字段名称列表）将不会具有相同值的集合中的两个文档。
>* `primary_key` （默认：False）
如果为True，则使用此字段作为集合的主键。 `DictField` 和`EmbeddedDocuments`都支持作为文档的主键。
>       ***如果设置，该字段也可通过`PK`字段访问*** 
>* `choices` （默认：无）
一个可迭代的（例如列表，元组或集合）选项，这个字段的值应该被限制到这个选项。
``` 
#可以是值的嵌套元组（存储在mongo中）和人类可读的密钥
SIZE  =  （（'S' ， 'Small' ），
        （'M' ， 'Medium' ），
        （'L' ， 'Large' ），
        （'XL' ， 'Extra Large' ），
        （'XXL' ， '特大号' ））
class Shirt （Document ）：
    size  =  StringField （max_length = 3 ， choices = SIZE 
```
```
#或者是一个只包含值的平坦迭代器
SIZE  =  （'S' ， 'M' ， 'L' ， 'XL' ， 'XXL' ）
class  Shirt （Document ）：
    size  =  StringField （max_length = 3 ， choices = SIZE ）
    
```
> * `**kwargs` （可选的）
您可以提供额外的元数据作为任意附加关键字参数 但是，您无法覆盖现有的属性。常用选项包括`help_tex`t和`verbose_name`，通常由窗体和小部件库使用

----------
### 3.2. 列表字段 ###
MongoDB允许存储项目列表。要想添加列表类型到 Document，请使用`ListField`字段类型。`ListField`将另一个字段对象作为其第一个参数，该参数指定列表中可以存储哪些类型元素
```
class Page(Document):
    tags = ListField(StringField(max_length=50))
```

----------


### 3.2. 嵌入式文档 ###
MongoDB能够在其他文档中嵌入文档。可以为这些嵌入式文档定义概要，就像它们可能用于常规文档一样。要创建嵌入式文档，只需像往常一样定义一个文档，但继承`EmbeddedDocument`而不是 `Document`：
```
class Comment(EmbeddedDocument):
    content = StringField()
```
要将文档嵌入到另一个文档中，请使用 `EmbeddedDocumentField`字段类型，将嵌入的文档类作为第一个参数提供：
```
class  Page （Document ）：
    comments  =  ListField （EmbeddedDocumentField （Comment ））

comment1  =  Comment （content = 'Good work！' ）
comment2  =  Comment （content = 'Nice article！' ）
page  =  Page （comments = [ comment1 ， comment2 ]）
```

----------


### 3.3. 字典字段 ###
通常，可以使用嵌入式文档而不是字典 - 通常建议使用`嵌入式文档`，因为`字典`不支持验证或自定义字段类型。但是，有时你不会知道你想要存储的结构; 在这种情况下`DictField`是合适的：
```python
class SurveyResponse(Document):
    date = DateTimeField()
    user = ReferenceField(User)
    answers = DictField()

survey_response = SurveyResponse(date=datetime.utcnow(), user=request.user)
response_form = ResponseForm(request.POST)
survey_response.answers = response_form.cleaned_data()
survey_response.save()
```
*字典可以存储复杂的数据，其他字典，列表，对其他对象的引用，所以是最灵活的可用字段类型*

----------
### 3.4. 引用字段 ###
要使引用字段可以存储到数据库中的其他文档，可以使用         `ReferenceField`。传入另一个文档类作为*构造函数*的*第一个参数*，然后将文档对象分配给该字段
```
class User(Document):
    name = StringField()

class Page(Document):
    content = StringField()
    author = ReferenceField(User)

john = User(name="John Smith")
john.save()

post = Page(content="Test Page")
post.author = john
post.save()
```
*该User对象在幕后自动变为引用，并在Page检索对象时解除引用。*

要添加一个`ReferenceField`引用正在定义的文档的引用，请使用该字符串'self'代替文档类作为ReferenceField构造函数的参数。要引用尚未定义的文档，请使用未定义文档的名称作为构造函数的参数
```
class Employee(Document):
    name = StringField()
    boss = ReferenceField('self')
    profile_page = ReferenceField('ProfilePage')

class ProfilePage(Document):
    content = StringField()
```

#### 3.4.1 一对多与ListField　####
如果通过引用列表实现*一对多关系*，则引用将存储为`DBRefs`，并且需要将对象的实例传递给查询
```
class User(Document):
    name = StringField()

class Page(Document):
    content = StringField()
    authors = ListField(ReferenceField(User))

bob = User(name="Bob Jones").save()
john = User(name="John Smith").save()

Page(content="Test Page", authors=[bob, john]).save()
Page(content="Another Page", authors=[john]).save()

# 查找所有由bob编写的网页，此处用到了高级查询，后面会介绍
Page.objects(authors__in=[bob])

# 查找所有由bob和john编写的网页
Page.objects(authors__all=[bob, john])

# 删除由bob编写的某篇文章（根据某个id查找到的）.
Page.objects(id='...').update_one(pull__authors=bob)

# 把john添加到某篇文章（根据某个id查找到的）.
Page.objects(id='...').update_one(push__authors=john)
```
#### 3.4.2 处理引用文档的删除 ####
默认情况下，MongoDB不检查数据的*完整性*，因此删除其他文档仍然存在引用的文档将*导致一致性问题*。`Mongoengine` 的`ReferenceField`增加了一些功能来防范这些类型的数据库完整性问题，为每个引用提供*删除规则规范*。通过`reverse_delete_rule`在`ReferenceField`定义上提供属性 来指定删除规则，如下所示
```
class ProfilePage(Document):
    ...
    employee = ReferenceField('Employee', reverse_delete_rule=mongoengine.CASCADE)
    #这个例子中的声明意味着当一个Employee对象被移除时，ProfilePage引用该雇员的那个也被移除。如果删除了整批员工，则所有链接的配置文件页面也会被删除
```
它的值可以采用以下任何常量：

>* `mongoengine.DO_NOTHING`
这是默认设置，不会执行任何操作。删除速度很快，但可能会导致数据库不一致或悬空引用。
>* `mongoengine.DENY`
如果仍存在对被删除对象的引用，则拒绝删除。
>* `mongoengine.NULLIFY`
删除任何仍然指向被删除对象的对象字段（使用MongoDB的“未设置”操作），从而有效地消除关系。
*即删除了被引用的字段，对引用它的字段无影响，举个例子，假如，文章的作者字段采用的是引用字段，那么作者一旦被删除，那么，由他写的文章仅仅是没有了作者，他的文章都还在。*
>* `mongoengine.CASCADE`
引用字段被删除，则引用此字段的文档也会被删除，
*举个例子，假如，文章的作者字段采用的是引用字段，那么作者一旦被删除，那么，由他写的文章也都被删除。*
>* `mongoengine.PULL`
从ListField（ReferenceField）的任何对象的字段中删除对该对象的引用（使用MongoDB的“拉”操作 ）。

#### 3.4.3 通用参考字段 ####
第二种场景也存在， `GenericReferenceField`。这允许您引用任何类型的Document，因此不会将 Document子类作为构造函数参数：
```
class Link(Document):
    url = StringField()

class Post(Document):
    title = StringField()

class Bookmark(Document):
    bookmark_object = GenericReferenceField()

link = Link(url='http://hmarr.com/mongoengine/')
link.save()

post = Post(title='Using MongoEngine')
post.save()

Bookmark(bookmark_object=link).save()
Bookmark(bookmark_object=post).save()
Note
```
*使用`GenericReferenceFields`的效率稍低于标准`ReferenceFields`，所以如果只引用一种文档类型，则更喜欢标准 `ReferenceField`*

----------


### 3.4. 唯一性约束 ###
`MongoEngine`允许你通过提供`unique=True`给Field构造函数来指定一个字段在集合中是唯一的。如果您尝试将与唯一字段具有相同值的文档保存为数据库的文档， `NotUniqueError`则会引发。您也可以使用下列方法指定多字段唯一性约束：`unique_with`可以是单个字段名称，也可以是字段名称的`列表`或`元组`
```
class User(Document):
    username = StringField(unique=True)
    first_name = StringField()
    last_name = StringField(unique_with='first_name')
```
---------
### 3.5 跳过文档验证保存 ###
您也可以通过设置`validate=False`调用save() 方法时跳过整个文档验证过程 
```
class Recipient(Document):
    name = StringField()
    email = EmailField()

recipient = Recipient(name='admin', email='root@localhost')
recipient.save()               # 会产生一个 ValidationError 错误
recipient.save(validate=False) # 不会
```

----------


##（四). 文档集合  ##
通过在文档中添加meta类字典属性，我们可以更改数据集合的名称，例如下方的例子，我们把*page*改为了*cmspage*
```
class Page(Document):
    title = StringField(max_length=200, required=True)
    meta = {'collection': 'cmsPage'}
```
----------
##（五). 索引  ##
您可以在集合上指定`索引`以加快查询速度。这是通过创建`indexes`在 `meta`字典中调用的索引规范列表完成的，其中索引规范可以是单个字段名称，包含多个字段名称的元组或包含完整索引定义的字典。

顺序可以通过在字段名前加上 +（用于升序）或-号（用于降序）来指定。请注意，方向只对多字段索引很重要。文本索引可以通过在字段名前添加一个$来指定。散列索引可以通过在字段名前添加＃来指定：
```
class Page(Document):
    category = IntField()
    title = StringField()
    rating = StringField()
    created = DateTimeField()
    meta = {
        'indexes': [
            'title',
            '$title',  # 文本索引
            '#title',  # 散列索引
            ('title', '-rating'),
            ('category', '_cls'),
            {
                'fields': ['created'],
                'expireAfterSeconds': 3600
            }
        ]
    }
```
如果采用字典，那么以下选项可用：

>* `fields` （默认：无）
要索引的字段。与上述相同的格式指定。
>* `cls` （默认值：True）
如果您有多态模型可以继承并 `allow_inheritance`打开，则可以配置索引是否应该将该_cls字段自动添加到索引的开头。
sparse （默认：False）
索引是否应该是稀疏的。
>* `unique` （默认：False）
索引是否应该是唯一的。
>* `expireAfterSeconds` （可选的）
允许您通过设置以秒为单位的时间过期来自动将某个字段中的数据过期。
注意

----------


##（六). 排序  ##
可以为您QuerySet使用的ordering属性 指定默认排序。排序将在QuerySet创建时应用 ，并且可以通过随后的order_by()覆盖：
```
from datetime import datetime

class BlogPost(Document):
    title = StringField()
    published_date = DateTimeField()

    meta = {
        'ordering': ['-published_date']
    }

blog_post_1 = BlogPost(title="Blog Post #1")
blog_post_1.published_date = datetime(2010, 1, 5, 0, 0 ,0)

blog_post_2 = BlogPost(title="Blog Post #2")
blog_post_2.published_date = datetime(2010, 1, 6, 0, 0 ,0)

blog_post_3 = BlogPost(title="Blog Post #3")
blog_post_3.published_date = datetime(2010, 1, 7, 0, 0 ,0)

blog_post_1.save()
blog_post_2.save()
blog_post_3.save()

# 通过默认的排序（降序），获取第一篇文章
# from BlogPost.meta.ordering
latest_post = BlogPost.objects.first()
assert latest_post.title == "Blog Post #3"

# 覆盖原来的默认排序规则，采用升序
first_post = BlogPost.objects.order_by("+published_date").first()
assert first_post.title == "Blog Post #1"
```

----------


##（七). 文档继承  ##
要创建您定义的文档的专用类型，您可以对其进行子类化并添加任何可能需要的额外字段或方法。 由于这是新类，它不是Document的直接子类，因此它不会存储在它自己的集合中; 它将使用与超类使用相同的集合。 这样可以更方便，更高效地检索相关文档 - 您只需在元数据中将allow_inheritance设置为True即可。
```
# Stored in a collection named 'page'
class Page(Document):
    title = StringField(max_length=200, required=True)

    meta = {'allow_inheritance': True}  #这里要设置为True，默认是False

# Also stored in the collection named 'page'
class DatedPage(Page):
    date = DateTimeField()
```

----------
##（八). 抽象类  ##
如果您想为一组Document类添加一些额外的功能，但您不需要或不需要继承的开销，则可以使用该 `abstract`属性`meta`。这不会打开文档继承，但可以让您保持代码干燥：
```
class BaseDocument(Document):
    meta = {
        'abstract': True,
    }
    def check_permissions(self):
        ...

class User(BaseDocument):
   ...
```
*现在，User类将有权访问继承的check_permissions方法，并且不会存储任何额外的_cls信息*
*这个特性在实际的web开发中作用很大*

[1]: https://github.com/AngleMAXIN/FlyBlog
[2]: http://docs.mongoengine.org/projects/flask-mongoengine/en/latest/#installing-flask-mongoengine


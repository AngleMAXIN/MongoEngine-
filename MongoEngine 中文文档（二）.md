

# MongoEngine 中文文档（一）

### API参考

- #### 连接

```python
mongoengine.connect（db = None，alias ='default'，** kwargs ）
```



```python
mongoengine.register_connection(alias, db=None, name=None, host=None, port=None, read_preference=Primary(), username=None, password=None, authentication_source=None, authentication_mechanism=None, **kwargs)
```

- #### 文档

```
class mongoengine.Document(*args, **values)
```

> ##### `objects`
>
> [`QuerySet`](http://docs.mongoengine.org/apireference.html#mongoengine.queryset.QuerySet)在访问时懒惰创建的对象 

> #####  `cascade_save`(**\*kwargs*) 
>
> 递归地保存文档上的任何引用和通用引用

> ##### `classmethod compare_indexes()`
>
> 比较MongoEngine中定义的索引和数据库中现有的索引。返回任何失踪/额外的索引



> ##### `classmethod create_index（keys，background = False，** kwargs ）`
>
> 如果需要，创建给定的索引。
>
> **参数:**
>
>    - keys——单个索引键或索引键列表(用于构建多字段索引);键可以用+或a前缀来确定索引顺序。
>    - background  —— 允许在后台创建索引

> ##### `delete(signal_kwargs=None, **write_concern)`
>
> 从数据库中删除文档。只有在以前保存文档时才会生效。
>
>
> **参数:**
>
> - signal_kwargs -(可选)kwargs字典要传递给信号调用。
> - write_concern——将传递额外的关键字参数，作为生成的getLastError命令的选项。例如,保存(…， --write_concern={w: 2, fsync: True}，…)将等到至少两个服务器记录了写操作并在主服务器上强制进行fsync



> ##### `classmethod drop_collection()` 
>
> 从数据库中删除与此文档类型相关的整个集合。
>
>
> 如果文档没有集合集就会引发操作错误(例如，如果文档是抽象的)
## 01 Flexibly Structured Data

**JavaScript Object Notation (JSON)**
- objects {}
  - String keys and Values: {'key1':value1, 'key2':value2,...}
  - Order of values is not important
- Arrays []
  - Series of values [value1, value2,...]
  - Order of values is important
- Values
  - Strings 'name':'Donny Winston'
  - Numbers 'id': 12345
  - true/false
  - null
  - Another array
  - Another object

| MongoDB        | JSON        | Python        | 说明                                 |
|----------------|-------------|---------------|--------------------------------------|
| Databases      | Objects     | Dictionaries  | 顶层结构                              |
| ↳ Collections  | Arrays      | Lists         | 数据库中的集合，相当于表             |
| ↳↳ Documents   | Objects     | Dictionaries  | 集合中的文档，相当于一行记录         |
| ↳↳↳ Subdocuments | Objects   | Dictionaries  | 文档中的嵌套结构                     |
| ↳↳↳ Values     | Value types| Value types   | 基本值，如字符串、数值、布尔值等     |
|                |             |               | + 特殊类型如 `datetime`, `regex` 等  |

**The Nobel Prize API data(base)**
```python
import requests
from pymongo import MongoClient

# 连接 MongoDB，本地默认是 localhost:27017
client = MongoClient()

# 创建/获取名为 "nobel" 的数据库
db = client["nobel"]

# 从 API 中获取 "prizes" 和 "laureates" 数据
for collection_name in ["prizes", "laureates"]:
    # 构造 API 请求地址
    url = "http://api.nobelprize.org/v1/{}.json".format(collection_name[:-1])
    
    # 发送请求并解析 JSON
    response = requests.get(url)
    documents = response.json()[collection_name]
    
    # 将数据插入到对应集合（collection）中
    db[collection_name].insert_many(documents)

print("数据已成功导入 MongoDB！")
```


**Accessing databases and collections**
| 用法           | 含义                       | 示例代码 |
|----------------|----------------------------|----------|
| 使用 `[]`      | `client` 是数据库的字典；`db` 是集合的字典 | `db = client["nobel"]`<br>`prizes_collection = db["prizes"]` |
| 使用 `.`       | 数据库是 `client` 的属性；集合是 `db` 的属性 | `db = client.nobel`<br>`prizes_collection = db.prizes` |



```python
# 使用空文档 {} 作为过滤器，表示不加任何条件（即查询所有文档）
filter = {}

# 统计集合中的文档数量
n_prizes = db.prizes.count_documents(filter)
n_laureates = db.laureates.count_documents(filter)

# 查找并返回一个文档（用于查看结构）,查找第一个匹配条件的文档（默认返回第一个）
doc = db.prizes.find_one(filter)
```

```python
# Save a list of names of the databases managed by client, 列出当前 MongoDB 中所有数据库的名称
db_names = client.list_database_names()
print(db_names)

# Save a list of names of the collections managed by the "nobel" database 列出 `nobel` 数据库中所有集合（collection）的名称
nobel_coll_names = client.nobel.list_collection_names()
print(nobel_coll_names)
```

## 02 Finding documents
Filters as (sub)documents: 
```python
# Count documents by providing a filterdocument to match.
filter_doc = {'born': '1845-03-27','diedCountry': 'Germany','gender': 'male','surname': 'Röntgen'}

db.laureates.count_documents(filter_doc)

# Simple filters
db.laureates.count_documents({'gender': 'female'})

db.laureates.count_documents({'diedCountry': 'France'})
```
**Comparison Operators**
| 操作符    | 含义               | 示例                                      |
|-----------|--------------------|-------------------------------------------|
| `$eq`     | 等于               | `{ age: { $eq: 25 } }`                     |
| `$ne`     | 不等于             | `{ age: { $ne: 25 } }`                     |
| `$gt`     | 大于               | `{ age: { $gt: 18 } }`                     |
| `$gte`    | 大于等于           | `{ age: { $gte: 18 } }`                    |
| `$lt`     | 小于               | `{ age: { $lt: 30 } }`                     |
| `$lte`    | 小于等于           | `{ age: { $lte: 30 } }`                    |
| `$in`     | 值在列表中         | `{ country: { $in: ['USA', 'France'] } }` |
| `$nin`    | 值不在列表中       | `{ status: { $nin: ['inactive', 'banned'] } }` |

---

**Logical Operators**

| 操作符    | 含义                     | 示例                                                         |
|-----------|--------------------------|--------------------------------------------------------------|
| `$and`    | 所有条件都满足           | `{ $and: [ { age: { $gt: 18 } }, { age: { $lt: 60 } } ] }`   |
| `$or`     | 任一条件满足             | `{ $or: [ { age: { $lt: 18 } }, { age: { $gt: 60 } } ] }`    |
| `$nor`    | 所有条件都不满足         | `{ $nor: [ { country: 'USA' }, { gender: 'male' } ] }`       |
| `$not`    | 条件不满足（对表达式取反） | `{ age: { $not: { $gt: 60 } } }`                             |

---

**Element Operators**

| 操作符    | 含义               | 示例                                   |
|-----------|--------------------|----------------------------------------|
| `$exists` | 字段是否存在       | `{ middleName: { $exists: false } }`  |
| `$type`   | 字段类型匹配       | `{ age: { $type: 'int' } }`           |

---
**Array Operators**

| 操作符        | 含义                            | 示例                                                               |
|---------------|----------------------------------|----------------------------------------------------------------------|
| `$all`        | 包含所有指定元素                | `{ tags: { $all: ['science', 'math'] } }`                          |
| `$elemMatch`  | 数组中至少一个元素匹配多个条件 | `{ scores: { $elemMatch: { $gt: 70, $lt: 90 } } }`                 |
| `$size`       | 数组长度匹配                    | `{ tags: { $size: 3 } }`                                           |

---

**Regular Expression**

| 操作符 | 含义                     | 示例                                 |
|--------|--------------------------|--------------------------------------|
| `/.../` 或 `$regex` | 正则匹配字符串         | `{ name: { $regex: '^Al' } }`       |
| `$options` | 设置匹配选项（如 `i` 表示忽略大小写） | `{ name: { $regex: 'einstein', $options: 'i' } }` |

---

**Expression Operators**

| 操作符     | 含义                         | 示例                                          |
|------------|------------------------------|-----------------------------------------------|
| `$expr`    | 使用表达式进行字段比较       | `{ $expr: { $gt: ['$score', '$passingScore'] } }` |
| `$mod`     | 求余                         | `{ age: { $mod: [5, 0] } }`                   |
| `$text`    | 全文搜索                     | `{ $text: { $search: 'physics' } }`           |
| `$where`   | 使用 JavaScript 表达式       | `{ $where: 'this.age > this.siblings.length' }` |

---

**Evaluation Operators**

| 操作符     | 含义                          | 示例                                               |
|------------|-------------------------------|----------------------------------------------------|
| `$cond`    | 条件语句                      | `{ $cond: { if: <condition>, then: <expr1>, else: <expr2> } }` |
| `$ifNull`  | 如果值为 null 则替换默认值   | `{ $ifNull: ['$middleName', 'N/A'] }`             |

---


## 02 Dot notation
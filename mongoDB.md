# MongoDB 使用方法
* 修改key  
```
def rename_key():
    for item in collection.find():
        collection.update({'_id': item['_id']}, {'$rename': {'a': 'b'}})
```

* 修改collection name
```
>>> collection.rename('name')
>>> print db.collection_names()
[u'name']
```


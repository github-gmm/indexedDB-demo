# indexedDB-demo
浏览器数据库indexedDB，实现了增删查改功能

1、**打开数据库**：  
```js
var request = window.indexedDB.open('数据库名称', 版本号);
```

2、**新建数据仓库（表）和索引**：都是在upgradeneeded的事件中执行  
&emsp;&emsp;首先在upgradeneeded事件中获取数据库：  
```js
request.onupgradeneeded = function(event) {
  db = event.target.result;
};
```  
&emsp;&emsp;然后判断数据仓库是否存在？不存在就新建：keyPath指定主键 / autoIncrement: true代表自动生成主键  
```js
request.onupgradeneeded = function(event) {
  db = event.target.result;
  var table;
  if (!db.objectStoreNames.contains('数据仓库名称')) {
    table = db.createObjectStore('数据仓库名称', { keyPath: 'id' });
  }
}
```  
&emsp;&emsp;新建索引：unique代表唯一，如果为true不能插入该字段相同的数据  
```js
request.onupgradeneeded = function(event) {
  db = event.target.result;
  var table;
  if (!db.objectStoreNames.contains('数据仓库名称')) {
    table = db.createObjectStore('数据仓库名称', { keyPath: 'id' });
  }
  table.createIndex('name', 'name', { unique: false });
  table.createIndex('email', 'email', { unique: true });
};
```

3、**新增数据记录**：readwrite读写权限
```js
let table = db.transaction(['数据仓库名称'], 'readwrite').objectStore('数据仓库名称')
let record = table.add({ id: 1, name: '张三', age: 24, email: 'zhangsan@example.com' });
record.onsuccess = function (event) {
  console.log('数据写入成功');
};
record.onerror = function (event) {
  console.log('数据写入失败');
};
```

4、**更新数据记录**：  
```js
let table = db.transaction(['数据仓库名称'], 'readwrite').objectStore('数据仓库名称')
let record = table.put({ id: 1, name: '李四', age: 24, email: 'zhangsan@example.com' });
record.onsuccess = function (event) {
  console.log('数据更新成功');
};
record.onerror = function (event) {
  console.log('数据更新失败');
};
```

5、**删除数据记录**：  
```js
let table = db.transaction(['数据仓库名称'], 'readwrite').objectStore('数据仓库名称')
let record = table.delete(1);
record.onsuccess = function (event) {
  console.log('数据删除成功');
};
record.onerror = function (event) {
  console.log('数据删除失败');
};
``` 
  
6、**查询数据记录**：  
&emsp;&emsp;根据条件查询：  
```js
// 方法一：  
let table = db.transaction(['数据仓库名称']).objectStore('数据仓库名称');
let record = objectStore.get(1); // 获取主键 = 1的值
record.onsuccess = function( event) {
  let result = request.result
  if (result) {
    ...
  } else {
    console.log('未获得数据记录');
  }
};
record.onerror = function(event) {
  console.log('事务失败');
};
```
```js
// 方法二： 
let table = db.transaction(['数据仓库名称'], 'readonly').objectStore('数据仓库名称');
let record = table.index('name').get('李四'); // 根据索引获取name = 李四的值
record.onsuccess = function (event) {
  let result = event.target.result;
  if (result) {
    ...
  }
};
```
  &emsp;&emsp;查询全部：
```js
let table = db.transaction('数据仓库名称').objectStore('数据仓库名称');
table.openCursor().onsuccess = function (event) {
  var cursor = event.target.result;
  if (cursor) {
    console.log('Id: ' + cursor.key);
    console.log('Name: ' + cursor.value.name);
    console.log('Age: ' + cursor.value.age);
    console.log('Email: ' + cursor.value.email);
    cursor.continue();
  } else {
    console.log('没有更多数据了！');
  }
};
```

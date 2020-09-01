---
title: Eloquent
date: 2019-10-27 15:53:03
tags: 
  - Eloquent
  - ORM
categories: Eloquent
---
## What is Eloquent?

<!-- more --> 
***

* Eloquent是一個ORM，Object Relational Mapping，翻譯為“對象關係映射”。
* 所謂“對象”，就是“模型(Model)”
* 對象關係映射，即為模型間關係。

## 一對一關係
* 這描述的是兩個模型之間一對一的關係。
* 假如我們有兩個模型：User 和Account，分別對應user和消費者，他們是一對一的關係，那麼如果我們要使用Eloquent 提供的一對一關係方法，表格結構應該是這樣的：
```
user: id ... ... account_id

account: id ... ... user_id
```
* 假設我們需要在User模型中查詢對應的Account表的信息，那麼應該是這樣的。
* `/app/models/User.php`:
```php=
<?php

class User extends Eloquent 
{
  protected $table = 'users';

  public function hasOneAccount()
  {
      return $this->hasOne('Account', 'user_id', 'id');
  }
}

```
* 當我們需要用到這種關係的時候
`$account = User::find(10)->hasOneAccount;`
* 此時得到的`$account` 即為`Account` 類的一個實例。

* 這裡最難的地方在於後面的兩個foreign_key 和local_key 的設置，
* 在User 類中，無論hasOne 誰，第二個參數都是`user_id`，第三個參數一般都是`id `。由於前面的`find(10)` 已經鎖定了id = 10，所以這段函數對應的SQL 為： `select * from account where user_id=10`。
* 使用Eloquent 時候的建議：
    1. 每一個Model 中都指定表名
    2. has one account 這樣的關係寫成`hasOneAccount()` 而不是簡單的`account()`
    3. 每次使用模型間關係的時候都寫全參數，不要省略

#### belongsTo() 

```php=
<?php

class Account extends Eloquent 
{
  protected $table = 'accounts';

  public function belongsToUser()
  {
    return $this->belongsTo('User', 'user_id', 'id');
  }
}
```

### 一對多關係
* 我們引入一個新的Model：Pay，付款記錄。表結構應該是這樣的：
```
user: id ... ...

pay: id ... ... user_id
```
* User 和Pay 具有一對多關係，換句話說就是一個User 可以有多個Pay，這樣的話，只在Pay 表中存在一個`user_id` 字段即可。
* `/app/models/User.php`:
```php=
<?php

class User extends Eloquent 
{
  protected $table = 'users';

  public function hasManyPays()
  {
    return $this->hasMany('Pay', 'user_id', 'id');
  }
}

```
* 需要用到這種關係的時候
`$accounts = User::find(10)->hasManyPays()->get();`
* 此時得到的`$accounts` 即為`Illuminate\Database\Eloquent\Collection` 類的一個實例。
* 這裡不是簡單的`-> hasOneAccount` 而是`->hasManyPays()->get()`，因為這裡是`hasMany`，操作的是一個對象集合。

#### 相應的belongsTo() 的用法跟上面一對一關係一樣：
```php=
<?php

class Pay extends Eloquent 
{
  protected $table = 'pays';

  public function belongsToUser()
  {
    return $this->belongsTo('User', 'user_id', 'id');
  }
}

```
### 多對多關係
* 多對多關係和之前的關係完全不一樣，因為多對多關係可能出現很多冗餘數據，用之前自帶的表存不下了。
* 我們定義兩個模型：Article 和Tag，分別表示文章和標籤，他們是多對多的關係。表格結構應該是這樣的：
```
article: id ... ...

tag: id ... ...

article_tag: article_id tag_id
```

* 在Model 中使用：
```php=
<?php

class Tag extends Eloquent 
{
  protected $table = 'tags';

  public function belongsToManyArticle()
  {
    return $this->belongsToMany('Article', 'article_tag', 'tag_id', 'article_id');
  }
}

```
* 需要注意的是，第三個參數是本類的id，第四個參數是第一個參數那個類的id。
* 使用跟hasMany 一樣：
`$tagsWithArticles = Tag::take(10)->get()->belongsToManyArticle()->get();`

* 這裡會得到一個非常複雜的對象，可以自行`var_dump()`。跟大家說一個訣竅，`var_dump()` 以後，用Chrome 右鍵“查看原始代碼”，就可以看到非常整齊的對象/數組展開了。
```php= 
public function parent_video()
{
    return $this->belongsToMany($this, 'video_hierarchy', 'video_id', 'video_parent_id');
}

public function children_video()
{
    return $this->belongsToMany($this, 'video_hierarchy', 'video_parent_id', 'video_id');

}
```
* 可以belongsToMany 自己

#### 其他關係
* Eloquent 還提供“遠層一對多關聯”、“多態關聯” 和“多態的多對多關聯” 這另外三種用法，經過上面的學習，我們已經掌握了Eloquent 模型間關係的基本概念和使用方法，剩下的幾種不常用的方法就留到我們用到的時候再自己探索吧。

### 重要技巧：關係預載入
* 在一對一關係中，需要一次性查詢出10個User並帶上對應的Account的話，需要給數據庫打1 + 10條SQL，這樣性能是很差的。
`$users = User::with('hasOneAccount')->take(10)->get()`
* 這樣生成的SQL 就是這個樣子的：
`select * from account where id in (1, 2, 3, ... ...)`
* 這樣1 + 10 條SQL 就變成了1 + 1 條，性能大增。
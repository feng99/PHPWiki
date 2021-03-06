# MVC操作

## 接收参数,并指定参数类型

```
// POST
$this->request->getPost('page', 'int'); 


$post = $this->getRequest()->getPost("uuid","int");
```





## 抛出异常

```
//参数错误
ErrorHandle::throwErr(Err::create(CoreLogic::INVALID_PARAM, ['phone']));

//操作失败
ErrorHandle::throwErr(Err::create(CoreLogic::OPERATE_ERROR, ['phone']));

//重复操作
ErrorHandle::throwErr(Err::create(CoreLogic::REPEAT_SEND_ERROR, ['']));

//没有权限
ErrorHandle::throwErr(Err::create(CoreLogic::PERMISSION_ERROR, ['phone']));
```

## 获取格式化时间

```
$day = intval(DateHelper::getFormatDate());
$week = intval(DateHelper::format('W'));
$month = intval(DateHelper::getFormatDateMonth());
```



# Mysql数据库操作

## create_time/update_time

```
`create_time` datetime DEFAULT CURRENT_TIMESTAMP,
`update_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
```









![image-20200820111434458](imgs/image-20200820111434458.png)



## 查询    使用参数绑定  杜绝SQL注入的问题

官方文档

https://www.kancloud.cn/jaya1992/phalcon_doc_zh/753278

不带注释说明的版本

```
public static function findList()
    {
        return self::find(
        	[
	        	'columns' => 'id,name',
            	"conditions" => "id > :id: AND is_delete = 0",
            	'bind' => [
                	"id" => 0
            	],
            	'bindTypes' => [
	            	"id" => Column::BIND_PARAM_INT
            	],
            	'order'      => "id desc",
			   'offset' => 5
            	"limit"	=> 20,

         	]
         )->toArray();
    }
```

带注释说明的版本

```
public static function findList()
    {
        return self::find(
        	[
        		//返回指定的字段
	        	'columns' => 'id,name',
        		//查询条件
            	"conditions" => "id > :id: AND is_delete = 0",
            	//参数绑定
            	'bind' => [
                	"id" => 0
            	],
            	'bindTypes' => [
	            	"id" => Column::BIND_PARAM_INT
            	],
            	//指定排序条件
            	'order'      => "id desc",
            	//将查询结果偏移一定量  配合limit做分页查询
            	'offset' => 5
            	//限制返回数据的条数
            	"limit"	=> 20,
            	
         	]
         )->toArray();
    }
```

另一种写法,面向对象的方式

没有哪种更好, 我跟习惯上面的写法.

```
$robots = Robots::query()
    ->where('type = :type:')
    ->andWhere('year < 2000')
    ->bind(['type' => 'mechanical'])
    ->order('name')
    ->execute();
```



## 防止SQL隐式类型转换问题

> 以下均能作用于In查询的数组

| 绑定类型 | 绑定类型常量               | 示例 |
| -------- | -------------------------- | ---- |
| string   | Column::BIND_PARAM_STR     |      |
| int      | Column::BIND_PARAM_INT     |      |
| double   | Column::BIND_PARAM_DECIMAL |      |
| bool     | Column::BIND_PARAM_BOOL    |      |
| blob     | Column::BIND_PARAM_BLOB    |      |
| null     | Column::BIND_PARAM_NULL    |      |



## IN查询

```
/**
 * in查询
 * @param $ids 数组
 * @return mixed
 */
public static function findInList($ids)
{
    return self::find(
        [
            'conditions' => ' id IN ({ids:array})',
            'bind'       => ['ids'=>$ids],
            'bindTypes' => [
	            	"ids" => Column::BIND_PARAM_INT
            ],
        ]
    );
}
```

## Like查询

```
$robots = $connection->fetchAll(
          "SELECT FROM robots WHERE name LIKE :name",
          \Phalcon\Db::FETCH_ASSOC,
          [
              "name" => "%robot%",
          ]
      );
```







## 悲观锁 的使用

**又称为  独占锁,排他锁**

使用此选项，`Phalcon\Mvc\Model` 将读取最新的可用数据，并在其读取的每一行上设置独占锁。

使用场景: 建议在只查询一条数据的情况下使用  

```
'for_update' => true
```

**使用样例**

```
public static function findList()
    {
        return self::findFirst(
        	[
        		//返回指定的字段
	        	'columns' => 'id,name',
        		//查询条件
            	"conditions" => "id > :id: AND is_delete = 0",
            	//参数绑定
            	'bind' => [
                	"id" => 0
            	],
            	//指定排序条件
            	'order'      => "id desc",
            	//将查询结果偏移一定量  配合limit做分页查询
            	'offset' => 5
            	//限制返回数据的条数
            	"limit"	=> 20,
            	
         	]
         )->toArray();
    }
```



## 共享锁的使用

使用此选项， `Phalcon\Mvc\Model` 将读取最新的可用数据，并在其读取的每一行上设置共享锁。

```
'shared_lock' => true
```





## 插入新数据后,获取最新的主键id

```
$newTag = new \App\Sdks\Models\TagModel();
$res = $newTag->create([
      'tag_name' => 'test1',
      'is_delete'=>0
       ]);
 //打印添加是否成功
 var_dump($res);
 //打印最新的主键id
 var_dump($newTag->tag_id);
```

## 用SQL语句的方式更新数据

> 使用场景:  与钱相关的数据,库存数量  钱包余额等    不能直接覆盖   
>
> 例子  
>
> 正确的SQL     set  count = count +1  
>
> 错误的SQL     set count = 2
>
> 原因:
>
> 并发情况下  直接赋值,会与其他线程,互相覆盖数据.

```
  //更新数据
  $db = DiHelper::getDB();
  $sql = "UPDATE tableName SET count = count + 1  WHERE `unique_key`=:unique_key";
  if (!$db->execute($sql, ['unique_key' => $data['unique_key']]) || $db->affectedRows() < 1) {
  	  //更新失败
      return false;
  }
```



## 用SQL语句的方式查询数据[分页]

```




```

常量 描述
Phalcon\Db::FETCH_NUM 返回带有数字索引的数组
Phalcon\Db::FETCH_ASSOC 返回带关联索引的数组
Phalcon\Db::FETCH_BOTH 返回包含关联索引和数字索引的数组
Phalcon\Db::FETCH_OBJ 返回⼀个对象⽽不是⼀个数组





## Orm方式 model  Dao 翻页

```
public static function getListPage($params)
    {
        $page       = self::getPageArgs($params);
        $filters    = self::getFilter($params);

        $conditions = [
            'conditions' => implode(' AND ', $filters['filter']),
            'bind' => $filters['bind'],
            'bindTypes' => $filters['bindTypes']
        ];

        $total = FollowDao::count($conditions);
        $data = [];
        if ($total > 0) {
            $where = [
                'columns'   => 'auuid,buuid,op_time,create_time',
                'offset'    => $page['start'],
                'limit'     => $page['pageSize'],
                'order'     => 'id DESC'
            ];
            $where = array_merge($conditions, $where);
            $data = FollowDao::find($where)->toArray();

            if($params['fans'] == 1){
                $data = self::listFill($data,'auuid');
            }else{
                $data = self::listFill($data,'buuid');
            }
        }
        return self::flashPageData($total, $data, $page);
    }
```



在日志中  记录SQL语句

```
	/**
     * 在日志中打印sql语句
     * @param $connection
     */
    public function logSql($connection)
    {
        if (CommonHelper::isPro()) {
            return;
        }

        try{
            $sql = $connection->getRealSQLStatement();
            if (mb_stripos(" " .$sql, "select") && !mb_stripos(" " .$sql, "INFORMATION_SCHEMA") ) {
                //var_dump($sql);die();
                $where   = $connection->getSqlVariables();
                if(count($where) > 0){
                    //pdo param bind
                    /*foreach($where as $k => $v){
                        $type = is_numeric($v) ? $pdo::PARAM_INT : $pdo::PARAM_STR;
                        $sth->bindValue(':'.$k, $v,$type) ;
                    }*/

                    //str_replace param
                    foreach($where as $k => $v){
                        $prefix = strpos(' '.$k,':') === false ? ':' : '' ;
                        if(!is_array($v)){
                            $sql = is_numeric($v) ? str_replace($prefix.$k,$v,$sql) : str_replace($prefix.$k,"'".$v."'",$sql);
                        }else{
                            foreach($v as $kn => $vn){
                                $sql = str_replace($prefix.$k.$kn,"'".$vn."'",$sql);
                            }
                        }
                    }
                }
                LogHelper::debug('sql', $sql);
                //var_dump($exp_sql);die();
            }
        }catch (PDOException $e){
            $error_msg  = $e->errorInfo;
            LogHelper::error(" explain_pdo",$error_msg);
        }
    }
```





# 操作Redis

```
$redis = DiHelper::getRedis();
$key   = sprintf(RedisKey::USER_RONGYUN_TOKEN, $uuid);
$redis->set($key,$register->token,RedisKey::expire(RedisKey::USER_RONGYUN_TOKEN));
```

## Redis内存锁的使用

```
$redis = DiHelper::getSharedRedis();
LockManager::init($redis);
//获取锁
LockManager::lock($key);

//释放锁
LockManager::unlock($key);
```



# Rpc客户端调用示例

> 必须用try{ }catch{ }包起来
>
> 定一个rpc返回异常的code  

```
	try {
            $client = DiHelper::getRpcClient();
            $rpcRes = $client->getUserInfo('120017');
            if($rpcRes['code'] !== 0){
                ErrorHandle::throwErr(Err::create(CoreLogic::RPC_ERROR, [$rpcRes['code'].$rpcRes['msg']]));
            }
        } catch (Exception $e) {
            throw new JsonFmtException($e->getMessage(), $e->getCode());
        }
```

# 打印日志 支持数组格式

```
//打印字符串
LogHelper::debug("userServer-UserLogin","userId:12007");

//打印数组  后续会自动进行转换为json
LogHelper::debug("userServer-UserLogin",['userId'=>12007,'nickName'=> '天下独舞']);
```



## 生成随机数 随机字符串

```
$random = new Random();

//生成一个0-N之间的随机数字
//可用于redis key加随机数,防止缓存雪崩问题
$number     = $random->number($n);

// ...
$bytes      = $random->bytes();

// 随机生成一个长度为$length的十六进制字符串
$hex        = $random->hex($length);

// 随机生成长度为$length的base64字符串
$base64     = $random->base64($length);

// 生产一个长度为$length的安全base64字符串.
$base64Safe = $random->base64Safe($length);

// 生成一个UUId
// See https://en.wikipedia.org/wiki/Universally_unique_identifier
$uuid       = $random->uuid();


```



## 数字计算  加减乘除

https://php.net/manual/en/function.bcadd.php

```
//加法 1+2
bcadd(1,2);

//减法 2-1
bcsub(2,1);

//乘法 1x2
bcmul(1,2);

//除法 2/1
bcdiv(2,1)
//除法  小数点后保留2位
bcdiv(2,1,2);
```











---



参考 https://segmentfault.com/a/1190000014166424#item-1-11

# 数据库事务

```
<?php

try {
	$db = DiHelper::getDB();
    // 开启一个事务 Start a transaction 
    $db->begin();

    // 执行一些SQL
    $connection->execute('DELETE `robots` WHERE `id` = 101');
    $connection->execute('DELETE `robots` WHERE `id` = 102');
    $connection->execute('DELETE `robots` WHERE `id` = 103');

    // 提交事务
    $db->commit();
} catch (Exception $e) {
    // 发生异常，回滚事务
    // 使用事务,必须捕获异常
    $db->rollback();
}
```



# 事务嵌套

**事务嵌套产生问题的原因:**

> 当执行一个START TRANSACTION指令时，会隐式的执行一个commit操作
>
> 翻译:开启一个新事务时,会自动提交上一个事务

**解决事务嵌套问题的关键在与  创建保存点**

> `InnoDB引擎`支持SQL语句
> `SAVEPOINT`  创建事务保存点  [也叫暂存点]
> `ROLLBACK TO SAVEPOINT` 回滚事务到指定的保存点
> `RELEASE SAVEPOINT` 删除事务的所有保存点
> `ROLLBACK`  回滚事务
>
> 一个事务支持设置多个保存点  每个保存点的名称不一样  如果名称一样则会新的覆盖旧的
>
> `ROLLBACK TO SAVEPOINT`语句将事务回滚到指定的保存点，而不会终止该事务
> 如果执行`COMMIT`或`ROLLBACK`事务,则将删除当前事务的所有保存点。

更高级的用法, phalcon提供了事务管理器Transaction Manager

```
try {
    //开启一个事务 Start a transaction
    $connection->begin();

    // 执行一些SQL
    $connection->execute('DELETE `robots` WHERE `id` = 101');

    try {
        // 开启一个嵌套事务
        $connection->begin();

        // 在嵌套事务中执行一些SQL
        $connection->execute('DELETE `robots` WHERE `id` = 102');
        $connection->execute('DELETE `robots` WHERE `id` = 103');

        // 创建一个保存点 Create a save point
        $connection->commit();
    } catch (Exception $e) {
        // 发生错误，回滚 嵌套事务
        // 使用事务,必须捕获异常
        $connection->rollback();
    }

    // 继续，执行更多SQL语句
    $connection->execute('DELETE `robots` WHERE `id` = 104');

    // 提交第一个事务
    $connection->commit();
} catch (Exception $e) {
    // 发生异常，回滚第一个事务
    // 使用事务,必须捕获异常
    $connection->rollback();
}
```

如果要在Phalcon中使用，记得override `Phalcon\Mvc\Model:: getSchema`

phalcon提供了事务管理器
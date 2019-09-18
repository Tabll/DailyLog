# Laravel 查询构造
*Laravel 的查询构造器已使用 PDO 参数绑定来防止 SQL 注入*

## 获取结果
### 从数据表中获取所有的数据
```php
<?php
namespace App\Http\Controllers;
use Illuminate\Support\Facades\DB;
use App\Http\Controllers\Controller;
class UserController extends Controller
{
    /**
     * 显示所有应用程序用户的列表
     * @return Response
     */
    public function index()
    {
        $users = DB::table('users')->get();
        return view('user.index', ['users' => $users]);
    }
}
```
`get` 方法会返回一个包含 `Illuminate\Support\Collection` 的结果，其中每个结果都是一个 PHP `StdClass` 对象的一个实例。可以通过访问字段作为对象的属性来访问每列的值：
```php
foreach ($users as $user) {
    echo $user->name;
}
```

### 从数据表中获取单个列或行
```php
$user = DB::table('users')->where('name', 'John')->first();
echo $user->name;

$email = DB::table('users')->where('name', 'John')->value('email');
$email = DB::table('users')->where('name', 'John')->email; // 这样也行
```

### 获取一列的值
```php
$titles = DB::table('roles')->pluck('title');
foreach ($titles as $title) {
    echo $title;
}

// 也可以在返回的集合中指定字段的自定义键值
$roles = DB::table('roles')->pluck('title', 'name');
foreach ($roles as $name => $title) {
    echo $title;
}
```

### 结果分块
```php
// 一次处理 user 表中的 100 个记录
DB::table('users')->orderBy('id')->chunk(100, function ($users) {
    foreach ($users as $user) {
        // DO SOMETHING
    }
});

// 可以在闭包中返回 false 能够阻止进一步的分块处理
DB::table('users')->orderBy('id')->chunk(100, function ($users) {
    // DO SOMETHING
    return false; // 停止进一步处理
});
```

### 聚合
查询构造器还提供了各种聚合方法，如 `count`、 `max`、 `min`、 `avg` 和 `sum`
```php
$users = DB::table('users')->count();
$price = DB::table('orders')->max('price');
```
也可以将这些方法和其它语句结合起来：
```php
$price = DB::table('orders')
                ->where('finalized', 1)
                ->avg('price');
```

## Selects
### 指定一个 Select 语句
```php
// 选取指定字段
$users = DB::table('users')->select('name', 'email as user_email')->get();
```
使用 `distinct` 方法强制让查询返回不重复的结果
```php
$users = DB::table('users')->distinct()->get();
```
如果已有一个查询构造器实例，并希望在现有的 `select` 语句中加入一个字段，则可以使用 `addSelect` 方法
```php
$query = DB::table('users')->select('name');
$users = $query->addSelect('age')->get();
```

## 原生表达式
```php
$users = DB::table('users')
                     ->select(DB::raw('count(*) as user_count, status'))
                     ->where('status', '<>', 1)
                     ->groupBy('status')
                     ->get();
```
> **原生表达式将会被当作字符串注入到查询中，所以主要注意 SQL 注入漏洞。**

### 原生方法
可以使用以下的方法代替 `DB::raw` 将原生表达式插入查询的各个部分：

**`selectRaw`**
```php
// selectRaw 方法可以用来代替 select(DB::raw(...))。这个方法的第二个参数接受一个可选的绑定参数的数组
$orders = DB::table('orders')
                ->selectRaw('price * ? as price_with_tax', [1.0825])
                ->get();
```

**`whereRaw / orWhereRaw`**
```php
// 可以使用 whereRaw 和 orWhereRaw 方法将原生的 where 语句注入到查询中。这些方法接受一个可选的绑定数组作为他们的第二个参数
$orders = DB::table('orders')
                ->whereRaw('price > IF(state = "TX", ?, 100)', [200])
                ->get();
```

**`havingRaw / orHavingRaw`**
```php
// havingRaw 和 orHavingRaw 方法可用于将原生字符串设置为 having 语句的值
$orders = DB::table('orders')
                ->select('department', DB::raw('SUM(price) as total_sales'))
                ->groupBy('department')
                ->havingRaw('SUM(price) > 2500')
                ->get();
```

**`orderByRaw`**
```php
// orderByRaw 方法可用于将原生字符串设置为 order by 语句的值
$orders = DB::table('orders')
                ->orderByRaw('updated_at - created_at DESC')
                ->get();
```

## Joins
### Inner Join 语句
```php
// 内连接
$users = DB::table('users')
            ->join('contacts', 'users.id', '=', 'contacts.user_id')
            ->join('orders', 'users.id', '=', 'orders.user_id')
            ->select('users.*', 'contacts.phone', 'orders.price')
            ->get();
```

### Left Join / Right Join 语句
```php
// 左连接
$users = DB::table('users')
            ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
            ->get();
```

### Cross Join 语句
```php
// 交叉连接
$users = DB::table('sizes')
            ->crossJoin('colours')
            ->get();
```

### 高级 Join 语句
```php
// 可以使用闭包
DB::table('users')
        ->join('contacts', function ($join) {
            $join->on('users.id', '=', 'contacts.user_id')->orOn(...);
        })
        ->get();

// 可以使用 where 用来比较值和对应的字段
DB::table('users')
        ->join('contacts', function ($join) {
            $join->on('users.id', '=', 'contacts.user_id')
                 ->where('contacts.user_id', '>', 5);
        })
        ->get();
```

## Unions
可以使用 `union` 使两个查询合并
```php
$first = DB::table('users')
            ->whereNull('first_name');

$users = DB::table('users')
            ->whereNull('last_name')
            ->union($first)
            ->get();
```
也可以使用 `unionAll` 方法，它与 `union` 有相同的用法

## Where 语句
### 简单的 Where 语句
```php
// 查询年龄为10的用户
$users = DB::table('users')->where('age', '=', 10)->get();
$users = DB::table('users')->where('age', 10)->get();
```
```php
// 其它运算符示例
$users = DB::table('users')
                ->where('age', '>=', 10)
                ->get();
$users = DB::table('users')
                ->where('age', '<>', 10)
                ->get();
$users = DB::table('users')
                ->where('name', 'like', '%述%')
                ->get();
// 也可以传递条件数组给where
$users = DB::table('users')->where([
    ['status', '=', '1'],
    ['subscribed', '<>', '1'],
])->get();
```

### Or 语句
```php
$users = DB::table('users')
                    ->where('votes', '>', 100)
                    ->orWhere('name', 'John')
                    ->get();
```

### 其它 Where 语句
**`whereBetween`** / **`whereNotBetween`**
```php
$users = DB::table('users')->whereBetween('votes', [1, 100])->get();
$users = DB::table('users')->whereNotBetween('votes', [1, 100])->get();
```

**`whereIn`** / **`whereNotIn`**
```php
$users = DB::table('users')->whereIn('id', [1, 2, 3])->get();
$users = DB::table('users')->whereNotIn('id', [1, 2, 3])->get();
```

**`whereNull`** / **`whereNotNull`**
```php
$users = DB::table('users')->whereNull('updated_at')->get();
$users = DB::table('users')->whereNotNull('updated_at')->get();
```

**`whereDate`** / **`whereMonth`** / **`whereDay`** / **`whereYear`** / **`whereTime`**
```php
$users = DB::table('users')->whereDate('created_at', '2020-1-1')->get();
$users = DB::table('users')->whereMonth('created_at', '12')->get();
$users = DB::table('users')->whereDay('created_at', '31')->get();
$users = DB::table('users')->whereYear('created_at', '2020')->get();
$users = DB::table('users')->whereTime('created_at', '=', '12:58')->get();
```

**`whereColumn`**
```php
// 比较两个字段的大小
$users = DB::table('users')->whereColumn('first_name', 'last_name')->get();
$users = DB::table('users')
                ->whereColumn([
                    ['first_name', '=', 'last_name'],
                    ['updated_at', '>', 'created_at']
                ])->get();
```

### 参数分组
```php
DB::table('users')
            ->where('name', '=', 'John')
            ->orWhere(function ($query) {
                $query->where('votes', '>', 100)
                      ->where('title', '<>', 'Admin');
            })
            ->get();
```
以上代码会生成以下 SQL 语句
```sql
SELECT * FROM users WHERE name = 'John' OR (votes > 100 AND title <> 'Admin')
```

### Where Exists 语句
```php
DB::table('users')
            ->whereExists(function ($query) {
                $query->select(DB::raw(1))
                      ->from('orders')
                      ->whereRaw('orders.user_id = users.id');
            })
            ->get();
```
以上代码会生成以下 SQL 语句
```sql
SELECT * FROM users
WHERE EXISTS (
    SELECT 1 FROM orders WHERE orders.user_id = users.id
)
```

### JSON where 语句
> 仅在对 JSON 类型支持的数据库上）。目前，本特性仅支持 MySQL 5.7+ 和 Postgres 数据库
```php
// 可以使用 -> 运算符来查询 JSON 列数据
$users = DB::table('users')
                ->where('options->language', 'en')
                ->get();

$users = DB::table('users')
                ->where('preferences->dining->meal', 'salad')
                ->get();
```

## Ordering, Grouping, Limit, & Offset
**`orderBy`**
```php
$users = DB::table('users')->orderBy('name', 'desc')->get(); // asc / desc
```

**`latest`** / **`oldest`**
```php
// 默认是对 created_at 排序。或者可以传要排序的字段名称
$user = DB::table('users')->latest()->first();
```

**`inRandomOrder`**
```php
// 获取一个随机用户
$randomUser = DB::table('users')->inRandomOrder()->first();
```

**`groupBy`** / **`having`**
```php
// groupBy 和 having 方法可用来对查询结果进行分组
$users = DB::table('users')
                ->groupBy('account_id')
                ->having('account_id', '>', 100)
                ->get();
$users = DB::table('users')
                ->groupBy('first_name', 'status')
                ->having('account_id', '>', 100)
                ->get();
```
高级用法可以参考 **[havingRaw](#原生方法)**  

**`skip`** / **`take`**
```php
// 两种都可以，公司基本上都是使用的是最下面一种
$users = DB::table('users')->skip(10)->take(5)->get();
$users = DB::table('users')->offset(10)->limit(5)->get();
```

## 条件语句
```php
$role = $request->input('role');
// 通过 when 判断情况为 true 时才会执行查询
$users = DB::table('users')
                ->when($role, function ($query) use ($role) {
                    return $query->where('role_id', $role);
                })
                ->get();
```
```php
$sortBy = null;
// 如果第一个参数为 false 时则执行第二个闭包，否则执行第一个闭包
$users = DB::table('users')
                ->when($sortBy, function ($query) use ($sortBy) {
                    return $query->orderBy($sortBy);
                }, function ($query) {
                    return $query->orderBy('name');
                })
                ->get();
```
```php
// 实例
->when(!is_null($parameter->is_connect_contract), function ($query) use ($parameter) {
    if ($parameter->is_connect_contract == Debit::CONNECT_CONTRACT) {
        $query->join('field_order_items', 'field_order_items.debit_id', '=', 'debits.id')
            ->whereExists(function ($query) {
                $query->select('*')
                    ->from('order_relation_contracts')
                    ->whereRaw('order_relation_contracts.order_item_id = field_order_items.id')
                    ->whereIn('order_relation_contracts.contract_type', [
                        'contractPur', 'contractSup', 'contractNor'
                    ]);
            });
    } elseif ($parameter->is_connect_contract == Debit::NOT_CONNECT_CONTRACT) {
        $query->join('field_order_items', 'field_order_items.debit_id', '=', 'debits.id')
            ->whereExists(function ($query) {
                $query->select('*')
                    ->from('order_relation_contracts')
                    ->whereRaw('order_relation_contracts.order_item_id <> field_order_items.id');
            });
    } else {
        die();
    }
})
```

## Inserts
```php
DB::table('users')->insert(
    ['email' => 'john@example.com', 'votes' => 0]
);
// 多条记录
DB::table('users')->insert([
    ['email' => 'taylor@example.com', 'votes' => 0],
    ['email' => 'dayle@example.com', 'votes' => 0]
]);
```
```php
// 存在自增 ID 则使用 insertGetId 插入记录并获取 ID
$id = DB::table('users')->insertGetId(
    ['email' => 'john@example.com', 'votes' => 0]
);
```
*非 5.5 版本*
```php
// 忽略重复插入记录到数据库的错误
DB::table('users')->insertOrIgnore([
    ['id' => 1, 'email' => 'taylor@example.com'],
    ['id' => 2, 'email' => 'dayle@example.com']
]);
```

## Updates
```php
DB::table('users')->where('id', 1)->update(['votes' => 1]);
```

### 更新或者新增
*非 5.5 版本*
```php
// 该方法将首先尝试使用第一个参数的键和值对来查找匹配的数据库记录
// 如果记录存在，则使用第二个参数中的值去更新记录
// 如果找不到记录，将插入一个新记录，更新的数据是两个数组的集合
DB::table('users')
    ->updateOrInsert(
        ['email' => 'john@example.com', 'name' => 'John'],
        ['votes' => '2']
    );
```

### 更新 JSON 字段
```php
DB::table('users')->where('id', 1)->update(['options->enabled' => true]);
```

### 自增 & 自减
```php
DB::table('users')->increment('votes');
DB::table('users')->increment('votes', 5); // 自增 5
DB::table('users')->decrement('votes');
DB::table('users')->decrement('votes', 5); // 自减 5
// 指定更新字段
DB::table('users')->increment('votes', 666, ['name' => 'Tabll']);
```

## Deletes
```php
DB::table('users')->delete();
DB::table('users')->where('votes', '>', 100)->delete();
// 清空表，删除所有行，并重置自动递增 ID 为零
DB::table('users')->truncate();
```

## 悲观锁
```php
// 共享锁，防止选中的行被篡改，直到事务被提交
DB::table('users')->where('votes', '>', 100)->sharedLock()->get();
// 更新锁，同样的可避免行被其它共享锁修改或选取
DB::table('users')->where('votes', '>', 100)->lockForUpdate()->get();
```

## 调试
```php
// 显示调试信息，停止执行
DB::table('users')->where('votes', '>', 100)->dd();
// 显示调试信息，不停止执行
DB::table('users')->where('votes', '>', 100)->dump();

DB::table('users')->toSql(); // 输出 select * from `users`

DB::enableQueryLog(); // 开启日志
$debits = $debitService->debitList($parameters); //此处为需要提取的数据库执行语句
return response()->success(DB::getQueryLog()); // 打印日志
dd(DB::getQueryLog()); // 打印日志
```

> *以上内容来自 Laravel 文档，有增删改*

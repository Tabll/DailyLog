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
$users = DB::table('users')
            ->join('contacts', 'users.id', '=', 'contacts.user_id')
            ->join('orders', 'users.id', '=', 'orders.user_id')
            ->select('users.*', 'contacts.phone', 'orders.price')
            ->get();
```

### Left Join 语句
```php
$users = DB::table('users')
            ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
            ->get();
```

### Cross Join 语句
```php
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

## Where 语句

## Ordering, Grouping, Limit, & Offset

## 条件语句

## Inserts

## Updates

## Deletes

## 悲观锁


<style>
table {
    width: 100%; /*表格宽度*/
    margin: 15px auto; /*外边距*/
}
/* table tbody tr:nth-child(2n) {
    background: rgba(158,188,226,0.12); 
} */
table tr:hover {
    background: #efefef; 
}
table th,
table td {
  height: 35px; /*统一每一行的默认高度*/
  border: 1px solid #dedede; /*内部边框样式*/
  padding: 0 10px; /*内边距*/
}
</style>

### 命名空间
*目的：为解决命名冲突而产生的一种包装 类、函数和常量 的方法，另一个重要的作用是为组件化开发提供了可能*

#### 命名空间的定义
通过关键字 `namespace` 来定义的，如果一段PHP代码需要用它来封装，则命名空间的声明需要在这部分代码之前，例如：
```php
<?php
namespace App\Http;
use Illuminate\Foundation\Http\Kernel as HttpKernel;
class Kernel extends HttpKernel {
    // 其它代码内容
}
```

获取和使用当前命名空间的方法：分别是 `_NAMESPACE_` 魔术常量和 `namespace` 关键字
例：命名空间 `App\Http;`
```php
<?php
namespace App\Http;
echo _NAMESPACE_;
// 输出：“App\Http”
```
```php
<?php
echo _NAMESPACE_;
// 输出：“”
```
通过 `namespace` 显式访问：
```php
<?php
namespace App\Http;
class Kernel {
    // 其它代码内容
}

$a = new namespace\ Kernel(); // 即new App\Http\Kernel();
```
#### 命名空间的使用
有三种使用的方式：
- 第一种：非限定名称或不包含前缀的类名称
  - 例如 `$a = new Kernel()`
- 第二种：限定名称或包含前缀的名称
  - 例如 `$a = new subnamespace\Kernel()`
- 第三种：完全限定名称或包含了全局前缀操作符的名称
  - 例如 `$a = new \currentnamespace\Kernel()`

此外，为了使用简洁，因此命名空间允许通过导入外部引用或别名引用的方式指定相应的类和命名空间，即可以为类名称使用别名，也可以为命名空间使用别名，如下例：
```php
<?php
namespace App\Http\Controllers\Auth;
use App\Http\Controllers\Controller;
use App\Models\Category as DataCategory;
use App\Models;
class AuthController extends Controller {
    //DO SOMETHING
}
```
```php
// 实例化 App\Http\Controllers\Controller 对象：
$obj1 = new Controller();
// 实例化 App\Http\Controllers\Auth\AuthController 对象：
$obj2 = new AuthController();
// 实例化 App\Models\Category 对象：
$obj3 = new DataCategory();
// 实例化 App\Models\Records 对象：
$obj4 = new Models\Records();
// 实例化 Models\Records 对象：
$obj5 = new \Models\Records();
```
PHP 的命名空间只支持导入类，不支持导入函数或常量，命名空间名称解析需要遵循如下原则：
1. 对完全限定名称的函数、类和常量可以直接解析。例如，`new \A\B` 解析为 类 `A\B`
2. 对所有非限定名称和非完全限定名称的函数、类和常量，根据当前导入的命名空间进行转换。例如，如果命名空间 `A\B\C` 被导入，那么 `new C\D\E()` 就会被转换为 `new A\B\C\D\E()`
3. 在命名空间内部，所有的没有根据导入规则转换的非限定名称和非完全限定名称均会在其前面加上当前的命名空间名称。例如，在命名空间 `A\B` 内部调用 `C\D\E()` 时，如果没有导入命名空间 `A\B\C`，则 `new C\D\E()` 会被转换为 `new A\B\C\D\E()`
4. 在命名空间内部（例如 `A\B`），对非限定名称和非完全限定名称的函数进行调用时，现在当前命名空间下解析，如果查找不到再在全局空间下查找，即：
    a. 在当前命名空间中查找名为 `A\B\foo()` 的函数
    b. 尝试查找并调用全局 `（global）` 空间中的函数 `foo()`
5. 在命名空间（如 `A\B`）内部对非限定名称和非完全限定名称的类进行调用时，只会在当前命名空间下解析。如对 `new C()`，只会转换为`new A\B\C()`，如果想引用全局命名空间中的全局类，必须使用完全限定名称 `new \C()`

### 文件包含
#### `include` 和 `require` 关键字
`require` 出错产生 `E_COMPILE_ERROR` 级别的错误
`include` 出错产生 `E_WARRNING` 级别的错误

在包含一个只有文件名的文件时，如 `include "file.php"` 时，系统会先在 PHP 配置文件中 `include_path` 指定的目录下进行逐一寻找，如果在这些目录下都没有找到，最后才在脚本文件所在的工作目录下寻找，未找到即报错
	默认情况下 PHP 配置文件中关于 `include_path` 的内容，其中 . 表示当前路径：
```php
include_path = ".;c:\php\includes"
// "../"表示在当前目录的父目录下寻找
```

#### 类的自动加载
通过魔术方法 `__autoload(string $class)` 实现，也可以通过函数 `sql_autoload_register` 注册一个自动加载方法，例如：
```php
function __autoload($class) {
    require_once($class . ".php");
}
```

当使用一个类名时，如果该类没有被当前文件包含，则会自动调用 `__autoload($class)` 魔术方法，而其中的 `$class` 为使用类的名称。但在实际应用中，通常使用 `sql_autoload_register` 注册自定义的函数作为自动加载类的实现，因为 `__autoload()` 魔术函数只可以定义一次，而 `sql_autoload_register` 可以将多个类自动加载方法注册到队列中，即创建了 `autoload` 函数的队列，在调用时按照定义时的顺序逐个执行。其函数定义如下：
```php
    bool sql_autoload_register([callable $autoload_function [, bool $throw = true [, bool $prepend = false ]]])
```
通过 `sql_autoload_register()` 函数自动加载的函数可以是全局函数，也可以是某个类实例对象的函数，即通过 `array("对象名", "函数名")` 注册。如果没有提供相应的函数参数，则自动注册 `autoload` 的默认实现函数 `sql_autoload()` 为类的自动加载函数。其中，`throw` 参数为 `true` 时，当类的自动加载函数无法成功注册时会抛出异常；当 `prepend` 参数为 `true` 时，`sql_autoload_register()` 会添加类的自动加载函数到队首，而不是队尾。具体实例如下：
```php
public function register ($prepend = true) {
    sql_autoload_register(array($this, 'loadClass'), true, $prepend);
}
```

#### Laravel 中的实现方案
```php
TODO -> P45
```

### 匿名函数
> 匿名函数（Anonymous functions）也叫闭包函数（Closure），即没有指定名称的函数，经常用作回调函数（callback）参数的值。
Closure（闭包）类也称匿名函数类，匿名函数（PHP 5.3+）本身就是这个类型的对象。在Laravel框架中，大量的使用了匿名函数，使得框架更加紧凑灵活。

#### 匿名函数的使用
如下：
```php
<?php
    $array = array(1, 2, 3, 4);
    // array_walk 使用用户自定义函数对数组中的每个元素做回调处理
    array_walk($array, function($value) { // 输出1234
        echo $value;
    });
?>
```
若要在父作用域中继承变量则使用 use 关键词来继承作用域外的变量，例如：
```php
<?php
    function getCounter() {
        $i = 0;
        return function() use ($i) {
            Echo ++$i;
        };
    }
    $counter = getCounter();
    $counter(); // 输出1
    $counter(); // 输出1
?>
```
若要改变上层变量的值，则需要通过引用的方式传递 `(&$i)`

#### Laravel 框架中的应用
例如：
文件 `Illuminate\Routing\ControllerServiceProvider.php`
```php
<?php
namespace Illuminate\Routing;
use Illuminate\Support\ServiceProvider;
class ControllerServiceProvider extends ServiceProvider
{
    public function register()
    {
        // 注册服务提供者
        $this->app->singleton('illuminate.route.dispather', function($app) {
            return new CountrollerDispatcher($app['router'], $app);
        });
    }
}
```
上例中 `$this->app->singleton()` 函数的作用是将服务名 `illuminate.route.dispatcher` 与后面的提供服务的匿名函数绑定起来，用于服务解析，服务就是通过匿名函数实现的

### PHP 中的特殊语法
#### 魔术方法
|  方法  |  说明  |
| :---------: | :--------- |
|`__construct()`|构造函数|
|`__destruct()`|析构函数|
|`__set()`|给不可访问属性赋值时|
|`__get()`|读取不可访问属性的值时|
|`__isset()`|当对不可访问属性调用 isset() 或 empty() 时|
|`__unset()`|当对不可访问属性调用 unset() 时|
|`__sleep()`|serialize() 函数会检查类中是否存在一个魔术方法 __sleep()。如果存在，该方法会先被调用，然后才执行序列化操作。此功能可以用于清理对象，并返回一个包含对象中所有应被序列化的变量名称的数组|
|`__wakeup()`|与之相反，unserialize() 会检查是否存在一个 __wakeup() 方法。如果存在，则会先调用 __wakeup 方法，预先准备对象需要的资源|
|`__toString()`|用于一个类被当成字符串时应怎样回应。例如 echo $obj; 应该显示些什么|
|`__invock()`|当尝试以调用函数的方式调用一个对象时|
|`__clone()`|复制完成时|
|`__call()`|在对象中调用一个不可访问方法时|
|`__callStatic()`|在静态上下文中调用一个不可访问方法时|
|`__autoload()`|在试图使用尚未被定义的类时|
|`__set_state()`|当调用 var_export() 导出类时，此静态 方法会被调用|
|`__debugInfo()`|转储对象以获取应显示的属性时 ，var_dump（）调用此方法|

#### 魔术常量
|  方法  |  说明  |
| :---------: | :---------: |
|`__LINE__`|文件中的当前行号|
|`__FILE__`|文件的完整路径和文件名。如果用在被包含文件中，则返回被包含的文件名|
|`__DIR__`|文件所在的目录。如果用在被包括文件中，则返回被包括的文件所在的目录|
|`__FUNCTION__`|返回该函数被定义时的名字（区分大小写）|
|`__CLASS__`|返回该类被定义时的名字（区分大小写）|
|`__TRAIT__`|返回 trait 被定义时的名字（区分大小写）|
|`__METHOD__`|返回该方法被定义时的名字（区分大小写）|
|`__NAMESPACE__`|当前命名空间的名称（区分大小写）|

### 反射
```php
<?php
class A {
    public function call()
    {
        echo "Hi";
    }
}
$ref = new ReflectionClass('A');
$inst = $ref->newInstanceArgs();
$inst->call(); // 输出 Hi
```

在很多情况下反射机制能够使代码更高效，能够在运行时根据信息动态确定需要实例化哪一个类。这是通过反射机制获取需要实例化类的构造函数信息并完成相应的实例化。Laravel 中的服务容器解析服务的过程中使用到了反射机制：

文件 `Illuminate\Container\Container.php`
```php
/**
 * Instantiate a concrete instance of the given type.
 * @param  string  $concrete
 * @return mixed
 * @throws \Illuminate\Contracts\Container\BindingResolutionException
 */
public function build($concrete)
{
    if ($concrete instanceof Closure) {
        return $concrete($this, $this->getLastParameterOverride());
    }
    $reflector = new ReflectionClass($concrete);
    if (! $reflector->isInstantiable()) {
        return $this->notInstantiable($concrete);
    }
    $this->buildStack[] = $concrete;
    $constructor = $reflector->getConstructor();
    if (is_null($constructor)) {
        array_pop($this->buildStack);
        return new $concrete;
    }
    $dependencies = $constructor->getParameters();
    $instances = $this->resolveDependencies(
        $dependencies
    );
    array_pop($this->buildStack);
    return $reflector->newInstanceArgs($instances);
}
```

### 后期静态绑定
自 PHP `5.3`开始增加此功能，用于在继承范围内引用静态调用的类，即在类的继承过程中，使用的类不再是当前类，而是调用的类。
后期静态绑定使用关键词 `static` 实现
```php
<?php
class A
{
    public static function call()
    {
        echo "class A<br>";
    }
    public static function test()
    {
        self::call();
        static::call();
    }
}
class B extends A
{
    public static function call()
    {
        echo "class B<br>"
    }
}
B::test();
```

### 其它新特性
#### `trait`
1. 当前类中的方法会覆盖掉 `trait` 中的方法，`trait` 中的方法会覆盖掉基类中的方法
2. `use` 多个基类用逗号隔开即可
3. 如果两个 `trait` 中都插入了一个同名的方法，需要解决冲突，否则会产生一个致命错误。需要使用 `insteadof` 操作符来明确指定使用冲突方法中的哪一个，同时，也可以用 `as` 操作符将其中一个冲突的办法以另一个名称来引入
4. 使用 `as` 语法可以用来调整方法的访问控制
5. 在 `trait` 中可以使用抽象方法，使得类中必须实现这个抽象方法
6. 在 `trait` 中可以使用静态方法的静态变量
7. 在 `trait` 中同样可以定义属性
#### 简化的三元运算符
```php
$value = (exprl1) ?? null
// exprl1 为 true 则为 exprl1 否则为 null
```

### 参考文献
`[1] 陈昊，陈远征 等．Laravel 框架关键技术解析[M]．北京：电子工业出版社，2019：39-63．`

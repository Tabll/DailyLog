#### Composer 安装（Windows）
安装Composer前需要有PHP环境
Composer官网： https://getcomposer.org ，下载安装，可以设置代理以加快访问速度
安装完成后输入 `composer -V` 可以查看版本信息

#### 组件安装
Composer 官方组件资源库：`packagist`，网址： https://packagist.org 
以需要安装日志资源包为例，搜索 `log` 关键词，选择组件（以 `monolog` 组件为例）
在项目的根目录下创建一个名为 `composer.json` 的文件
记录所需要的组件名及版本：
```php
{
    "name": "glow/model-test",
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```
其中 `name` 表示本项目的名称， `glow` 是公司名， `model-test` 是项目名称。
`require` 表示需要的资源包，其中的 `monolog/monolog` 为资源包的名称，`1.0.*`为版本号

#### 指定版本号
指定确切的版本号 `1.0.1`
指定版本范围 `>=1.0,<2.0` `>=1.0,<1.1|>=1.2` `>=1.0`
通配符 `1.0.*`
赋值运算符 `~1.2.3` (相当于 `>=1.2.3,<1.3.0` )
折音号 `^1.2.3` (相当于 `>=1.2.3,<2.0.0` )
稳定性后缀标识 `@stable` `（stable/RC/beta/alpha/dev）`

#### 执行
在创建完 `composer.json` 文件后，通过 `cmd` 命令进入项目的根目录下，执行命令 `composer install`
相关组件会被下载到 `vendor` 文件夹中，对于上面的例子中创建的目录结构会是 `vendor/monolog/monolog`
根目录 `composer.lock` 为锁文件，记录了当前项目依赖的确切版本号
更新命令：`composer update`
自动加载
执行完 `composer install` 后还会在 `vendor` 目录下提供一个自动加载的文件，只需要在项目中通过 `require 'vendor/autoload.php'` 语句引入该文件，即可实现自动加载。
如上例中，可以通过 `$myLog = new \monolog\Logger('name')` 语句直接使用组件中的类库， `autoload` 此时会自动加载相应的类文件。
在 `composer.json` 文件中可以直接添加autoload字段实现命名空间到目录的映射：
```php
{
    "autoload": {
        "": {"App\\": "app/"}
        "": {"Bpp\\": "bpp/"}
    }
}
```
对于 `classmap`，会扫描指定目录中所有的 `.php` 和 `.inc` 文件，并加载到 `vendor/composer/autoload_classmap.php` 文件中，在该文件中会实现一个具体类与文件映射的关联数组，也可以直接精确指定一个文件。通过 `classmap` 可以生成不遵循 `PSR-0` 和 `PSR-4` 规范的自动加载类库。如下例：
```php
{
    "autoload": {
        "classmap": ["database"]
    }
}
```
会搜索 `database` 目录下的所有`.php`和`.inc`文件
对于在每次程序执行时都需要载入的文件，可以通过files规范实现自动加载，通常经常使用的函数库文件就使用这种载入方式，例如下例每次都会加载：
```php
{
	"autoload": {
		"files": [
			"src/Illuminate/Foundation/healpers.php",
			"src/Illuminate/Support/helpers.php"
		]
	}
}
```

#### Composer命令行
Composer工具常用命令：
 - composer list：获取帮助信息
 - composer init：以交互方式填写composer.json文件信息
 - composer install：从当前目录读取composer.json文件，处理依赖关系，并安装到vendor目录下
 - composer update：获取依赖的最新版本，升级composer.lock文件
 - composer require：添加新的依赖包到composer.json文件中并执行更新
 - composer search：在当前项目中搜索依赖包
 - composer show：列举所有可用的资源包
 - composer validate：检测composer.json文件是否有效
 - composer self-update：将composer工具更新到最新版本
 - composer create-project：基于composer创建一个新的项目
 - composer dump-autoload：在添加新的类和目录映射时更新autoloader

#### Composer 相关示例：
`2019/10` 全新安装的的 `Laravel` `6.2` 框架的默认 Composer：
```php
{
    "name": "laravel/laravel",
    "type": "project",
    "description": "The Laravel Framework.",
    "keywords": ["framework", "laravel"],
    "license": "MIT",
    "require": {
        "php": "^7.2",
        "fideloper/proxy": "^4.0",
        "laravel/framework": "^6.2",
        "laravel/tinker": "^1.0"
    },
    "require-dev": {
        "facade/ignition": "^1.4",
        "fzaninotto/faker": "^1.4",
        "mockery/mockery": "^1.0",
        "nunomaduro/collision": "^3.0",
        "phpunit/phpunit": "^8.0"
    },
    "config": {
        "optimize-autoloader": true,
        "preferred-install": "dist",
        "sort-packages": true
    },
    "extra": {
        "laravel": {
            "dont-discover": []
        }
    },
    "autoload": {
        "psr-4": {
            "App\\": "app/"
        },
        "classmap": [
            "database/seeds",
            "database/factories"
        ]
    },
    "autoload-dev": {
        "psr-4": {
            "Tests\\": "tests/"
        }
    },
    "minimum-stability": "dev",
    "prefer-stable": true,
    "scripts": {
        "post-autoload-dump": [
            "Illuminate\\Foundation\\ComposerScripts::postAutoloadDump",
            "@php artisan package:discover --ansi"
        ],
        "post-root-package-install": [
            "@php -r \"file_exists('.env') || copy('.env.example', '.env');\""
        ],
        "post-create-project-cmd": [
            "@php artisan key:generate --ansi"
        ]
    }
}

```
其它 `Laravel` `5.5` 参考：
```php
{
    "require": {
        "php": ">=7.1.12",
        "ext-json": "*",
        "ext-bcmath": "*",
        "laravel/framework": "5.5.*",
        "mews/captcha": "~2.0",
        "qiniu/php-sdk": "dev-master",
        "simplesoftwareio/simple-qrcode": "1.3.*",
        "guzzlehttp/guzzle":"^6.2",
        "doctrine/dbal": "^2.5",
        "dingo/api": "1.0.x@dev",
        "tymon/jwt-auth": "1.0.0-rc.4.1",
        "overtrue/laravel-wechat": "~3.0",
        "rap2hpoutre/laravel-log-viewer": "^1.2.1",
        "maatwebsite/excel": " ~2.1.0",
        "laravel/envoy": "^1.4",
        "laravel/scout": "^4.0",
        "elasticsearch/elasticsearch": "^6.0",
        "predis/predis": "^1.1",
        "socialiteproviders/qq": "^3.0",
        "laravel/horizon": "^1.3",
        "intervention/image": "^2.4",
        "appstract/laravel-opcache": "^2.0",
        "overtrue/laravel-pinyin": "~3.0",
        "spatie/laravel-permission": "^2.38"
    },
    "require-dev": {
        "fzaninotto/faker": "~1.4",
        "mockery/mockery": "0.9.*",
        "phpunit/phpunit": "~6.0",
        "symfony/dom-crawler": "3.1.*",
        "symfony/css-selector": "3.1.*",
        "laravel/tinker": "^1.0",
        "barryvdh/laravel-ide-helper": "^2.6"
    },
    "autoload": {
        "classmap": [
            "database",
            "app/Services"
        ],
        "psr-4": {
            "App\\": "app/",
            "Huangfu\\UmengPush\\": "packages/huangfu/umengpush/src/",
            "Huangfu\\LaravelAlipay\\": "packages/huangfu/laravel-alipay/src/"
        },
        "files": [
            "app/helpers.php",
            "packages/taobao/TopSdk.php"
        ]
    },
    "autoload-dev": {
        "psr-4": {
            "Tests\\": "tests/"
        }
    },
    "scripts": {
        "pre-update-cmd": [
            "php artisan clear-compiled"
        ],
        "post-update-cmd": [
            "Illuminate\\Foundation\\ComposerScripts::postUpdate",
            "php artisan ide-helper:generate",
            "php artisan ide-helper:meta"
        ],
        "post-autoload-dump": [
            "Illuminate\\Foundation\\ComposerScripts::postAutoloadDump",
            "@php artisan package:discover --ansi"
        ],
        "post-root-package-install": [
            "php -r \"copy('.env.example', '.env');\""
        ],
        "post-create-project-cmd": [
            "php artisan key:generate"
        ]
    },
    "config": {
        "preferred-install": "dist",
        "secure-http": false
    }
}
```

> 旧文章：https://www.tabll.cn/2017/use_composer/

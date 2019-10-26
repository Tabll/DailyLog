### Composer 使用相关
#### 指定版本号
指定确切的版本号 `1.0.1`
指定版本范围 `>=1.0,<2.0` `>=1.0,<1.1|>=1.2` `>=1.0`
通配符 `1.0.*`
赋值运算符 `~1.2.3` (相当于 `>=1.2.3,<1.3.0` )
折音号 `^1.2.3` (相当于 `>=1.2.3,<2.0.0` )
稳定性后缀标识 `@stable` （stable/RC/beta/alpha/dev）

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
        "hfpf/alipay": "1.0.5",
        "hfpf/laravel-alipay": "1.1.5",
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
    },
    "repositories": {
        "packagist": {
            "type": "composer",
            "url": "https://mirrors.aliyun.com/composer/"
        }
    }
}
```

---
export_on_save:
  html: true
---

### Laravel 的定时任务
app/Console/Kernel.php
> [定时任务官方文档](https://laravel.com/docs/6.x/scheduling)


### 实现异常邮件发送
编写文件 `app/Traits/ExceptionHandler.php`
```php {.line-numbers}
<?php
/* @noinspection PhpUndefinedClassInspection */
/* @noinspection PhpUndefinedMethodInspection */
/* @noinspection PhpUnusedParameterInspection */
/* @noinspection PhpInconsistentReturnPointsInspection */

namespace App\Traits;

use App;
use App\Jobs\SendEmail;
use Carbon\Carbon;
use Exception;
use Log;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;
use Throwable;

const EMAIL_VIEW = 'email.exception';
const MARK = '定时任务运行异常';

trait ExceptionHandler
{
    protected function execute(InputInterface $input, OutputInterface $output)
    {
        try {
            return $this->laravel->call([$this, 'handle']);
        } catch (Exception $exception) {
            $this->sendEmail((string)$exception, 'EXCEPTION');
        } catch (Throwable $throwable) {
            $this->sendEmail((string)$throwable, 'THROWABLE');
        }
    }

    private function getEnvName()
    {
        $envNames = [
            'local' => '本地环境',
            'integration' => '集成环境',
            'testing' => '测试环境',
            'preview' => '预演环境',
            'production' => '生产环境',
        ];

        if ($envNames[App::environment()]) {
            return $envNames[App::environment()];
        }
        return App::environment();
    }

    private function sendEmail($messages, $errorType)
    {
        $emailAddresses = config('email-send.emails');
        $subject = '服务监控-邻汇-'.$this->getEnvName();
        $data = [
            'title' => '邻汇-'.$this->getEnvName(),
            'time' => Carbon::now(),
            'type' => $errorType,
            'mark' => MARK,
            'signature' => $this->signature,
            'description' => $this->description,
            'exception' => $messages,
        ];
        try {
            dispatch(new SendEmail(EMAIL_VIEW, $data, $emailAddresses, $subject));
        } catch (Exception $exception) {
            Log::info('邮件发送异常', [$exception->getMessage()]);
        }
    }
}
```

关于 `Trait` [官网说明](https://www.php.net/manual/zh/language.oop5.traits.php)
> *Trait 是为类似 PHP 的单继承语言而准备的一种代码复用机制。Trait 为了减少单继承语言的限制，使开发人员能够自由地在不同层次结构内独立的类中复用 method。Trait 和 Class 组合的语义定义了一种减少复杂性的方式，避免传统多继承和 Mixin 类相关典型问题。
> Trait 和 Class 相似，但仅仅旨在用细粒度和一致的方式来组合功能。 无法通过 trait 自身来实例化。它为传统继承增加了水平特性的组合；也就是说，应用的几个 Class 之间不需要继承。*

在此处遇到的问题:
`dispatch` 报错,但是 `dispatch_now` 可以使用
以下是当时获取的错误信息:
```php
Exception: Serialization of 'Closure' is not allowed in 
E:\www\LinhuibaServer\vendor\laravel\framework\src\Illuminate\Queue\Queue.php:128 Stack trace: #0
```
解决方法:将 `Exception $exception` 转换成 `String` 类型
```php
//$this->sendEmail($throwable, 'THROWABLE');
$this->sendEmail((string)$throwable, 'THROWABLE');
```

发件邮箱在配置文件里设置
```php {.line-numbers}
<?php
// 设置用于发送队列任务异常通知的用户邮件
return [
    'emails' => [
        'wts@tabll.cn',
        'tabll@outlook.com',
    ]
];
```

### 添加至用于测试的任务
```php {.line-numbers}
<?php

namespace App\Console\Commands;

use App\Traits\ExceptionHandler;
use Illuminate\Console\Command;

class Test extends Command
{
    use ExceptionHandler;
    /**
     * The name and signature of the console command.
     * @var string
     */
    protected $signature = 'test';

    /**
     * The console command description.
     * @var string
     */
    protected $description = '测试任务运行异常';

    /**
     * Create a new command instance.
     * @return void
     */
    public function __construct()
    {
        parent::__construct();
    }

    /**
     * Execute the console command.
     * @return mixed
     */
    public function handle()
    {
        $this->info('开始ing');
        $g = (int)'你好';
        $g = $g->toDate();
    }
}
```
需要在每一个任务中加上它
```php
use ExceptionHandler;
```
### 效果

<div align=center>
<img src="https://tabll-1252262977.cos.ap-shanghai.myqcloud.com/others/laravel-task-monitor1.png" width="70%">
</div>

这样就完成了具有通知是哪一个运行环境，时间，名称，描述，异常内容的功能

### HTML邮件模板
`resources/views/email/exception.blade.php`
```html {.line-numbers}
<html>
<style type="text/css">
    body {
        margin: 0;
    }
    body, table, td, p, a, li, blockquote {
        -webkit-text-size-adjust: none !important;
        font-family: '微软雅黑';
        font-style: normal;
        font-weight: 400;
    }
    button {
        width: 90%;
    }
    @media screen and (max-width: 600px) {
        body, table, td, p, a, li, blockquote {
            -webkit-text-size-adjust: none !important;
            font-family: '微软雅黑';
        }
        table {
            width: 100%;
        }
        .top {
            height: auto !important;
            max-width: 48% !important;
            width: 48% !important;
        }
    }
    @media screen and (max-width: 480px) {
        body, table, td, p, a, li, blockquote {
            -webkit-text-size-adjust: none !important;
            font-family: "微软雅黑;
        }
        table {
            width: 100% !important;
            border-style: none !important;
        }
        .top {
            height: auto !important;
            max-width: 100% !important;
            width: 100% !important;
        }
        button {
            width: 90% !important;
        }
    }
</style>
<table width="100%" cellspacing="0" cellpadding="0">
    <tbody>
    <tr>
        <td>
            <table width="600" align="center" cellpadding="0" cellspacing="0">
                <tbody>
                <tr>
                    <td>
                        <table bgcolor="#111111" class="top" width="48%" align="left" cellpadding="0" cellspacing="0"
                               style="padding:10px 10px 10px 10px;">
                            <tbody>
                            <tr>
                                <td style="font-size: 12px; color:#FFFFFF;text-align:center;">{{$title}}</td>
                            </tr>
                            </tbody>
                        </table>
                        <table bgcolor="#E53935" class="top" width="48%" align="left" cellpadding="0" cellspacing="0"
                               style="padding:10px 10px 10px 10px; text-align:right">
                            <tbody>
                            <tr>
                                <td style="font-size: 12px; color:#FFFFFF; text-align:center;">{{$time}}</td>
                            </tr>
                            </tbody>
                        </table>
                    </td>
                </tr>
                <tr>
                    <td style="font-size: 0; line-height: 0;" height="20">
                        <table width="96%" align="left" cellpadding="0" cellspacing="0">
                            <tbody>
                            <tr>
                                <td style="font-size: 0; line-height: 0;" height="20">&nbsp;</td>
                            </tr>
                            </tbody>
                        </table>
                    </td>
                </tr>
                <tr>
                    <td>
                        <table width="96%" align="left" cellpadding="0" cellspacing="0">
                            <tbody>
                            <tr>
                                <td align="center"
                                    style="font-size: 32px; font-weight: 300; line-height: 2.5em; color: #929292;">{{$type}}</td>
                            </tr>
                            <tr>
                                <td align="center" style="font-size: 16px;color: #929292;">{{$mark}}</td>
                            </tr>
                            <tr>
                                <td style="font-size: 0; line-height: 0;" height="20">
                                    <table width="96%" align="left" cellpadding="0" cellspacing="0">
                                        <tbody>
                                        <tr>
                                            <td style="font-size: 0; line-height: 0;" height="20">&nbsp;</td>
                                        </tr>
                                        </tbody>
                                    </table>
                                </td>
                            </tr>
                            <tr>
                                <td align="left"
                                    style="font-size: 14px; font-style: normal; font-weight: 100; color: #000000; line-height: 1.8;  padding:10px 20px 0px 20px;">
                                    任务名：<b>{{$signature}}</b><br>
                                    任务描述：{{$description}}<br>
                                    {{$exception}}
                                </td>
                            </tr>
                            </tbody>
                        </table>
                    </td>
                </tr>
                </tbody>
            </table>
        </td>
    </tr>
    </tbody>
</table>
</html>
```

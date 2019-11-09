---
export_on_save:
  html: true
---

### 另一种实现异常邮件发送的方式
之前是使用 `Traits` 的方式实现的，原先文章地址：[Laravel 定时任务监控](https://www.tabll.cn/2019/laravel-task-monitor/)。这种方式可以实现但是稍微麻烦了一点点，需要在每一个任务里加一个 `use`，现在找到了一个更好的方法能够直接解决这个问题
编写文件 `app/Exceptions/Handler.php`
重写 `renderForConsole` 方法
```php {.line-numbers}
    /**
     * 捕获 Console 的错误并发送邮件通知
     * @param OutputInterface $output
     * @param Exception $e
     */
    public function renderForConsole($output, Exception $e)
    {
        $emailAddresses = config('email-send.emails');
        $subject = '服务监控-'.$this->getEnvName();
        $data = [
            'title' => '系统-'.$this->getEnvName(),
            'time' => Carbon::now(),
            'type' => 'Exception',
            'mark' => '定时任务运行异常',
            'signature' => $e->getFile(),
            'line' => $e->getLine(),
            'description' => $e->getMessage(),
            'exception' => $e->getTraceAsString(),
        ];

        // 限制同一文件一天内只发送一次邮件 && 测试和预演环境
        if ((!Cache::has('exception:' . $e->getFile())) && (in_array(App::environment(), ['production', 'testing', 'staging']))) {
            dispatch(new SendEmail('email.exception', $data, $emailAddresses, $subject));

            $expiresAt = Carbon::now()->addDay(1); // 键值过期时间
            Cache::put('exception:' . $e->getFile(), 'exception', $expiresAt);
        }

        parent::renderForConsole($output, $e);
    }

    /**
     * 获取当前的环境名称
     * @return mixed|string
     */
    private function getEnvName()
    {
        $envNames = [
            'local' => '本地环境',
            'integration' => '集成环境',
            'testing' => '测试环境',
            'staging' => '预演环境',
            'production' => '生产环境',
        ];

        return $envNames[App::environment()] ?? App::environment();
    }
```

这里还使用了 `Redis` 缓存来实现同一个错误不会多次发送错误通知的限制。因为在实际使用的情况中会有一个错误多次发送导致邮箱塞满的情况
```php
if ((!Cache::has('exception:' . $e->getFile())) && (in_array(App::environment(), ['production', 'testing', 'staging']))) {
    dispatch(new SendEmail('email.exception', $data, $emailAddresses, $subject));

    $expiresAt = Carbon::now()->addDay(1); // 键值过期时间
    Cache::put('exception:' . $e->getFile(), 'exception', $expiresAt);
}
```
这里的 `键值过期时间` 是限制同一任务多久不会发送的配置

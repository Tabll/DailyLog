<style>
table {
    width: 100%; /*表格宽度*/
    max-width: 65em; /*表格最大宽度，避免表格过宽*/
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
## PHP Aritisan 命令集合
```sh
Usage:
  list [options] [--] [<namespace>]

Arguments:
  namespace            The namespace name

Options:
      --raw            To output raw command list
      --format=FORMAT  The output format (txt, xml, json, or md) [default: "txt"]

Help:
  The list command lists all commands:

    php artisan list

  You can also display the commands for a specific namespace:

    php artisan list test

  You can also output the information in other formats by using the --format option:

    php artisan list --format=xml

  It's also possible to get raw list of commands (useful for embedding command runner):

    php artisan list --raw
```

### 命令
|  命令  |  效果  |  说明  |
| :--------- | :--------- | :-------- |
| clear-compiled | 清除编译文件 | Remove the compiled class file |
| down | 开启维护模式 | Put the application into maintenance mode |
| dump-server `5.7+` | 启动转储服务器收集转储信息 | Start the dump server to collect dump information |
| env | 显示当前运行环境 | Display the current framework environment |
| help | 显示帮助信息 | Displays help for a command |
| inspire `5.0+` | 随机输出一条名言 | Display an inspiring quote |
| list | 打印命令列表 | Lists commands |
| migrate | 执行数据库迁移 | Run the database migrations |
| optimize `5.5-` | 优化框架性能（不建议使用） | Optimize the framework for better performance (deprecated) |
| preset `5.5+` | 为应用程序交换前端脚手架 | Swap the front-end scaffolding for the application |
| serve | 启动应用程序 | Serve the application on the PHP development server |
| tinker | 打开 Psy Shell | Interact with your application |
| up | 结束维护模式 | Bring the application out of maintenance mode |

### DingoAPI
|  命令  |  效果  |  说明  |
| :--------- | :--------- | :-------- |
| api:cache | 创建路由缓存文件 | Create a route cache file for faster route registration |
| api:docs | 生成 API 文档 | Generate API documentation from annotated controllers |
| api:routes | 列出全部 API 路径 | List all registered API routes |

### APP
|  命令  |  效果  |  说明  |
| :--------- | :--------- | :-------- |
| app:name | 设置应用程序命名空间 | Set the application namespace |

### Auth
|  命令  |  效果  |  说明  |
| :--------- | :--------- | :-------- |
| auth:clear-resets | 刷新过期的密码重置 token | Flush expired password reset tokens |

### Cache
|  命令  |  效果  |  说明  |
| :--------- | :--------- | :-------- |
| cache:clear | 清除缓存 | Flush the application cache |
| cache:forget | 移除指定缓存 | Remove an item from the cache |
| cache:table | 为缓存数据表创建迁移 | Create a migration for the cache database table |

### Config
|  命令  |  效果  |  说明  |
| :--------- | :--------- | :-------- |
| config:cache | 创建配置文件缓存 | Create a cache file for faster configuration loading |
| config:clear | 清除配置文件缓存 | Remove the configuration cache file |

### DB
|  命令  |  效果  |  说明  |
| :--------- | :--------- | :-------- |
| db:seed | 执行数据库填充 | Seed the database with records |

### Event
|  命令  |  效果  |  说明  |
| :--------- | :--------- | :-------- |
| event:generate | 生成缺少的事件和监听 | Generate the missing events and listeners based on registration |

### Horizon `Linux`
|  命令  |  效果  |  说明  |
| :--------- | :--------- | :-------- |
| horizon:assets | 开启维护模式 | Re-publish the Horizon assets |
| horizon:continue | 开启维护模式 | Instruct the master supervisor to continue processing jobs |
| horizon:list | 开启维护模式 | List all of the deployed machines |
| horizon:pause | 开启维护模式 | Pause the master supervisor |
| horizon:purge | 开启维护模式 | Terminate any rogue Horizon processes |
| horizon:snapshot | 开启维护模式 | Store a snapshot of the queue metrics |
| horizon:supervisors | 开启维护模式 | List all of the supervisors |
| horizon:terminate | 开启维护模式 | Terminate the master supervisor so it can be restarted |

### IDE-Helper
|  命令  |  效果  |  说明  |
| :--------- | :--------- | :-------- |
| ide-helper:eloquent | 开启维护模式 | Add \Eloquent helper to \Eloquent\Model |
| ide-helper:generate | 开启维护模式 | Generate a new IDE Helper file |
| ide-helper:meta | 开启维护模式 | Generate metadata for PhpStorm |
| ide-helper:models | 开启维护模式 | Generate autocompletion for models |

### Key
|  命令  |  效果  |  说明  |
| :--------- | :--------- | :-------- |
| key:generate | 生成一个 APP_KEY | Set the application key |

|  命令  |  效果  |  说明  |
| :--------- | :--------- | :-------- |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |
| down | 开启维护模式 | Put the application into maintenance mode |

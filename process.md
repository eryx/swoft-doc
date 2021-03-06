# 进程

Swoft 提供自定义进程，每个进程 Swoft 启动的时候一起启动，Manager 进程负责管理该进程，默认启动的进程是守护进程，并且意外退出后，Manager 进程会重新启动一个。

## 定义进程

定义进程简单，只需继承`\Swoft\Process\AbstractProcess`类，实现`run`方法，run方法里面就是实际逻辑。自定义进程和任务一样，里面可以使用所有功能比如注解、日志、RPC、HTTP、Mysql、Redis 底层无缝自动切换成同步IO操作。

```php
<?php

namespace App\Process;

use Swoft\App;
use Swoft\Process\AbstractProcess;
use Swoole\Process;

/**
 * 自定义进程demo
 */
class MyProcess extends AbstractProcess
{
    /**
     * 实际进程运行逻辑
     *
     * @param Process $process 进程对象
     */
    public function run(Process $process)
    {
        $pname = $this->server->getPname();
        $processName = "$pname myProcess process";
        $process->name($processName);

        $i = 1;
        while (true) {
            echo "this my process \n";
            App::trace("my process count=" . $i);
            sleep(10);
            $i++;
        }
    }
}
```

 

## 配置进程

config/server.php 里面配置进程，server start 的时候，就会启动改进程，必须为每一个进程定义一个名称，用于进程之间通讯。

```php
'process' => [
    'reload'    => \Swoft\Process\ReloadProcess::class,
    'cronTimer' => \Swoft\Process\CronTimerProcess::class,
    'cronExec'  => \Swoft\Process\CronExecProcess::class,
],
```




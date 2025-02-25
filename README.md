Throttle
======

Composer安装
-------

```
composer require davedevelopment/stiphle
```

是什么？
-----------

提供一种简单的限制/速率限制请求的一个类库。

能做什么？
-----------------

``` php
<?php

$throttle = new Stiphle\Throttle\LeakyBucket;
$identifier = 'dave';
while(true) {
    // the throttle method returns the amount of milliseconds it slept for
    echo $throttle->throttle($identifier, 5, 1000);
}
# 0 0 0 0 0 200 200....

```

Use combinations of values to provide bursting etc, though use carefully as it
screws with your mind

``` php
<?php

$throttle = new Stiphle\Throttle\LeakyBucket;
$identifier = 'dave';
for(;;) {
    /**
     * Allow upto 5 per second, but limit to 20 a minute - I think
     **/
    echo "a:" . $throttle->throttle($identifier, 5, 1000);
    echo " b:" . $throttle->throttle($identifier, 20, 60000);
    echo "\n";
}
#a:0 b:0
#a:0 b:0
#a:0 b:0
#a:0 b:0
#a:0 b:0
#a:199 b:0
#a:200 b:0
#a:199 b:0
#a:200 b:0
#a:200 b:0
#a:199 b:0
#a:200 b:0
#a:199 b:0
#a:200 b:0
#a:200 b:0
#a:199 b:0
#a:200 b:0
#a:200 b:0
#a:199 b:0
#a:200 b:0
#a:199 b:0
#a:200 b:2600
#a:0 b:3000
#a:0 b:2999


```

节流策略
-------------------

目前有两种节流方式，, LeakyBucket和TimeWindow

``` php

/**
 * 每24小时限制1000次
 */
$throttle = new Stiphle\Throttle\LeakyBucket;
$throttle->throttle('api.request', 1000, 86400000);

/**
 * 自然天，每天限制1000次
 */
$throttle = new Stiphle\Throttle\TimeWindow;
$throttle->throttle('api.request', 1000, 86400000);

```

存储引擎
-------

提供5个储存引擎

* in process，进程内存储（默认）
* APC
* Memcached
* Doctrine Cache
* Redis

``` php
$throttle = new Stiphle\Throttle\LeakyBucket();
$storage = new \Stiphle\Storage\Memcached(new \Memcached());
$throttle->setStorage($storage);
```

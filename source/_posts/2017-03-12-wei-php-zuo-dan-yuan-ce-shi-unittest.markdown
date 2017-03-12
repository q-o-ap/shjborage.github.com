---
layout: post
title: "为 PHP 做单元测试（UnitTest）"
date: 2017-03-12 11:16
comments: true
categories: Web
tags: PHP BackEnd
---

随便用 Google 百度一下就可以找到这个： [PHPUnit – The PHP Testing Framework](https://phpunit.de/)

貌似也还有别的类似框架或者自己写 PHP 脚本来完成，但这个就不在我们今天的讨论范围啦。

## Getting Started with PHPUnit
### Download
We distribute a [PHP Archive (PHAR)](http://php.net/phar) that contains everything you need in order to use PHPUnit. Simply download it from here, make it executable, and put it into your `$PATH`, for instance:

```
wget https://phar.phpunit.de/phpunit.phar
chmod +x phpunit.phar
sudo mv phpunit.phar /usr/local/bin/phpunit
phpunit --version
```

### Code

```
<?php
class Money {
    private $amount;

    public function __construct($amount) {
        $this->amount = $amount;
    }

    public function getAmount() {
        return $this->amount;
    }

    public function negate() {
        return new Money(-1 * $this->amount);
    }

    // ...
}

```

### TestCode

```
<?php
use PHPUnit\Framework\TestCase;

class MoneyTest extends TestCase {
    public function testCanBeNegated() {
        // Arrange
        $a = new Money(1);

        // Act
        $b = $a->negate();

        // Assert
        $this->assertEquals(-1, $b->getAmount());
    }

    // ...
}


```

### Run

```
➜  PHPUnitExample git:(master) ✗ phpunit --bootstrap src/Monkey.php tests/MonkeyTest.php
PHPUnit 5.7.5 by Sebastian Bergmann and contributors.

.                                                                   1 / 1 (100%)

Time: 95 ms, Memory: 8.00MB

OK (1 test, 1 assertion)
```

比较好理解，加载一个类，然后执行相应的单元测试。
[PHPUnit 完整例子](https://github.com/shjborage/PHPUnitExample)

还要看相应的文档，了解一下高级用法。

## 高级用法(TODO)

## Refs
[Getting Started with PHPUnit](https://phpunit.de/getting-started.html)





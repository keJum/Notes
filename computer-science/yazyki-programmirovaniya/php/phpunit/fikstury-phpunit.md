---
description: >-
  Настройки тестового окружения в известное состояние, а затем возврат его в
  исходное состояние, когда тест будет завершён.
---

# 🛹 🐘 🧪 Фикстуры PHPUnit

## Локальные фикстуры

**Перед выполнением тестового метода** будет вызван шаблонный метод `setUp(): void`. `setUp(): void` — это место, где вы создаёте тестируемые объекты. После того, как тестовый метод выполнится, вне зависимости успешно или нет, вызывается другой шаблонный метод с названием `tearDown(): void`. `tearDown(): void` - это место, где вы очищаете протестированные объекты.

{% tabs %}
{% tab title="PHP" %}
```php
<?php
use PHPUnit\Framework\TestCase;

class StackTest extends TestCase
{
    protected $stack;

    protected function setUp(): void
    {
        $this->stack = [];
    }

    public function testEmpty(): void
    {
        $this->assertTrue(empty($this->stack));
    }

    public function testPush(): void
    {
        array_push($this->stack, 'foo');
        $this->assertSame('foo', $this->stack[count($this->stack) - 1]);
        $this->assertFalse(empty($this->stack));
    }

    public function testPop(): void
    {
        array_push($this->stack, 'foo');
        $this->assertSame('foo', array_pop($this->stack));
        $this->assertTrue(empty($this->stack));
    }
}
```
{% endtab %}
{% endtabs %}

 Кроме того, вызываются шаблонные методы `setUpBeforeClass(): void` и `tearDownAfterClass(): void` **перед тем, как первый тест в тестовом классе будет выполнен**, и после запуска последнего теста тестового класса, соответственно.

{% tabs %}
{% tab title="PHP" %}
```php
<?php
use PHPUnit\Framework\TestCase;

class TemplateMethodsTest extends TestCase
{
    public static function setUpBeforeClass(): void
    {
        fwrite(STDOUT, __METHOD__ . "\n");
    }

    protected function setUp(): void
    {
        fwrite(STDOUT, __METHOD__ . "\n");
    }

    protected function assertPreConditions(): void
    {
        fwrite(STDOUT, __METHOD__ . "\n");
    }

    public function testOne(): void
    {
        fwrite(STDOUT, __METHOD__ . "\n");
        $this->assertTrue(true);
    }

    public function testTwo(): void
    {
        fwrite(STDOUT, __METHOD__ . "\n");
        $this->assertTrue(false);
    }

    protected function assertPostConditions(): void
    {
        fwrite(STDOUT, __METHOD__ . "\n");
    }

    protected function tearDown(): void
    {
        fwrite(STDOUT, __METHOD__ . "\n");
    }

    public static function tearDownAfterClass(): void
    {
        fwrite(STDOUT, __METHOD__ . "\n");
    }

    protected function onNotSuccessfulTest(Exception $e): void
    {
        fwrite(STDOUT, __METHOD__ . "\n");
        throw $e;
    }
}
```
{% endtab %}

{% tab title="Out" %}
```
$ phpunit TemplateMethodsTest
PHPUnit latest.0 by Sebastian Bergmann and contributors.

TemplateMethodsTest::setUpBeforeClass
TemplateMethodsTest::setUp
TemplateMethodsTest::assertPreConditions
TemplateMethodsTest::testOne
TemplateMethodsTest::assertPostConditions
TemplateMethodsTest::tearDown
.TemplateMethodsTest::setUp
TemplateMethodsTest::assertPreConditions
TemplateMethodsTest::testTwo
TemplateMethodsTest::tearDown
TemplateMethodsTest::onNotSuccessfulTest
FTemplateMethodsTest::tearDownAfterClass

Time: 0 seconds, Memory: 5.25Mb

There was 1 failure:

1) TemplateMethodsTest::testTwo
Failed asserting that <boolean:false> is true.
/home/sb/TemplateMethodsTest.php:30

FAILURES!
Tests: 2, Assertions: 2, Failures: 1.
```
{% endtab %}
{% endtabs %}

## Совместные фикстуры

Использует шаблонные методы `setUpBeforeClass(): void` и `tearDownAfterClass(): void` для подключения к базе данных до выполнения первого теста в тестовом классе и закрытие соединения с базой данных после запуска последнего теста, соответственно.

```php
<?php
use PHPUnit\Framework\TestCase;

class DatabaseTest extends TestCase
{
    protected static $dbh;

    public static function setUpBeforeClass(): void
    {
        self::$dbh = new PDO('sqlite::memory:');
    }

    public static function tearDownAfterClass(): void
    {
        self::$dbh = null;
    }
}
```

## Список используемой литературы:

{% embed url="https://phpunit.readthedocs.io/ru/latest/fixtures.html" %}


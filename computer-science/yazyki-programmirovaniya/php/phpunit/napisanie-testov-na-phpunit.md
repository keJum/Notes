# 👨🏻‍💻 🐘 🧪 PHPUnit: Написание тестов

## Написание тестов на PHPUnit

1. Тесты для класса `Class` содержатся в классе `ClassTest`.
2. `ClassTest` наследуется \(чаще всего\) от `PHPUnit\Framework\TestCase`.
3. Тесты — общедоступные методы с именами `test*`.
4. Внутри тестовых методов для проверки того, соответствует ли фактическое значение ожидаемому используются методы-утверждения, такие как `assertSame()` \(см. [Утверждения](https://phpunit.readthedocs.io/ru/latest/assertions.html#appendixes-assertions)\).

```php
<?php
use PHPUnit\Framework\TestCase;

class StackTest extends TestCase
{
    public function testPushAndPop()
    {
        $stack = [];
        $this->assertSame(0, count($stack));

        array_push($stack, 'foo');
        $this->assertSame('foo', $stack[count($stack)-1]);
        $this->assertSame(1, count($stack));

        $this->assertSame('foo', array_pop($stack));
        $this->assertSame(0, count($stack));
    }
}
```

## Зависимости тестов

Аннотация `@depends`  используется для представления зависимостей между тестовыми методами.

Вместо ссылки возможна, либо глубокая копия через `@depends clone` или поверхностная копия на основе ключевого слова PHP `clone` через `@depends shallowClone`.

Тест, содержащий более одной аннотации `@depends`, получит фикстуру от первого поставщика в качестве первого аргумента, фикстуру от второго поставщика вторым аргументом.

```php
<?php
use PHPUnit\Framework\TestCase;

class MultipleDependenciesTest extends TestCase
{
    public function testProducerFirst()
    {
        $this->assertTrue(true);
        return 'first';
    }

    public function testProducerSecond()
    {
        $this->assertTrue(true);
        return 'second';
    }

    /**
     * @depends testProducerFirst
     * @depends testProducerSecond
     */
    public function testConsumer($a, $b)
    {
        $this->assertSame('first', $a);
        $this->assertSame('second', $b);
    }
}
```

## Провайдеры данных

Тестовый метод может принимать произвольное количество аргументов. Эти аргументы могут быть предоставлены одним или несколькими методами провайдеров данных `additionProvider()`. Метод, который будет использован в качестве провайдера данных, обозначается с помощью аннотации `@dataProvider`.

Метод провайдера данных должен быть объявлен как `public` и возвращать либо массив массивов, либо объект, реализующий интерфейс `Iterator` и возвращать массив при каждой итерации. Для каждого массива, являющегося частью коллекции, будет вызываться тестовый метод с элементами массива в качестве его аргументов.

```php
<?php
use PHPUnit\Framework\TestCase;

class DataTest extends TestCase
{
    /**
     * @dataProvider additionProvider
     */
    public function testAdd($a, $b, $expected)
    {
        $this->assertSame($expected, $a + $b);
    }

    public function additionProvider()
    {
        return [
            [0, 0, 0],
            [0, 1, 1],
            [1, 0, 1],
            [1, 1, 3]
        ];
    }
}
```

## Тестирование исключений

Использование метода `expectException()` для проверки того, было ли выброшено исключение в тестируемом коде. В дополненя существуют методы :

* `expectExceptionCode()`,
* `expectExceptionMessage()` 
* `expectExceptionMessageRegExp()`

Можно использовать аннотации `@expectedException.`

```php
<?php
use PHPUnit\Framework\TestCase;

class ExceptionTest extends TestCase
{
    public function testException()
    {
        $this->expectException(InvalidArgumentException::class);
    }
}
```

## Тестирование ошибок PHP

По умолчанию PHPUnit преобразует ошибки, предупреждения и уведомления, вызываемые PHP во время выполнения теста, в исключения. Используя эти исключения, вы можете, например, ожидать, что тест вызовет ошибку PHP используя @expectedException.

```php
<?php
use PHPUnit\Framework\TestCase;

class ExpectedErrorTest extends TestCase
{
    /**
     * @expectedException PHPUnit\Framework\Error\Error
     */
    public function testFailingInclude()
    {
        include 'not_existing_file.php';
    }
}
```

## Тестирования вывода

Иногда вам нужно проверить, что выполнение метода, например, генерирует ожидаемый вывод \(к примеру, через `echo` или `print`\) использовать метод `expectOutputString()` для установки ожидаемого вывода.

```php
<?php
use PHPUnit\Framework\TestCase;

class OutputTest extends TestCase
{
    public function testExpectFooActualFoo()
    {
        $this->expectOutputString('foo');
        print 'foo';
    }

    public function testExpectBarActualBaz()
    {
        $this->expectOutputString('bar');
        print 'baz';
    }
}
```



| Методы для тестирования вывода | Описание |
| :--- | :--- |
| `void expectOutputRegex(string $regularExpression)` | Проверить, что вывод соответствует регулярному выражению `$regularExpression`. |
| `void expectOutputString(string $expectedString)` | Проверить, что вывод соответствует строке `$expectedString`. |
| `bool setOutputCallback(callable $callback)` | Устанавливает функцию обратного вызова, используемую, например, для нормализации фактического вывода. |
| `string getActualOutput()` | Получить фактический вывод. |

## Используемая литература:

{% embed url="https://phpunit.readthedocs.io/ru/latest/writing-tests-for-phpunit.html" %}




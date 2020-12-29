# 🏛️ 🚗 🚸 🏭 Паттерн фабрика

#### Создающий метод

Простой метод-обёртка над вызовом конструктора продукта. Выделив создающий метод, вы изолируете любые изменения в конструировании продуктов от основного кода.

```php
class Number {
    private $value;

    public function __construct($value) {
        $this->value = $value;
    }

    public function next() {
        return new Number ($this->value + 1);
    }
}
```

#### Статический фабричный метод

Вариация создающего метода, объявленная как `static`. Если этот метод создаёт объекты своего же класса, то, по сути, он выступает в роли альтернативного конструктора.

**Применения**

* требуется создать разные по функциональности конструкторы, у которых бы совпадали сигнатуры;
* хочется повторно использовать готовые объекты, вместо создания новых.

```php
class User {
    private $id, $name, $email, $phone;

    public function __construct($id, $name, $email, $phone) {
        $this->id = $id;
        $this->name = $name;
        $this->email = $email;
        $this->phone = $phone;
    }

    // Статчиный фабричный метод
    // Предостовляющий способ загрузки пользователя в БД
    public static function load($id) {
        list($id, $name, $email, $phone) = DB::load_data('users', 'id', 'name', 'email', 'phone');
        $user = new User($id, $name, $email, $phone);
        return $user;
    }
}
```

#### Простая фабрика

Простая фабрика просто генерирует экземпляр для клиента без предоставления какой-либо логики экземпляра.

**Применения**

Когда создание объекта подразумевает какую-то логику, а не просто несколько присваиваний, то имеет смысл делегировать задачу выделенной фабрике, а не повторять повсюду один и тот же код.

```php
/*
 * Интерфейс двери и его реализация. 
 */

interface Door
{
    public function getWidth(): float;
    public function getHeight(): float;
}

class WoodenDoor implements Door
{
    protected $width;
    protected $height;

    public function __construct(float $width, float $height)
    {
        $this->width = $width;
        $this->height = $height;
    }

    public function getWidth(): float
    {
        return $this->width;
    }

    public function getHeight(): float
    {
        return $this->height;
    }
}

/*
 * Фабрика дверей, которая создаёт и возвращает нам двери.
 */

class DoorFactory
{
    public static function makeDoor($width, $height): Door
    {
        return new WoodenDoor($width, $height);
    }
}

/*
 * Использование
 */

$door = DoorFactory:makeDoor(100, 200);
echo 'Width: ' . $door->getWidth();
echo 'Height: ' . $door->getHeight();
```

#### Фабричный метод

Устройство классов, при котором подклассы могут переопределять тип создаваемого в суперклассе продукта.

**Применение**

Этот шаблон полезен для каких-то общих обработок в классе, но требуемые подклассы динамически определяются в ходе выполнения \(runtime\). То есть когда клиент не знает, какой именно подкласс может ему понадобиться.

```php
/*
 * Интерфейс сотрудника, проводящего собеседование, и некоторые реализации для него.
 */

interface Interviewer
{
    public function askQuestions();
}

class Developer implements Interviewer
{
    public function askQuestions()
    {
        echo 'Asking about design patterns!';
    }
}

class CommunityExecutive implements Interviewer
{
    public function askQuestions()
    {
        echo 'Asking about community building';
    }
}

/*
 *  Cоздадим кадровичку HiringManager.
 */

abstract class HiringManager
{

    // Фабричный метод
    abstract public function makeInterviewer(): Interviewer;

    public function takeInterview()
    {
        $interviewer = $this->makeInterviewer();
        $interviewer->askQuestions();
    }
}

/*
 * Дочерний класс может расширять его и предоставлять нужного собеседующего
 */

class DevelopmentManager extends HiringManager
{
    public function makeInterviewer(): Interviewer
    {
        return new Developer();
    }
}

class MarketingManager extends HiringManager
{
    public function makeInterviewer(): Interviewer
    {
        return new CommunityExecutive();
    }
}

/*
 * Использование 
 */

$devManager = new DevelopmentManager();
$devManager->takeInterview(); // Output: Спрашивает о шаблонах проектирования.

$marketingManager = new MarketingManager();
$marketingManager->takeInterview(); // Output: Спрашивает о создании сообщества.
```

#### Абстрактная фабрика

Фабрика, группирующая индивидуальные, но взаимосвязанные/взаимозависимые фабрики без указания для них конкретных классов.

**Применения**

Когда у вас есть взаимосвязи с не самой простой логикой создания \(creation logic\).

```php
/*
 * Создадим интерфейс Door и несколько реализаций для него.
 */

interface Door
{
    public function getDescription();
}

class WoodenDoor implements Door
{
    public function getDescription()
    {
        echo 'I am a wooden door';
    }
}

class IronDoor implements Door
{
    public function getDescription()
    {
        echo 'I am an iron door';
    }
}

/*
 * Теперь нам нужны специалисты по установке каждого вида дверей.
 */

interface DoorFittingExpert
{
    public function getDescription();
}

class Welder implements DoorFittingExpert
{
    public function getDescription()
    {
        echo 'I can only fit iron doors';
    }
}

class Carpenter implements DoorFittingExpert
{
    public function getDescription()
    {
        echo 'I can only fit wooden doors';
    }
}

/*
 * Мы получили абстрактную фабрику, которая позволяет создавать семейства объектов или взаимосвязанные объекты. 
 * То есть фабрика деревянных дверей создаст деревянную дверь и человека для её монтажа, 
 * фабрика стальных дверей — стальную дверь и соответствующего специалиста и т. д.
 */

interface DoorFactory
{
    public function makeDoor(): Door;
    public function makeFittingExpert(): DoorFittingExpert;
}

// Фабрика деревянных дверей возвращает плотника и деревянную дверь
class WoodenDoorFactory implements DoorFactory
{
    public function makeDoor(): Door
    {
        return new WoodenDoor();
    }

    public function makeFittingExpert(): DoorFittingExpert
    {
        return new Carpenter();
    }
}

// Фабрика стальных дверей возвращает стальную дверь и сварщика
class IronDoorFactory implements DoorFactory
{
    public function makeDoor(): Door
    {
        return new IronDoor();
    }

    public function makeFittingExpert(): DoorFittingExpert
    {
        return new Welder();
    }
}

/*
 *  Использование:
 */

$woodenFactory = new WoodenDoorFactory();

$door = $woodenFactory->makeDoor();
$expert = $woodenFactory->makeFittingExpert();

$door->getDescription();  // Output: Я деревянная дверь
$expert->getDescription(); // Output: Я могу устанавливать только деревянные двери

// Same for Iron Factory
$ironFactory = new IronDoorFactory();

$door = $ironFactory->makeDoor();
$expert = $ironFactory->makeFittingExpert();

$door->getDescription();  // Output: Я стальная дверь
$expert->getDescription(); // Output: Я могу устанавливать только стальные двери
```


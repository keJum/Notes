---
description: >-
  Позволяет создавать разные свойства объекта, избегая загрязнения конструктора 
  (constructor pollution).
---

# 🏛️ 🚗 🚸 🛠️                 Паттерн строитель

Когда у объекта может быть несколько свойств и когда нужно избежать Telescoping constructor. 

Ключевое отличие от шаблона «Простая фабрика»: он используется в одно этапном создании, а «Строитель» — в многоэтапном.

```php
/*
 * Сначала создадим бургер:
 */

class Burger
{
    protected $size;

    protected $cheese = false;
    protected $pepperoni = false;
    protected $lettuce = false;
    protected $tomato = false;

    public function __construct(BurgerBuilder $builder)
    {
        $this->size = $builder->size;
        $this->cheese = $builder->cheese;
        $this->pepperoni = $builder->pepperoni;
        $this->lettuce = $builder->lettuce;
        $this->tomato = $builder->tomato;
    }
}

/*
 * А затем добавим «строителя»:
 */

class BurgerBuilder
{
    public $size;

    public $cheese = false;
    public $pepperoni = false;
    public $lettuce = false;
    public $tomato = false;

    public function __construct(int $size)
    {
        $this->size = $size;
    }

    public function addPepperoni()
    {
        $this->pepperoni = true;
        return $this;
    }

    public function addLettuce()
    {
        $this->lettuce = true;
        return $this;
    }

    public function addCheese()
    {
        $this->cheese = true;
        return $this;
    }

    public function addTomato()
    {
        $this->tomato = true;
        return $this;
    }

    public function build(): Burger
    {
        return new Burger($this);
    }
}

/*
 * Использование
 */

$burger = (new BurgerBuilder(14))
                    ->addPepperoni()
                    ->addLettuce()
                    ->addTomato()
                    ->build();
```

### Используемая литература

* [Шаблоны проектирования с человеческим лицом](https://habr.com/ru/company/mailru/blog/325492/)


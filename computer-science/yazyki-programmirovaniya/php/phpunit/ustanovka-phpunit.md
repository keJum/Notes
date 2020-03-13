# 🛹 🐘 🧪 Установка PHPUnit

### Требования

Основные: 

* PHP 7.2
* php-dom 
* php-json \( по умолчанию \) 
* php-pcre \( по умолчанию\)
* php-reflection \( стандарт \) 
* php-spl  \( стандарт \)

Дополнительно: 

* Xdebug &gt; 2.7.0
* php- tokenizer
* php-xmlwriter

### PHP Archive \(PHAR\)

```bash
$ wget https://phar.phpunit.de/phpunit-latest.phar
$ chmod +x phpunit-latest.phar
$ ./phpunit-latest.phar --version
PHPUnit x.y.z by Sebastian Bergmann and contributors.
```

### Composer

```text
composer require --dev phpunit/phpunit ^latest
```

{% hint style="warning" %}
Обратите внимание, что не рекомендуется устанавливать PHPUnit глобально, например\`\`/usr/bin/phpunit\`\` или `/usr/local/bin/phpunit`.
{% endhint %}




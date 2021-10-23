# PSR-16: Общий интерфейс для кэширующих библиотек.

### Этот документ описывает простой, но расширяемый интерфейс для элемента кэша и драйвера кэша.

![Дарова. Чекаешь альты?](https://pbs.twimg.com/profile_images/1444689501880737797/xtLL2gE7_400x400.jpg)


Ключевые слова **"MUST"**, **"MUST NOT"**, **"REQUIRED"**, **"SHALL"**, **"SHALL NOT"**, **"SHOULD"**, **"SHOULD NOT"**, **"RECOMMENDED"**,  **"MAY"**, и **"OPTIONAL"** в этом документе являются следует интерпретировать, как описано в [RFC 2119](https://www.rfc-archive.org/getrfc.php?rfc=2119#gsc.tab=0).

Окончательные реализации могут украсить объекты большей функциональностью, чем предлагаемая, но сначала они должны реализовать указанные интерфейсы/функции.

## 1.1 Введение
Кэширование - это распространенный способ улучшить производительность любого проекта, что делает библиотеки кеширования одной из наиболее распространенных функций многих фреймворков и библиотек. Функциональная совместимость на этом уровне означает, что библиотеки могут отказаться от своих собственных реализаций кэширования и легко полагаться на ту, которую им предоставил фреймворк, или другая выделенная библиотека кеширования.

PSR-6 уже решает эту проблему, но довольно формальным и подробным способом для самых простых случаев использования. Этот более простой подход направлен на создание стандартизированного оптимизированного интерфейса для общих случаев. Он не зависит от PSR-6, но был разработан, чтобы максимально упростить совместимость с PSR-6.

## 1.2 Определения
Определения для библиотеки вызовов, реализации библиотеки, TTL, срока действия и ключа скопированы из PSR-6, поскольку те же предположения верны.

* Вызов библиотеки - библиотека или код, который действительно нуждается в службах кеширования. Эта библиотека будет использовать службы кэширования, которые реализуют интерфейсы этого стандарта, но в противном случае не будет знать о реализации этих служб кэширования.

* Реализация библиотеки - эта библиотека отвечает за реализацию этого стандарта, чтобы предоставлять услуги кэширования любой библиотеке вызовов. Библиотека реализации должна предоставлять класс, реализующий интерфейс Psr \ SimpleCache \ CacheInterface. Реализация библиотек должна поддерживать минимальную функциональность TTL, как описано ниже, с точностью до секунды.

* TTL - время жизни (TTL) элемента - это промежуток времени между тем, когда этот элемент сохраняется, и считается устаревшим. TTL обычно определяется целым числом, представляющим время в секундах, или объектом DateInterval.

* Истечение срока - фактическое время, когда элемент настроен на то, чтобы стать устаревшим. Это вычисляется путем добавления TTL ко времени хранения объекта.

Элемент с 300-секундным TTL, сохраненным в 1:30:00, будет иметь срок действия 1:35:00.



Реализующие библиотеки могут истечь срок действия до истечения запрошенного срока действия, но должны рассматривать элемент как истекший по истечении срока его действия. Если вызывающая библиотека запрашивает сохранение элемента, но не указывает время истечения срока действия или указывает нулевое время истечения срока действия или TTL, Реализующая библиотека может использовать настроенную продолжительность по умолчанию. Если продолжительность по умолчанию не установлена, Библиотека реализации должна интерпретировать это как запрос на кэширование элемента навсегда или до тех пор, пока поддерживает базовая реализация.

Если указан отрицательный или нулевой TTL, элемент должен быть удален из кеша, если он существует, поскольку срок его действия уже истек.

* Ключ - строка по крайней мере из одного символа, которая однозначно идентифицирует кэшированный элемент. Реализация библиотеки должен поддерживать ключи , состоящие из символов __A-Z__, __a-z__, __0-9__, _ , и __.__ в любом порядке в UTF-8 кодировке и длиной до 64 символов. Реализуемые библиотеки должны поддерживать дополнительные символы и кодировки или более длинные длины, но могут поддерживаться как минимум этот минимум. Библиотеки несут ответственность за собственное экранирование ключевых строк в зависимости от ситуации, но должен иметь возможность возвращать исходную немодифицированную строку ключей. Следующие символы зарезервированы для будущих расширений и не поддерживаются реализующими библиотеками:__{}()/\@:__

* Кэш - объект, реализующий Psr\SimpleCache\CacheInterfaceинтерфейс.

* Промахи кэша - промахи кэша вернут значение null, и поэтому определение того, сохранено null что-либо, невозможно. Это главное отклонение от предположений PSR-6.

## 1.3 Кэш

![Только здесь ты увидишь мои альты к картинкам](http://d3fa1t.ninja/content/images/2018/11/cache.jpg)

Реализации могут предоставлять пользователю механизм для указания TTL по умолчанию, если он не указан для конкретного элемента кеша. Если не указано значение по умолчанию, заданное пользователем, реализации должны по умолчанию используют максимальное допустимое значение, разрешенное базовой реализацией. Если базовая реализация не поддерживает TTL, указанный пользователем TTL должен игнорироваться без уведомления.

## 1.4 Данные

Реализующие библиотеки должны поддерживать все сериализуемые типы данных PHP, включая:
* Строки - символьные строки произвольного размера в любой PHP-совместимой кодировке.
* Целые числа - все целые числа любого размера, поддерживаемые PHP, вплоть до 64-битных подписей.
* Floats - все значения с плавающей запятой со знаком.
* Логические значения - Истина и Ложь.
* Null - нулевое значение (хотя при обратном чтении его нельзя будет отличить от промаха кеша).
* Массивы - индексированные, ассоциативные и многомерные массивы произвольной глубины.
* Объекты - любой объект, который поддерживает сериализацию и десериализацию без потерь, например $ o == unserialize (serialize ($ o)). Объекты могут использовать интерфейс PHP Serializable,**__sleep()** или **__wakeup()** магические методы, или аналогичные языковые функции, если это необходимо.

Все данные, переданные в Реализационную библиотеку, должны быть возвращены точно в том виде, в котором они были переданы. Это включает тип переменной. То есть возвращать (string) 5, если (int) 5 было сохраненным значением, является ошибкой. Реализация библиотек должна использовать функции PHP serialize () / unserialize () внутри, но это не обязательно. Совместимость с ними просто используется в качестве основы для приемлемых значений объекта.

Если по какой-либо причине невозможно вернуть точное сохраненное значение, реализующие библиотеки должны ответить промахом в кэше, а не поврежденными данными.

## 2.1 CacheInterface

Интерфейс кэша определяет самые основные операции с коллекцией записей кэша, которые включают в себя базовое чтение, запись и удаление отдельных элементов кэша.

Кроме того, у него есть методы для работы с несколькими наборами записей кэша, таких как запись, чтение или удаление нескольких записей кеша за раз. Это полезно, когда вам нужно выполнить много операций чтения / записи в кэш, и позволяет выполнять операции за один вызов кеш-сервера, значительно сокращая время задержки.

Экземпляр CacheInterface соответствует единственной коллекции элементов кэша с одним ключевым пространством имен и эквивалентен «пулу» в PSR-6. Различные экземпляры CacheInterface могут поддерживаться одним и тем же хранилищем данных, но должны быть логически независимыми.

```PHP
<?php

namespace Psr\SimpleCache;

interface CacheInterface
{
    /**
     * Fetches a value from the cache.
     *
     * @param string $key     The unique key of this item in the cache.
     * @param mixed  $default Default value to return if the key does not exist.
     *
     * @return mixed The value of the item from the cache, or $default in case of cache miss.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if the $key string is not a legal value.
     */
    public function get($key, $default = null);

    /**
     * Persists data in the cache, uniquely referenced by a key with an optional expiration TTL time.
     *
     * @param string                 $key   The key of the item to store.
     * @param mixed                  $value The value of the item to store. Must be serializable.
     * @param null|int|\DateInterval $ttl   Optional. The TTL value of this item. If no value is sent and
     *                                      the driver supports TTL then the library may set a default value
     *                                      for it or let the driver take care of that.
     *
     * @return bool True on success and false on failure.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if the $key string is not a legal value.
     */
    public function set($key, $value, $ttl = null);

    /**
     * Delete an item from the cache by its unique key.
     *
     * @param string $key The unique cache key of the item to delete.
     *
     * @return bool True if the item was successfully removed. False if there was an error.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if the $key string is not a legal value.
     */
    public function delete($key);

    /**
     * Wipes clean the entire cache's keys.
     *
     * @return bool True on success and false on failure.
     */
    public function clear();

    /**
     * Obtains multiple cache items by their unique keys.
     *
     * @param iterable $keys    A list of keys that can obtained in a single operation.
     * @param mixed    $default Default value to return for keys that do not exist.
     *
     * @return iterable A list of key => value pairs. Cache keys that do not exist or are stale will have $default as value.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if $keys is neither an array nor a Traversable,
     *   or if any of the $keys are not a legal value.
     */
    public function getMultiple($keys, $default = null);

    /**
     * Persists a set of key => value pairs in the cache, with an optional TTL.
     *
     * @param iterable               $values A list of key => value pairs for a multiple-set operation.
     * @param null|int|\DateInterval $ttl    Optional. The TTL value of this item. If no value is sent and
     *                                       the driver supports TTL then the library may set a default value
     *                                       for it or let the driver take care of that.
     *
     * @return bool True on success and false on failure.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if $values is neither an array nor a Traversable,
     *   or if any of the $values are not a legal value.
     */
    public function setMultiple($values, $ttl = null);

    /**
     * Deletes multiple cache items in a single operation.
     *
     * @param iterable $keys A list of string-based keys to be deleted.
     *
     * @return bool True if the items were successfully removed. False if there was an error.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if $keys is neither an array nor a Traversable,
     *   or if any of the $keys are not a legal value.
     */
    public function deleteMultiple($keys);

    /**
     * Determines whether an item is present in the cache.
     *
     * NOTE: It is recommended that has() is only to be used for cache warming type purposes
     * and not to be used within your live applications operations for get/set, as this method
     * is subject to a race condition where your has() will return true and immediately after,
     * another script can remove it, making the state of your app out of date.
     *
     * @param string $key The cache item key.
     *
     * @return bool
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if the $key string is not a legal value.
     */
    public function has($key);
}
```
## 2.2 CacheException
```PHP
<?php

namespace Psr\SimpleCache;

/**
 * Interface used for all types of exceptions thrown by the implementing library.
 */
interface CacheException
{
}
```
## 2.3 InvalidArgumentException
```PHP
<?php

namespace Psr\SimpleCache;

/**
 * Exception interface for invalid cache arguments.
 *
 * When an invalid argument is passed, it must throw an exception which implements
 * this interface.
 */
interface InvalidArgumentException extends CacheException
{
}
```

[Источник PHP-FIG](https://www.php-fig.org/psr/psr-16/)

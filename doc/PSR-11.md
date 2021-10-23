#PSR-11

#1. Спецификация
##1.1 Основы
###1.1.1 Идентификаторы записи

	Идентификатор записи - это любая допустимая PHP строка, содержащая по крайней мере один символ, которая однозначно идентифицирует элемент в контейнере. 
	Идентификатор записи - это непрозрачная строка, поэтому вызывающим НЕ СЛЕДУЕТ предполагать, что структура строки несет какое-либо семантическое значение.

###1.1.2 Чтение из контейнера
	Предоставляет Psr\Container\ContainerInterfaceдва метода: getи has.
	getпринимает один обязательный параметр: идентификатор записи, который ДОЛЖЕН быть строкой. 
	getможет возвращать что угодно ( смешанное значение) или выдавать, NotFoundExceptionInterfaceесли идентификатор неизвестен контейнеру. 
	Два последовательных вызова getс одним и тем же идентификатором ДОЛЖНЫ возвращать одно и то же значение. Однако, в зависимости от implementor дизайна и / или userконфигурации, 
	могут быть возвращены разные значения, поэтому userНЕ СЛЕДУЕТ полагаться на получение одного и того же значения при двух последовательных вызовах.	
	has принимает один уникальный параметр: идентификатор записи, который ДОЛЖЕН быть строкой. has ДОЛЖЕН возвращаться, true если идентификатор записи известен контейнеру, 
	а falseесли нет. Если has($id)возвращается false, get($id)ДОЛЖЕН выдавать NotFoundExceptionInterface.

##1.2 Исключения
	Исключения, создаваемые непосредственно контейнером, ДОЛЖНЫ реализовывать Psr\Container\ContainerExceptionInterface.
	Вызов getметода с несуществующим идентификатором ДОЛЖЕН вызывать ошибку Psr\Container\NotFoundExceptionInterface.

##1.3 Рекомендуемое использование
	Пользователям НЕ СЛЕДУЕТ передавать контейнер в объект, чтобы объект мог получить свои собственные зависимости . 
	Это означает, что контейнер используется в качестве локатора услуг, что обычно не рекомендуется.
	Пожалуйста, обратитесь к разделу 4 документа META для получения более подробной информации.

#2. Пакет
	Описанные интерфейсы и классы, а также соответствующие исключения предоставляются как часть пакета psr / container .
	Пакеты, предоставляющие реализацию контейнера PSR, должны декларировать, что они предоставляют psr/container-implementation 1.0.0.
	Проекты, требующие реализации, должны требовать psr/container-implementation 1.0.0.

#3. Интерфейсы

##3.1. Psr\Container\ContainerInterface
```<?php
namespace Psr\Container;

/**
 * Describes the interface of a container that exposes methods to read its entries.
 */
interface ContainerInterface
{
    /**
     * Finds an entry of the container by its identifier and returns it.
     *
     * @param string $id Identifier of the entry to look for.
     *
     * @throws NotFoundExceptionInterface  No entry was found for **this** identifier.
     * @throws ContainerExceptionInterface Error while retrieving the entry.
     *
     * @return mixed Entry.
     */
    public function get($id);

    /**
     * Returns true if the container can return an entry for the given identifier.
     * Returns false otherwise.
     *
     * `has($id)` returning true does not mean that `get($id)` will not throw an exception.
     * It does however mean that `get($id)` will not throw a `NotFoundExceptionInterface`.
     *
     * @param string $id Identifier of the entry to look for.
     *
     * @return bool
     */
    public function has($id);
}```


	Начиная с версии 1.1 psr / container , вышеупомянутый интерфейс был обновлен для добавления подсказок типа аргумента.
	Начиная с версии 2.0 psr / container , вышеупомянутый интерфейс был обновлен для добавления подсказок типа возвращаемого значения (но только для has()метода).


##3.2. Psr\Container\ContainerExceptionInterface
```<?php
namespace Psr\Container;

/**
 * Base interface representing a generic exception in a container.
 */
interface ContainerExceptionInterface
{
}```

##3.3. Psr\Container\NotFoundExceptionInterface
```<?php
namespace Psr\Container;

/**
 * No entry was found in the container.
 */
interface NotFoundExceptionInterface extends ContainerExceptionInterface
{
}```

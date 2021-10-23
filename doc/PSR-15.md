# PSR-15: обработчики запросов HTTP-сервера

В этом документе описываются общие интерфейсы для обработчиков запросов HTTP-сервера («обработчики запросов») и компонентов промежуточного программного обеспечения HTTP-сервера («промежуточное ПО»), которые используют сообщения HTTP, как описано в PSR-7 или последующих заменяющих PSR.

Обработчики HTTP-запросов являются фундаментальной частью любого веб-приложения. Серверный код получает сообщение запроса, обрабатывает его и выдает ответное сообщение. Промежуточное ПО HTTP - это способ перенести обработку общих запросов и ответов с уровня приложения.

Описанные в этом документе интерфейсы являются абстракциями для обработчиков запросов и промежуточного программного обеспечения.

Примечание. Все ссылки на «обработчики запросов» и «промежуточное ПО» относятся к обработке запросов сервера .

Ключевые слова «ДОЛЖЕН», «НЕ ДОЛЖЕН», «ТРЕБУЕТСЯ», «ДОЛЖЕН», «НЕ ДОЛЖЕН», «ДОЛЖЕН», «НЕ ДОЛЖЕН», «РЕКОМЕНДУЕТСЯ», «МОЖЕТ» и «ДОПОЛНИТЕЛЬНО» в этом документе являются следует интерпретировать, как описано в RFC 2119 .

использованная литература
ПСР-7
RFC 2119

# 1. Спецификация
## 1.1 Обработчики запросов

Обработчик запросов - это отдельный компонент, который обрабатывает запрос и выдает ответ, как определено в PSR-7.

Обработчик запроса МОЖЕТ вызвать исключение, если условия запроса не позволяют ему произвести ответ. Тип исключения не определен.

Обработчики запросов, использующие этот стандарт, ДОЛЖНЫ реализовывать следующий интерфейс:

Psr\Http\Server\RequestHandlerInterface

## 1.2 Промежуточное ПО

Компонент промежуточного программного обеспечения - это отдельный компонент, участвующий, часто вместе с другими компонентами промежуточного программного обеспечения, в обработке входящего запроса и создании результирующего ответа, как определено в PSR-7.

Компонент промежуточного программного обеспечения МОЖЕТ создавать и возвращать ответ без делегирования обработчику запросов, если выполняются достаточные условия.

Промежуточное ПО, использующее этот стандарт, ДОЛЖНО реализовывать следующий интерфейс:

Psr\Http\Server\MiddlewareInterface

## 1.3 Получение ответов

РЕКОМЕНДУЕТСЯ, чтобы любое промежуточное программное обеспечение или обработчик запросов, генерирующее ответ, составило либо прототип PSR-7, ResponseInterfaceлибо фабрику, способную генерировать ResponseInterfaceэкземпляр, чтобы предотвратить зависимость от конкретной реализации HTTP-сообщения.

## 1.4 Обработка исключений

РЕКОМЕНДУЕТСЯ, чтобы любое приложение, использующее промежуточное ПО, включало компонент, который перехватывает исключения и преобразует их в ответы. Это промежуточное ПО ДОЛЖНО быть первым выполняемым компонентом и завершать всю дальнейшую обработку, чтобы гарантировать, что ответ всегда генерируется.

# 2. Интерфейсы
## 2.1 Пср \ Http \ Server \ RequestHandlerInterface

Следующий интерфейс ДОЛЖЕН быть реализован обработчиками запросов.

``` PHP

namespace Psr\Http\Server;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

/**
 * Handles a server request and produces a response.
 *
 * An HTTP request handler process an HTTP request in order to produce an
 * HTTP response.
 */
interface RequestHandlerInterface
{
    /**
     * Handles a request and produces a response.
     *
     * May call other collaborating code to generate the response.
     */
    public function handle(ServerRequestInterface $request): ResponseInterface;
}
```

## 2.2 Пср \ Http \ Server \ MiddlewareInterface

Следующий интерфейс ДОЛЖЕН быть реализован совместимыми компонентами промежуточного программного обеспечения.


``` PHP

namespace Psr\Http\Server;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

/**
 * Participant in processing a server request and response.
 *
 * An HTTP middleware component participates in processing an HTTP message:
 * by acting on the request, generating the response, or forwarding the
 * request to a subsequent middleware and possibly acting on its response.
 */
interface MiddlewareInterface
{
    /**
     * Process an incoming server request.
     *
     * Processes an incoming server request in order to produce a response.
     * If unable to produce the response itself, it may delegate to the provided
     * request handler to do so.
     */
    public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface;
}
```


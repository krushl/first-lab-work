# PSR-18: HTTP Client
PSR-18 описывает общие интерфейсы для отправки PSR-7 HTTP-запросов и получения HTTP-ответов.

*ClientInterface, RequestExceptionInterface и NetworkExceptionInterface*

## ClientInterface
```interface ClientInterface
{
    public function sendRequest(RequestInterface $request): ResponseInterface;
}
```

## RequestExceptionInterface
```interface RequestExceptionInterface extends ClientExceptionInterface
{
    public function getRequest(): RequestInterface;
}
```

## NetworkExceptionInterface
```interface NetworkExceptionInterface extends ClientExceptionInterface
{
    public function getRequest(): RequestInterface;
}
```

Это может сделать библиотеки более пригодными для повторного использования, так как уменьшает количество зависимостей и снижает вероятность конфликтов версий.

Также в спецификации указано:
> HTTP-клиенты могут быть заменены согласно принципу подстановки Лисков.
Это означает, что все клиенты ДОЛЖНЫ вести себя одинаково при отправке запроса.

Пример реализации PSR-18 можно увидеть в библиотеке Symfony HTTP-client.

Предложенная абстракция над HTTP — это весьма серьезная заявка на попытку реализовывать действительно переиспользуемые и не зависящие от конкретных библиотек и фреймворков полноценные решения.

На практике, конечно все на много сложнее и есть свои нюансы и подводные камни, однако PHP-FIG делает значительный шаг вперед в этом направлении.
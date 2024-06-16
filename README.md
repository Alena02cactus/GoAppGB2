# GoAppGB2


### Описание
Продолжаем развивать наше зарождающееся микросервисное приложение.
В этом задании мы добавим новый сервис с публичным контрактом и свяжем его с двумя нашими микросервисами посредством 
gRPC.

Самая главная задача разберитесь с тем, что уже есть в репозитории. Я создал для вас простой контракт для openapi и 
gRPC. Также я сделал Makefile, на семинаре работали с ним немного.

### Про утилиты
Напомню, установите команду make. Чтобы запускать рецепты Makefile нужно всего лишь написать
```shell
make build
```

или

```shell
make docker
```

Некоторые рецепты вам здорово упростят жизнь. Например 
```shell
make generate
```

Перегенерирует вам gRPC или openapi каркасы если вы внесли в них изменения. Я стараюсь контракты описывать за вас, 
чтобы возникло меньше сложностей. 

Для проверки работы сервиса можно воспользоваться утилитой [grpcui](https://github.com/fullstorydev/grpcui)
В коде сервиса указан запуск reflection api, чтобы эта утилитка могла работать. Она работает через web gui.

Для проверки http воспользуйтесь утилитой [curl](https://curl.se/download.html) или [postman](https://www.postman.
com/downloads/)

### Важная информация
Еще раз ознакомьтесь с проектом, посмотрите на новые файлы и далее приступайте к изучению, что мы хотим сделать в 
этом задании. Сразу хочется сказать, что это учебный пример, и мы возвращаем модель с паролем. Я предлагаю просто 
возвращать модель с пустой строкой. В реальном апи нужно возвращать другую модель, обрезанную. Также мы пока не 
учитываем, что пароли нужно хешировать и хранить в хешированном виде. Позже мы вернемся к этому.


### Что нового в проекте

Итак у нас появляется третий сервис это апи гейтвей. Более подробно об этом паттерне я расскажу на 3ей лекции. Мы 
будем реализовывать его самостоятельно, хотя существуют и готовые решения для подобных задач.

Идея следующая:
* http запрос приходит на api-gw на определенный хэндлер
* Далее с помощью gRPC клиента соответствующего мы делаем запрос в сервис users-srv
  или links-srv и получаем ответ
* Упаковываем ответ в модель openapi и возвращаем клиенту.
* Пока на этом все.


## Что нужно реализовать?

* Хэндлеры api-gw которые лежат в internal/apigw/v1 . Логика реализуется в файлах links.go и users.go. Это хэндлеры 
  согласно спецификации. Они должны получать запрос и перенаправлять его в нужный сервис через grpc с помощью grpc 
  клиента, который туда уже проброшен. Вы увидите у него соответствующие методы. 
* Хэндлеры grpc сервера для users и links, которые лежат по пути internal/link/linkgrpc и internal/user/usergrpc . 
  Тут соответственно мы должны получить запрос и с помощью репозиториев usersRepository или linksRepository 
  создавать, получать,обновлять или удалять сущности User, Link. Сам код репозитория уже написан. Если вы видите 
  ошибки не стисняйтесь их исправлять и говорить мне. Я исправлю в шаблоне.

## Как все проверить

Вы должны делать запросы на наше апи по адресу http://localhost:8080/api/v1 на разные ресурсы и у 
вас все должно работать.
1) Запустить отдельно все 3 сервиса. Убедитесь, что сервис стартовал и вывел свой порт
2) Можно отлаживать grpc с помощью grpcui там веб интерфейс все интуитивно понятно
3) Делать запросы в api-gw по адресу http://localhost:8080/api/v1/users например
4) curl -vvv -XGET http://localhost:8080/api/v1/users/58a06aa0-633e-45bd-976a-c5171413b3ea должен вернуть 
   пользователя с id 58a06aa0-633e-45bd-976a-c5171413b3ea. Для этого его сначала нужно создать через POST

Через api-gw вы должны уметь
* Создавать пользователя, получать пользователя по id, удалять пользователя, обновлять пользователя, отображать всех 
  пользователей
* Создавать ссылку, получать ссылку по id, удалять ссылку, обновлять ссылку, отображать все ссылки
* Когда запрос приходит на api-gw он должен через клиент gRPC делать соответствующие запросы на нужный сервис по 
  gRPC, получать ответ, преобразовывать ответ и отдавать по HTTP

### Коммуникация
Если что-то не получается коммуницируйте в группе и делитесь знаниями. Это ускоряет прогресс всей группы

### Документация

Пока в папке docs лежит только упрощенная схема текущего приложения, которая будет обновляться)
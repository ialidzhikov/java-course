class: center, middle

# REST

---

### REpresentational State Transfer (REST)

- REST е стил софтуерна архитектура за реализация на уеб услуги.
  - REST не е спецификация или протокол
- Състои се от 6 принципа, които допринасят към това уеб услугата да бъде проста, бързодействаща и скалируема.
- Има за цел да подобри и улесни комуникацията между две системи.
- Уеб услугите, които покриват REST принципите, се наричат RESTful уеб услуги.

---

### Принципи на REST архитектурата

1. REST е клиент-сървър архитектура
1. REST предоставя единен интерфейс (контракт)
1. REST не пази състояние (stateless)
1. REST предлага възможности за кеширане
1. REST може да бъде многослойна система (optional)
1. REST може да предостави code on demand (optional)

---

### REST е клиент-сървър архитектура

- Клиентът и сървърът са отделни компоненти
- Separation of concerns - клиентът и сървърът имат различни задачи
- Сървърът:
  - съдържа бизнес логиката
  - грижи се за съхранението на данните
- Клиентът:
  - извлича данни от сървъра и се грижи се за представянето на данните
  - позволява CRUD операции върху ресурсите
  - примери: уеб клиенти - браузъри, мобилни приложения, конзолни клиенти и др.

---

### REST е клиент-сървър архитектура (2)

- Единният интерфейс позволява разделянето на клиент и сървър.
- Клиентът и сървърът могат да бъдат променяни независимо един от друг, стига това да не променя единния интерфейс помежду им.

---

### Пример за клиент-сървър

- Дa предположим, че искаме да извлечем информация за даден потребител от GitHub. REST API документация за въпросната операция - [link](https://docs.github.com/en/rest/users/users?apiVersion=2022-11-28#get-a-user).
- Можем да го направим с:
  - уеб клиент - `https://github.com/{username}`
  - заявка към REST API-то
     ```bash
     $ curl https://api.github.com/users/{username}
     ```
  - Java клиент - [hub4j/github-api](https://github.com/hub4j/github-api/)

---

### Ресурси

- REST въвежда концепцията за ресурс.
- Всяка информация, която може да бъде именувана, може да бъде ресурс.
- Ние, като програмисти, дефинираме кои са ресурсите, с които ще оперира нашата уеб услуга.
- В примера, който разгледахме, ресурси бяха дадените GitHub потребители.
- Ресурси могат да бъдат html страници, js файлове, изображения, потребители и др.

---

### REST предоставя единен интерфейс

- REST предоставя единен интерфейс (контракт) между клиента и сървъра.
- Следните 4 изисквания оформят единния интерфейс:
  - Идентификация на ресурси
  - Промяна на ресурси посредством техните представяния
  - Описателни представяния на ресурси и съобщения
  - Hypermedia as the engine of application state (HATEOAS)

---

### Идентификация на ресурси

- Всеки ресурс се идентифицира чрез URI (Uniform Resource Identifier).
- Използвайки различни HTTP методи (`GET`, `PUT`, `DELETE` и др.) се извършват въпросните операции върху ресурса.
- Пример:
   ```bash
   # Get a repo
   $ curl -X GET https://api.github.com/repos/octocat/Hello-World

   # Update a repo
   $ curl -X PATCH https://api.github.com/repos/octocat/Hello-World -d "$REQUEST_BODY"

   # Delete a repo
   $ curl -X DELETE https://api.github.com/repos/octocat/Hello-World
   ```

- Забележете, че в примера URI-ът на ресурса не се променя. Променят се само HTTP методите.

---

### Представяне на ресурси

- Ресурсите са концептуално разделени от представянията, които се връщат към клиентите.
- Например, сървър пази ресурсите си в база от данни и на файловата система, а връща на клиентите ресурси под формата на HTML, JSON и XML.
- Не винаги върнатото представяне е вътрешното представяне.
- Ресурсите трябва да имат еднакви представяния в отговорите от сървъра.
- Ползвателите на REST API-то трябва да използват тези представяния, за да модифицират състоянието на ресурса в сървъра.

---

### REST предоставя единен интерфейс (пример)

- Опити за неспазване на контракта на [Github REST API](https://docs.github.com/en/rest?apiVersion=2022-11-28)-то:

   ```bash
   # Github API does not support text/html
   # instead use application/json
   $ curl --header "Accept: text/html" \
      https://api.github.com/repos/fmi/java-course
   
   # repozzz resource does not exist
   $ curl https://api.github.com/repozzz/fmi/java-course
   ```

---

### Примери за REST API-та

- [Github REST API](https://developer.github.com/v3/)
- Полезен списък с публични REST API-та - [public-apis/public-apis](https://github.com/public-apis/public-apis)

---

### REST != HTTP

- В практиката често термините HTTP и REST са взаимнозаменяеми.
- HTTP е най-използваният протокол за имплементиране на RESTful уеб услуги, но REST не налага използването на HTTP.
- HTTP по дефиниция покрива част от REST принципите, но не всяко HTTP API е RESTful. Примери:
  - Немалко HTTP API-та използват бисквитки (cookies), за да пазят някакво състояние. Това нарушава принципа, че  REST не пази състояние (stateless).
  - HTTP API може да наруши семантиката на HTTP метода и единния интерфейс.
   ```
   GET /products?id=22        # извличане на ресурс
   GET /products?delete_id=22 # изтриване на ресурс
   ```
   Семантично правилният начин:
   ```
   GET /products?id=22        # извличане на ресурс
   DELETE /products?id=22     # изтриване на ресурс
   ```

---

class: center, middle

# JSON

---

### Формати за обмен на данни

- Форматите за обмен на данни са зависими от използваната архитектура или протокол
- Уеб услуги, базирани на SOAP - поддържат само XML
- Уеб услуги, базирани на REST:
  - REST не налага използването на конкретен формат за обмен на данни
  - Най-използваният формат при RESTful уеб услугите е JSON
  - XML, YAML, Protocol Buffers (protobuf) и други формати също намират приложение

---

### JavaScript Object Notation (JSON)

- Един от най-популярните формати за обмен на данни
- Произлиза от JavaScript
- Използва се за предаване на структурирани данни по мрежова комуникация
- Намира широко приложение в RESTful уеб услугите за представяне на данни

---

### JSON - характеристики

- Web friendly - тривиално парсване (за разлика от XML)
- Кратък и интуитивен
- Разбираем едновременно от човек и машина
- Езиково независима спецификация - [RFC 7159](https://tools.ietf.org/html/rfc7159)
- Почти всички езици поддържат JSON

---

### Пример

```bash
$ curl https://api.github.com/users/kelseyhightower
```

<br>

```json
{
  "login": "kelseyhightower",
  "name": "Kelsey Hightower",
  "company": "Google, Inc",
  "location": "Portland, OR",
  "email": null,
  "hireable": true,
  "followers": 10041,
  "following": 13
}
```

---

### Типове данни

- Boolean - `true` / `false`
- Number - `42`, `3.14156`, `-1`
- String - `"foo"`, `"bar"`
- Array - `["John", "Anna", "Peter"]`
- Object - `{"name": "John", "age": 30}`
- Поддържа `null`, `{}`, но не и функция, дата или `undefined`

---

### Пример (2)

```bash
$ curl https://api.github.com/repos/kubernetes/kubernetes/tags
```

<br>

```json
[
  {
    "name": "v1.14.0-alpha.0",
    "commit": {
      "sha": "1f56cd801e795fd063ec3e61fe4f6fa8841f4222"
    }
  },
  {
    "name": "v1.13.2-beta.0",
    "commit": {
      "sha": "efe48f3cd6436737d37fd2fcd6beb9e2328f7cce"
    }
  }
]
```

---

### Java библиотеки за работа с JSON

- [google/gson](https://github.com/google/gson)
- [FasterXML/jackson](https://github.com/FasterXML/jackson)
- ...

---

### google/gson

- Предоставя лесен механизъм за преобразуване от Java обект към JSON и обратно
    - `.toJson()` и `.fromJson()`
- Не изисква добавяне на анотации или прекомпилиране
- Позволява вмъкването на custom serializer/deserializer
- Поддържа Builder, който позволява конфигуриране на различни опции
- [Gson User Guide](https://github.com/google/gson/blob/main/UserGuide.md)

---

### `.toJson(Object src)`

```java
public class Developer {
    private String name;
    private int age;
    public Developer(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

<br>

```java
Developer dev = new Developer("Kelsey", 28);
Gson gson = new Gson();
String json = gson.toJson(dev);
// {"name":"Kelsey","age":28}
System.out.println(json);
```

---

### `.fromJson(String json, Class<T> classOfT)`

```java
String json = "{\"name\": \"Wesley\", \"age\": 20 }";
Gson gson = new Gson();
Developer dev = gson.fromJson(json, Developer.class);
// Wesley, 20 years old
System.out.printf("%s, %d years old%n",
    dev.getName(), dev.getAge());
```

---

### `.fromJson(Reader json, Class<T> classOfT)`

```java
Gson gson = new Gson();

try (var reader = new FileReader(new File("devs.json"))) {
    // unchecked conversion
    List<Developer> devs = gson.fromJson(reader, List.class);
}
```

---

### `.fromJson(String json, Type typeOfT)`

```java
List<Developer> devs = List.of(
        new Developer("Kelsey", 28),
        new Developer("Wesley", 20));

Gson gson = new Gson();
String json = gson.toJson(devs);

Type type = new TypeToken<List<Developer>>(){}.getType();
List<Developer> devsAgain = gson.fromJson(json, type);

System.out.println(devsAgain.size()); // 2
```

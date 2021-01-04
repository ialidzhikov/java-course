## REST

---

#### REpresentational State Transfer (REST)

@ul

- REST е стил софтуерна архитектура за реализация на уеб услуги.
  - REST не е спецификация или протокол
- Състои се от 6 принципа, които допринасят към това уеб услугата да бъде проста, бързодействаща и скалируема.

@ulend

---

#### REST (2)

@ul

- Има за цел да подобри и улесни комуникацията между две системи.
- Уеб услугите, които покриват REST принципите, се наричат RESTful уеб услуги.
- WWW е RESTful

@ulend

---

#### Принципи на REST архитектурата

@ul

- REST е клиент-сървър архитектура
- REST предоставя единен интерфейс (контракт)
- REST не пази състояние (stateless)
- REST предлага възможности за кеширане
- REST е многослойна система
- REST предоставя code on demand

@ulend

---

#### REST is client-server architecture

@ul

- Клиентът и сървърът са отделни компоненти
- Separation of concerns:
  - Сървърът съдържа бизнес логиката и се грижи за съхранението на данните
  - Клиентът извлича данни от сървъра и се грижи за представянето на данните:
    - уеб клиенти - браузъри
    - конзолни клиенти
    - ...

@ulend

---

#### REST is client-server architecture (2)

@ul

- Единният интерфейс позволява разделянето на клиент и сървър.
- Клиентът и сървърът могат да бъдат променяни независимо един от друг, стига това да не променя единния интерфейс помежду им.

@ulend

---

#### Пример за клиент-сървър

@ul

- Дa предположим, че искаме да извлечем информация за даден потребител от GitHub. REST API документация за въпросната операция - [link](https://docs.github.com/en/free-pro-team@latest/rest/reference/users#get-a-user).
- Можем да го направим през:
  - уеб клиент - @size[1.0em](https://github.com/{username})
  - Java клиент - [hub4j/github-api](https://github.com/hub4j/github-api/)
  - заявка към REST API-то
    - @size[0.75em]($ curl https://api.github.com/users/{username})
  - ...

@ulend

---

#### Ресурси

@ul

- REST въвежда концепцията за ресурс.
- Всяка информация, която може да бъде именувана, може да бъде ресурс.
- Ние, като програмисти, дефинираме кои са ресурсите, с които ще оперира нашата уеб услуга.
- В примера, който разгледахме, ресурси бяха дадените GitHub потребители.
- Ресурси могат да бъдат html страници, js файлове, изображения, потребители и др.

@ulend

---

#### Идентификатори на ресурси

- Всеки ресурс се идентифицира чрез URI (uniform resource identifier)
- Примери:

```bash
# Get all repos in fmi organisation
$ curl https://api.github.com/orgs/fmi/repos
# Get fmi/java-course repo
$ curl https://api.github.com/repos/fmi/java-course
```

---

#### Представяне на ресурси

@ul

- Ресурсите са концептуално разделени от представянията, които се връщат към клиентите.
- Например сървър пази ресурсите си в база от данни и на файловата система, а връща на клиентите ресурси под формата на HTML, JSON и XML.
- Не винаги върнатото представяне е вътрешното представяне.

@ulend

---

#### REST provides a uniform interface

- REST предоставя единен интерфейс (контракт) между клиента и сървъра.
- Контрактът се изгражда въз основа на ресурсите.
- [Github REST API](https://developer.github.com/v3/)

```bash
# Github API does not support text/html
# instead use application/json
$ curl --header "Accept: text/html" \
  https://api.github.com/repos/fmi/java-course
# repozzz resource does not exist
$ curl https://api.github.com/repozzz/fmi/java-course
```

---

## JSON

---

@ul

- Форматите за обмен на данни са зависими от използваната архитектура:
  - Уеб услуги, базирани на SOAP - само XML
  - Уеб услуги, базирани на REST - JSON, XML и други.

@ulend

---

#### JavaScript Object Notation (JSON)

@ul

- един от най-популярните формати за обмен на данни
- произлиза от JavaScript
- използва се за предаване на структурирани данни по мрежова комуникация
- намира широко приложение в RESTful уеб приложенията за предоставяне на данни

@ulend

---

#### JSON - характеристики

@ul

- web friendly - тривиално парсване (за разлика от XML)
- кратък и интуитивен
- разбираем едновременно от човек и машина
- езиково независима спецификация - [RFC 7159](https://tools.ietf.org/html/rfc7159)
- почти всички езици поддържат JSON

@ulend

---

#### Пример

```bash
$ curl https://api.github.com/users/kelseyhightower
```

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

#### Типове данни

@ul

- Boolean - `true` / `false`
- Number - `42`, `3.14156`, `-1`
- String - `"foo"`, `"bar"`
- Array - `["John", "Anna", "Peter"]`
- Object - `{"name": "John", "age": 30}`
- Поддържа `null`, `{}`, но не и функция, дата или `undefined`

@ulend

---

#### Пример (2)

```bash
$ curl https://api.github.com/repos/kubernetes/kubernetes/tags
```

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

#### Java библиотеки за работа с JSON


@ul

- [google/gson](https://github.com/google/gson)
- [FasterXML/jackson](https://github.com/FasterXML/jackson)
- ...

@ulend

---

#### `google/gson`

@ul

- Предоставя лесен механизъм за преобразуване от Java обект към JSON и обратно
    - .toJson() и .fromJson()
- Не изисква добавяне на анотации или прекомпилиране
- Позволява вмъкването на custom serializer/deserializer
- Комплексен Builder
- [Gson User Guide](https://github.com/google/gson/blob/master/UserGuide.md)

@ulend

---

#### `.toJson(Object src)`

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

```java
Developer dev = new Developer("Kelsey", 28);
Gson gson = new Gson();
String json = gson.toJson(dev);
// {"name":"Kelsey","age":28}
System.out.println(json);
```

---

#### `.fromJson(String json, Class<T> classOfT)`

```java
String json = "{\"name\": \"Wesley\", \"age\": 20 }";
Gson gson = new Gson();
Developer dev = gson.fromJson(json, Developer.class);
// Wesley, 20 years old
System.out.printf("%s, %d years old%n",
    dev.getName(), dev.getAge());
```

---

#### `.fromJson(Reader json, Class<T> classOfT)`

```java
Gson gson = new Gson();
FileReader reader = new FileReader(new File("devs.json"));
// unchecked conversion
List<Developer> devs = gson.fromJson(reader, List.class);
```

---

#### `.fromJson(String json, Type typeOfT)`

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

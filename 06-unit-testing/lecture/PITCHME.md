## Stubbing and mocking

---

#### Unit тестване на класове със зависимости

@ul

- Често нашите класове, за да свършат своята работа, използват други класове
- Познато ви е като композиция
- Това е съвсем очаквано и нормално :)
- Как unit тестваме такива класове?
- Да разгледаме един такъв клас

@ulend

---

```java
public class UserService {

	private UserRepository repository;
	private MailService mailService;

	public User register(String email, String password) {
		if (repository.exists(email)) {
			throw new UserAlreadyExistsException();
		}

		User user = new User(email, password);
		repository.save(user);
		mailService.sendWelcomeMail(email);
		return user;
	}
}
```

@[3-4]
@[7]
@[12]
@[13]

---

@ul

- Нека UserRepository е интерфейс, чиято задача е да съхранява User-и (in-memory, file system, database, etc.)
- Нека MailService също е интерфейс, чиято задача е да праща мейли

@ulend

---

#### Test cases for register()

@ul

- Методът register() има 2 exit point-a - следователно имаме 2 сценария за покриване
- [TC1] register() хвърля подходящо изключение, когато мейл адресът вече съществува в хранилището
- [TC2] register() запазва подходящия user в хранилището и изпраща welcome мейл, когато мейл адресът не съществува в хранилището
- Как unit тестваме UserService класа?

@ulend

---

@ul

- При unit тестване се интересуваме от функционалната коректност само на класа, който се тества
- Трябват ни инструменти, чрез които да "изолираме" композираните класове
- Композираните класове могат да бъдат трудни за инстанциране
- Например UserRepository изисква connectivity към база от данни

@ulend

---

@ul

- Доброто unit тестване се базира на изолация
- Изолацията се постига чрез stub/mock обекти

@ulend

---

#### Stubbing

@ul

- Stub - клас, който отговаря на дадени извиквания на методи с предварително зададени отговори
- В unit тестването ни служат за справяне с проблема с композираните класове

@ulend

---

#### Stubbing

@ul

- Обикновено имплементират по минимален начин даден интерфейс и се подават на класа, който се тества
- Извън unit тестването, могат да бъдат използвани и като заместител на код, който още не е разработен

@ulend

---

__PositiveUserRepositoryStubImpl__

```java
public class PositiveUserRepositoryStubImpl
				implements UserRepository {

	@Override
	public boolean exists(String email) {
		return true;
	}

	@Override
	public void save(User user) {
		// Do nothing
	}
}
```

---

__InMemoryUserRepositoryStubImpl__

```java
public class InMemoryUserRepositoryStubImpl
				implements UserRepository {

	private Map<String, User> users = new HashMap<>();

	@Override
	public boolean exists(String email) {
		return users.containsKey(email);
	}

	@Override
	public void save(User user) {
		users.put(user.getEmail(), user);
	}
}
```

---

#### The stub way

```java
@Test(expected = UserAlreadyExistsException.class)
public void testRegisterThrowsAppropriateException() {
	UserService service =
		new UserService(new PositiveUserRepositoryStubImpl());

	service.register("test@test.com", "weak");
}
```

@[3-4]
@[6]
@[1]

---

#### Test lifecycle with stub

1. Setup data - подготвяме обекта, който ще се тества, както и stub събратята му
2. Exercise - извикваме метода
3. Verify state - използваме assertions, за да проверим състоянието на обекта
4. Teardown - освобождаваме използваните ресурси

---

#### Характеристики на stub-овете

@ul

- Могат да съдържат логика, която не е тривиална (напр. InMemoryUserRepositoryStubImpl) (+)
- Броят на stub-овете расте експоненциално (-)
- Не може да проверим дали даден метод на stub-a е извикан определен брой пъти (-)

@ulend

---

#### Mocking

@ul

- Mock - конфигуриран обект с предварително зададени отговори на дадени извиквания на методи
- Динамични wrapper-и за композираните класове
- По подобие на stub-овете, ни служат за справяне с проблема с композираните класове

@ulend

---

#### The mock way

```java
@Test(expected = UserAlreadyExistsException.class)
public void testRegisterThrowsAppropriateException() {
	UserRepository mock = mock(UserRepository.class);
	when(mock.exists("test@test.com")).thenReturn(true);

	UserService service = new UserService(mock);
	service.register("test@test.com", "weak");
}
```

@[3]
@[6]
@[4]
@[7]
@[1]

---

#### Test lifecycle with mock

1. Setup data - подготвяме обекта, който ще се тества, както и mock събратята му
2. Setup expectations - задаваме желаните отговори
3. Exercise - извикваме метода
4. Verify expectations - уверяваме се, че правилният метод на mock-a се е извикал
5. Verify state - използваме assertions, за да проверим състоянието на обекта
6. Teardown - освобождаваме използваните ресурси

---

#### To mock or not to mock?

```java
public class Cinema {
	private Map<String, Projection> projections;

	public Cinema(Map<String, Projection> projections) {
		this.projections = projections;
	}

	public boolean buyTicket(String projection, int amount) {
		if (!projections.containsKey(projection)) {
			return false;
		}
		// [...]
		return true;
	}
}
```

---

#### Do not mock

```java
@Test
public void testBuyTicket() {
	Map<String, Projection> projections =
			Map.of("foo", new Projection("foo"));
	Cinema cinema = new Cinema(projections);

	boolean actual = cinema.buyTicket("bar", 3);
	assertFalse(actual);
}
```

---

#### To mock or not to mock?

```java
public class Cinema {
	private ProjectionService service;

	public Cinema(ProjectionService service) {
		this.service = service;
	}

	public boolean buyTicket(String projection, int amount) {
		if (!service.contains(projection)) {
			return false;
		}
		// [...]
		return true;
	}
}
```

---

#### Mock

```java
@Test
public void testBuyTicket() {
	ProjectionService mock = mock(ProjectionService.class);
	when(mock.contains("bar")).thenReturn(false);

	Cinema cinema = new Cinema(mock);

	boolean actual = cinema.buyTicket("bar", 3);
	assertFalse(actual);
}
```

---

#### Използвайте mock-ове, когато:

@ul

- Композираният клас се обръща към външен ресурс (REST API, database, file system, etc.)
- Логиката в композирания клас не е тривиална
- Не може да настроите test environment-a по тривиален начин

@ulend

---

#### Не използвайте mock-ове, когато:

@ul

- Композираният клас представлява value object, който може да подадете отвън
- Може тривиално да настроите test environment-a

@ulend

---

#### Mocking libraries

@ul

- [Mockito](https://github.com/mockito/mockito)
- [EasyMock](https://github.com/easymock/easymock)
- [PowerMock](https://github.com/powermock/powermock)\*
- \* Putting it in the hands of junior developers may cause more harm than good.

@ulend

---

#### Mockito

@ul

- Ще разглеждаме mockito (3.1.0) като mocking library
- Възниква като разширение на функционалността на EasyMock
- Една от 10-те най-популярни Java библиотеки като цяло
- Open-source

@ulend

---

#### Setup

@ul

- Mockito е външна библиотека
- Може да я изтеглите от [тук](http://central.maven.org/maven2/org/mockito/mockito-core/3.1.0/)
- Изтеглете mockito-core jar-a и 3-те му compile dependency-та
- Ако ползвате IDE, добавете въпросните jar-ки в class path-a на проекта си
- Алтернативно, ако сте запознати с maven/gradle, ползвайте тях :)

@ulend

---

#### Setup (2)

```
fancy-project
  └─ src/
    └─ (...)
  └─ test/
    └─ (...)
  └─ lib/
    └─ byte-buddy-1.9.10.jar
    └─ byte-buddy-agent-1.9.10.jar
    └─ mockito-core-3.1.0.jar
    └─ objenesis-2.6.jar
```

---

__mock() and verify()__

```java
import static org.mockito.Mockito.*;

List mockedList = mock(List.class);

mockedList.add("one");
mockedList.clear();
mockedList.get(0);

verify(mockedList).add("one");
verify(mockedList, atLeastOnce()).clear();
verify(mockedList, never()).add("two");
```

@[1]
@[3]
@[5-7]
@[9-11]

---

__when()__

```java
LinkedList mockedList = mock(LinkedList.class);

when(mockedList.get(0)).thenReturn("first");
when(mockedList.get(1)).thenThrow(new RuntimeException());

mockedList.get(0);
mockedList.get(1);
```

---

__Argument matchers__

```java
when(mockedList.get(anyInt())).thenReturn("element");

mockedList.get(999);
```

---

__@Mock annotation__

```java
@RunWith(MockitoJUnitRunner.class)
public class UserServiceTest {
	@Mock
	private UserRepository repositoryMock;

	@Test(expected = UserAlreadyExistsException.class)
	public void testRegisterThrowsAppropriateException() {
		when(repositoryMock.exists("test@test.com"))
				.thenReturn(true);

		UserService service =
				new UserService(repositoryMock);

		service.register("test@test.com", "weak");
	}
}

```

---

#### Mockito limitations

@ul

- Не може да mock-ва static методи (static is evil)
- Не може да mock-ва конструктори
- Не може да mock-ва final класове и методи (*)
- Не може да mock-ва методите equals() и hashCode()

@ulend

---

#### Best practices

@ul

- Не правете йерархии от тестови класове
- Не mock-вайте типове, които не притежавате
- Mock-вайте само толкова колкото ви трябва за конкретния тест
- Не mock-вайте value обекти
- Keep it short and simple (KISS)
- Redesign when you cannot test it

@ulend

---

#### Полезни четива

@ul

- [JUnit javadoc](https://junit.org/junit4/javadoc/latest/index.html)
- [Unit Testing with JUnit](http://www.vogella.com/tutorials/JUnit/article.html)
- [Mocks aren't stubs](https://martinfowler.com/articles/mocksArentStubs.html) by Martin Fowler
- [Writing good tests](https://github.com/mockito/mockito/wiki/How-to-write-good-tests) by Mockito team

@ulend

---

#### Полезни четива

![Practical unit testing](images/05.4-practical-unit-testing.png?raw=true)

---

## Въпроси

@snap[south span-100]
@fab[github] [fmi/java-course](https://github.com/fmi/java-course)
@snapen

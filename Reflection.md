# The Java

## 3. 리플렉션
### * 클래스 정보 조회

* 리플렉션의 시작은 Class<T>

```java
package org.example;

public class Book {

	private static String B = "Book";

	private static final String C = "Book";

	private String a = "a";

	public String d = "d";

	protected String e = "e";

	public Book() {
	}

	public Book(String a, String d, String e) {
		this.a = a;
		this.d = d;
		this.e = e;
	}

	private void f() {
		System.out.println("F");
	}

	public void g() {
		System.out.println("G");
	}

	public int h() {
		return 100;
	}
}

```

위와같은 Book 클래스가 있다고 가정할때  
  

* Class<T>에 접근하는 방법
  * 모든 클래스를 로딩 한 다음 Class<T>의 인스턴스가 생긴다. "타입.class"로 접근 가능
```java
Class<Book> bookClass = Book.class;
```
  * 모든 인스턴스는 getClass()메소드를 가지고 있다. "인스턴스.getClass()"로 접근 가능
```java
Book book = new Book();
Class<? extends Book> aClass = book.getClass();
```
  * 클래스를 문자열로 읽어오는 방법
    * Class.forName("FQCN)
    * 클래스 패스에 해당 클래스가 없다면 ClassNotFoundException이 발생함
```java
Class<?> aClass1 = Class.forName("org.example.Book");
```

리플렉션이 제공하는 api로 Class가 가지고있는 정보를 접근하는 방법들

### * public field접근

![image](https://user-images.githubusercontent.com/60100532/187625935-98389fe1-dc50-4ac0-9435-ddb186318c10.png)

![image](https://user-images.githubusercontent.com/60100532/187626152-d9bdf6dd-511a-4f01-a282-555c99d677f6.png)

<br>

---
### * 모든 필드 접근

![image](https://user-images.githubusercontent.com/60100532/187626475-a50d7ed2-b210-4805-9698-bc6e9d66a182.png)

<br>

---
### * 선언된 메소드

![image](https://user-images.githubusercontent.com/60100532/187628320-5acdab48-f4f8-48c7-b426-ecf958b4db11.png)

<br>

---
### * 생성자 
![image](https://user-images.githubusercontent.com/60100532/187628719-540a9733-925a-40b1-ab9e-58eb9f599dbb.png)

<br>

---
### * 상위클래스
![image](https://user-images.githubusercontent.com/60100532/187629353-cee85aca-8ad2-4ad5-b874-7d270769f598.png)

![image](https://user-images.githubusercontent.com/60100532/187630305-12e9e76e-3129-46c3-b24a-a18c9a4f8837.png)

---
### * 어노테이션과 리플렉션
* 중요 어노테이션
  * @Retention : 해당 어노테이션을 언제까지 유지할 것인가? 소스, 클래스, 런타임
  * @Inherit : 해당 어노테이션을 하위 클래스까지 전달할 것인가?
  * @Target : 어디에 사용할 수 있는가?  
  

* 리플렉션
  * getAnnotations(): 상속받은 ( @Inherit ) 어노테이션까지 조회
  * getDeclaredAnnotations(): 자기 자신에만 붙어있는 어노테이션 조회

![image](https://user-images.githubusercontent.com/60100532/187649044-636efb50-f791-4c64-976e-bf7d98f87574.png)

---
### * 클래스 정보 수정 또는 실행

```java


import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class App
{
	public static void main( String[] args ) throws
		ClassNotFoundException,
		NoSuchMethodException,
		InvocationTargetException,
		InstantiationException,
		IllegalAccessException,
		NoSuchFieldException {
		Class<?> bookClass = Class.forName("org.example.Book");
		Constructor<?> constructor = bookClass.getConstructor(null); // 기본 생성자
		Book book = (Book) constructor.newInstance();

		Constructor<?> constructorWithParam = bookClass.getConstructor(String.class); // String을 파라미터로 받는 생성자.
		Book myBook = (Book)constructorWithParam.newInstance("myBook");

		System.out.println("book = " + book);
		System.out.println("myBook = " + myBook);

		Field a = Book.class.getDeclaredField("A");
		// a라는 필드가 특정 인스턴스에 해당하는 필드면 인스턴스를 넘겨줄수있는데 A는 static변수이기때문에 모두가 공유 -> null을 넘겨주면됨
		System.out.println("a.get(null) = " + a.get(null));
		a.set(null, "AAAAA");
		System.out.println("a.get(null) = " + a.get(null));


		Field b = Book.class.getDeclaredField("B");
		b.setAccessible(true); // 원래는 B가 private 라 접근이 불가하지만 setAccessible를 통해 접근가능하도록 해줄 수 있다.
		System.out.println("b.get(book) = " + b.get(book));
		b.set(book, "BBBBB");
		System.out.println("b.get(book) = " + b.get(book));


		Method c = Book.class.getDeclaredMethod("c");
		c.setAccessible(true);
		c.invoke(book);

		Method sum = Book.class.getDeclaredMethod("sum", int.class, int.class);
		int invoke = (int)sum.invoke(book, 1, 2);
		System.out.println("invoke = " + invoke);
	}
}
```

![image](https://user-images.githubusercontent.com/60100532/187655056-02bb3cc2-5689-4278-8bb2-a8bf28333f0e.png)

---

### * 나만의 DI 프레임 워크 만들기

```java


import java.lang.reflect.InvocationTargetException;
import java.util.Arrays;

public class ContainerService {

	public static <T> T getObject(Class<T> classType) {
		T instance = createInstance(classType);

		Arrays.stream(classType.getDeclaredFields()).forEach(f ->{
			if (f.getAnnotation(Inject.class) != null) {
				Object fieldInstance = createInstance(f.getType());
				f.setAccessible(true);
				try {
					f.set(instance, fieldInstance);
				} catch (IllegalAccessException e) {
					throw new RuntimeException(e);
				}
			}
		});

		return instance;
	}

	private static <T> T createInstance(Class<T> classType) {
		try {
			return classType.getConstructor(null).newInstance();
		} catch (InstantiationException | IllegalAccessException | InvocationTargetException | NoSuchMethodException e) {
			throw new RuntimeException(e);
		}
	}
}
```

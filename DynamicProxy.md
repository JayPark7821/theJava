# The Java

## 4. Dynamic Proxy
### * 스프링 데이터 JPA는 어떻게 동작하나?
스프링 데이터 JPA에서 인터페이스 타입의 인스턴스는 누가 만들어 주는 것인가?
> Spring AOP를 기반으로 동작하며 RepositoryFactorySupport에서 ProxyFactory를 사용하여 프록시를 생성한다.

![image](https://user-images.githubusercontent.com/60100532/187680175-46defbcc-0b60-44a8-93e1-49b8f1d4bca7.png)

___

### * 프록시 패턴
![image](https://user-images.githubusercontent.com/60100532/187680380-3cda4da0-088c-4d7d-bf0d-5ac9bbe655ae.png)
>* 리얼 서브젝트는 자신이 해야 할 일만 하면서(SRP) 프록시를 사용해 부가적인 기능(접근 제한, 로깅, 트랜잭션 등)을 제공할 때 주로 사용
>

### * 다이나믹 프록시 (인터페이스 기반)
```java

	BookService bookService = (BookService)Proxy.newProxyInstance(BookService.class.getClassLoader(),
		new Class[] {BookService.class},
		new InvocationHandler() {

			BookService bookService = new DefaultBookService();
			@Override
			public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
				System.out.println("aaaaaaaaa");
				Object invoke = method.invoke(bookService, args);
				System.out.println("bbbbb");
				return invoke;
			}
		});
```

### * 다이나믹 프록시 CGLIB
```java
	@Test
	public void di() throws Exception{
		MethodInterceptor handler = new MethodInterceptor(){
			BookService bookService = new BookService();
			@Override
			public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
				System.out.println("aaaa");
				Object invoke = method.invoke(bookService, args);
				System.out.println("bbbb");
				return invoke;
			}
		};
		BookService bookService = (BookService)Enhancer.create(BookService.class, handler);
		Book book = new Book();
		book.setTitle("spring");
		bookService.rent(book);
		bookService.returnBook(book);

	}

```


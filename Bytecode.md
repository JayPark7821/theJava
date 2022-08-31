# The Java

## 2. 바이트코드 조작
### * 모자에서 토끼를 꺼내는 마술
* 아무것도 없는 Moja에서 "Rabbit"을 꺼내는 마술

```java

public class Moja{
	 
}
```

```java

public class Masulsa{
	public static void main(String[] args) {
		System.out.println(new Moja().pullOut());
	}
}
```


바이트코드 조작 라이브러리
* ASM: https://asm.ow2.io/
* Javassist: https://www.javassist.org/
* ByteBuddy: https://bytebuddy.net/#/

```java

import static net.bytebuddy.matcher.ElementMatchers.*;

import java.io.File;
import java.io.IOException;

import net.bytebuddy.ByteBuddy;
import net.bytebuddy.implementation.FixedValue;

public class Masulsa {
	public static void main(String[] args) throws IOException {
		new ByteBuddy().redefine(Moja.class)
			.method(named("pullOut")).intercept(FixedValue.value("Rabbit!"))
			.make().saveIn(new File("C:\\Users\\carol\\Desktop\\HC\\front\\server\\theJavaPrac\\target\\classes\\"));
		System.out.println(new Moja().pullOut());
	}
}
```
![image](https://user-images.githubusercontent.com/60100532/187599368-35e9b76e-d116-449f-9f93-d63324bf940d.png) 
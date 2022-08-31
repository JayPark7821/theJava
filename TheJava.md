# The Java

## 1. Java, JVM, JDK, JRE 의 차이점 이해
![image](https://user-images.githubusercontent.com/60100532/187471798-b72136b2-9e82-482c-a768-05924cc696f8.png)

### 1. JVM ( Java Virtual Machine)
* 자바 가상 머신으로 자바 바이트 코드(.class파일)를 OS에 특화된 코드로 변환 하여 실행
* 바이트 코드를 실행하는 표준(JVM 자체는 표준)이자 구현체( 특정 밴더가 구현한 JVM 오라클, 아마존, Azul)  
* 특정 플랫폼에 종속적  

<br>

> 정리하면 
> * JVM은 자바 가상 머신
> * 바이트 코드를 어떻게 실행할 수 있는지에 대한 스팩 
> * 구현체 다양 
> * 플랫폼에 종속적 왜냐하면 자바 코드는 네이티브 코드로 바꿔서 실행해야하는데   
> 이 네이티브 코드라는것이 OS에 맞춰서 실행해야 하기 때문에

<br>  


### 2. JRE ( Java Runtime Environment ) 목적은 Java application을 실행하는것
* `자바 어플리케이션을 실행할 수 있도록` 구성된 배포판.
* JVM과 핵심 라이브러리 및 자바 런타임 환경에서 사용하는 프로퍼티 세팅이나 리소스 파일을 가지고 있다. (JVM + 라이브러리)
* 개발 관련 도구는 포함하지 않는다. (JDK 에서 제공)

<br>  

### 3. JDK ( Java Development Kit )
* JRE + 개발에 필요한 툴
* 소스코드를 작성할 때 사용하는 자바 언어는 플랫폼에 독립적.
* 오라클은 자바 11부터는 JDK만 제공하며 JRE를 따로 제공하지 않는다.

<br>  

### 4. Java 
* 프로그래밍 언어
* JDK에 들어있는 자바 컴파일러(javac)를 사용하여 바이트코드(.class파일)로 컴파일 가능

> JVM 언어
> * JVM 기반으로 동작하는 프로그래밍 언어
> * 클로져, 그루비, JRuby, Jython, Kotlin, Scala, ... 


---

<br>  


## 2. JVM 구조

![image](https://user-images.githubusercontent.com/60100532/187482829-3b58742a-e6d7-4731-a845-7f0274712c8a.png)

### 클래스 로더 시스템
* `.class에서 바이트 코드를 읽고` -> `메모리에 저장`
* 로딩: .class 파일에서 바이트 코드를 읽어오는 과정
* 링크: 레퍼런스를 연결하는 과정
* 초기화: static 값들 초기화 및 변수에 할당

 
### 메모리
* 메소드 영역에는 클래스 수준의 정보 ( 클래스 이름, 부모 클래스 이름, 메소드, 변수) 저장, 공유 자원
* 힙 영역에는 객체를 저장, 공유 자원
* 스택 영역에는 `쓰레드 마다 런타임 스택을 만들고`, 그 안에 메소드 호출을 스택프레임이라 부르는 블럭으로 쌓는다.  
쓰레드를 종료하면 런타임 스택도 사라진다.  (Error log stack trace)
* PC(Program Counter) 레지스터: 쓰레드 마다 쓰레드 내 현재 실행할 instruction 위치를 가리키는 포인터가 생성

### 실행 엔진
* 인터프리터 : 바이트 코드를 한줄 씩 실행.
* JIT 컴파일러 ( 바이트코드를 네이티브 코드로 컴파일) : 인터프리터 효율을 높이기 위해, 인터프리터가 반복되는 코드를 발견하면,  
JIT 컴파일러로 반복되는 코드를 모두 네이티브 코드로 바꿔둔다.   
그 다음부터 인터프리터는 네이티브 코드로 컴파일된 코드를 바로 사용
* GC( Garbage Collector) : 더이상 참조되지 않는 객체를 모아서 정리.

### JNI ( Java Native Interface)
* 자바 어플리케이션에서 C, C++, 어셈블리로 작성된 함수를 사용할 수 있는 방법 제공
* Native 키워드를 사용한 메소드 호출

![image](https://user-images.githubusercontent.com/60100532/187487226-f0e9a9cc-6533-48a3-a5bc-03538756c915.png)

 ## 2. 클래스 로더

![image](https://user-images.githubusercontent.com/60100532/187577401-c54b7e20-421c-4efa-a0db-481758e15ba8.png)

* 로딩, 링크, 초기화 순으로 진행됨.  
  

* 로딩
  * 클래스 로더가 .class 파일을 읽고 그 내용에 따라 적절한 바이너리 데이터를 만들고 메모리의 "Method"영역에 저장.
  * "Method" 영역에 저장하는 데이터 
    * FQCN ( Fully Qualified Class Name )
    * 클래스|인터페이스|이늄
    * 메소드와 변수
  * 로딩이 끝나면 해당 클래스 타입의 Class객체를 생서앟여 메모리 "heap"영역에 저장.  
    ![image](https://user-images.githubusercontent.com/60100532/187594361-f427c730-f83b-4440-b79c-6204f96b23b9.png)

* 링크
  * Verify, Prepare, Resolve(optional) 세 단계로 나눠져 있다.
  * Verify : .class 파일 형식이 유효한지 체크한다.
  * Prepare : 클래스 변수 ( static 변수)와 기본값에 필요한 메모리 
  * Resolve : 심볼릭 메모리 레퍼런스를 메소드 영역에 있는 실제 레퍼런스로 교체
   

* 초기화
  * Static 변수의 값을 할당함. (static 블럭이 있다면 이때 실행됨.) 
 


 

# Class 와 main 메서드

자바는 객체지향 프로그래밍 (Object-Oriented Programming, OOP) 언어입니다.

OOP 는 현실 세계의 모든 데이터를 모델링하여 프로그래밍하는 방법으로, 이를 위해 객체(Object) 라는 개념을 사용합니다.

Class 는 객체를 생성하기 위한 설계도로 같은 Class 로 생성된 인스턴스 객체는 같은 기능을 하게 됩니다.

```
public class Hello {
	public static class Print {
		public void ping() {
			System.out.println("pong");
		}
	}

	public static void main(String[] args) {
		Print p1 = new Print();
		Print p2 = new Print();

		p1.ping(); // 콘솔에 pong 출력
		p2.ping(); // 콘솔에 pong 출력
	}
}
```

## 자바 실행파일 만들기

자바 프로그래밍을 할때 모듈 파일이 아닌 실행파일은 main 클래스를 작성해야 합니다.

터미널에서 javac Hello.java 를 호출하면 자바 컴파일러가 Hello.class 라는 바이트코드를 생성합니다.

java Hello 를 입력하면 JVM 이 초기화 과정 (config, loader, ...) 을 거친 다음 

ClassLoader 가 Hello.class 파일을 로딩합니다.

\* Jar 파일을 실행했다면 manifest의 Main-Class 에서 찾아 로딩합니다.

진입점이 될 클래스를 찾았다면 클래스 내부에서 main 메서드를 찾습니다.

JVM 이 main 메서드를 찾으면 JVM은 실행시 전달된 파라미터를 String 배열로 만들어 main 메서드 매개변수로 호출합니다.

java 프로그램을 실행할때 클래스명 뒤에 공백으로 구분해서 여러 문자열 인자를 넘길 수 있습니다.

많은 예제가 public static void main(String[] args) 로 설명하지만 사실 변수 이름은 자유롭게 작성해도 실행에는 문제가 없습니다.

실행해야 하는 클래스는 접근제한자를 public 으로 작성해야 찾을 수 있습니다.

또한 인스턴스를 생성하지 않고 호출하기 때문에 static 메소드로 작성할 필요가 있습니다.

main 함수에서 반환해야 하는 값이 없어야 하기 때문에 void 입니다.

만약 main 메소드가 int 를 반환하도록 수정하면 error: 'main' method is not declared with a return type of 'void' 에러를 만날 수 있습니다.

그럼 비정상 종료이며 반환값으로 비교하려면 자바에서는 어떻게 해야 하나요?

System.exit(code); 를 호출해서 원하는 코드로 프로세스를 종료할 수 있습니다.

아래 코드를 한번 실행해 봅시다.

**코드**
```
import java.util.Arrays;

public class Hello {
	public static void main(String[] arguments) {
		System.out.println(arguments);
		System.out.println(Arrays.toString(arguments));
		System.out.println(System.getProperty("profile"));
		System.out.println(new Hello());
	}
}
```

**실행**
```
java -Dprofile=prod Hello token1 token2 token3
```

**출력**
```
[Ljava.lang.String;@182decdb
[token1, token2, token3]
prod
Hello@7637f22
```

처음 System.out.println(arguments); 를 만나면 [Ljava.lang.String;@182decdb 를 출력합니다.

대부분 객체를 문자열로 변환할때 toString 메서드를 호출합니다.

우리는 toString 을 정의하지 않았는데 어떻게 호출하고 있을까요?

모든 Java 클래스는 별도로 상속(extends) 를 지정하지 않아도 Object 클래스를 상속받습니다.

(생성자 또한 마찬가지로 생략시 기본 생성자가 하나 생성됩니다)

이는 String.toString 를 호출하지 않고 Object.toString 을 호출하기 때문입니다.

그럼 배열의 내용을 보려면 매번 반복문을 돌려야 할까요?

자바 표준 API 중에 Arrays 클래스는 배열 관련 여러 유틸을 제공합니다.

그중 Arrays.toString 은 배열 아이템의 toString 을 호출해 배열 내부 Object 의 정보를 볼 수 있습니다.

우리가 클래스 뒤에 작성한 "token1 token2 token3" 가 배열에 담긴것을 알 수 있습니다.

잠깐! System.out.println 는 import 없이 사용 가능했는데 왜 Arrays 는 import 가 필요할까요?

이유는 java.lang.Object 를 기본 상속받는것처럼 java.lang 패키지의 모든 클래스는 별도의 import 선언 없이 사용할 수 있습니다.

생략 없이 작성한다면 java.lang.System.out.print(new Hello()); 로 작성할 수 있습니다.

그러면 클래스명 앞에 작성한 "-Dprofile=prod" 도 궁금할 수 있습니다.

이는 실행 시 시스템 속성을 설정할 수 있습니다.

profile 이라는 속성에 prod 라는 값을 지정했고 System.getProperty("profile") 를 통해 읽어 올 수 있습니다.

마지막 라인에 new Hello 를 출력하는데 Hello@7637f22 값은 무엇일까요?

Object.toString 은 클래스 이름과 hashcode 가 반환하는 값으로 메모리의 가상 주소를 반환해 고유한 값을 가집니다.

**코드**
```
public class Hello {
	public static void main(String[] arguments) {
		System.out.println(new Hello());

		java.lang.System.out.print(new Hello());
	}

	@Override
	public int hashCode() {
		return 0;
	}

}
```
**출력**
```
Hello@0
```

hashCode 를 Override(재정의) 하면 toString 값도 @0 으로 변경되는것을 확인 할 수 있습니다.

**코드**
```
public class Hello {
	public static void main(String[] arguments) {
		System.out.println(new Hello());
	}

	@Override
	public int hashCode() {
		return 0;
	}

	public String toString() {
		return getClass().getSimpleName() + " {}";
	}
}
```
**출력**
```
Hello {}
```

toString 을 수정해서 원하는 포맷으로 바꿀 수 있습니다.

그런데 왜 hashCode 에는 @Override 어노테이션을 포함하고 toString 에는 @Override 어노테이션을 추가하지 않았지만 문제없이 동작할까요?

@Override 어노테이션이 없어도 메소드를 재정의하는데 문제 없습니다.

**코드**
```
public class Hello {
	public static void main(String[] arguments) {
		System.out.println(new Hello());
		System.out.println(new Hello2());
	}

	public String getName() {
		return getClass().getSimpleName();
	}

	public String toString() {
		return getName() + " {}";
	}
}

public class Hello2 extends Hello {
	public String getName() {
		return "customName";
	}
}
```
**출력**
```
Hello {}
customName {}
```

그런데 만약 Override 했던 메소드의 상위 클래스가 이름이나 매개변수를 변경하면 어떻게 될까요?

**코드**
```
public class Hello {
	public static void main(String[] arguments) {
		System.out.println(new Hello());
		System.out.println(new Hello2());
	}

	public String getSimpleName(Class clazz) {
		return clazz.getSimpleName();
	}

	public String toString() {
		return getSimpleName(this.getClass()) + " {}";
	}
}
```
**출력**
```
Hello {}
Hello2 {}
```

Override 했던 메서드가 변경되었음에도 문제없이 실행되고 상위 메서드에서 정의한 메서드 그대로 호출되는것을 알 수 있습니다.

그럼 @Override 메서드를 추가하면 어떤 변화가 있을까요?

error: method does not override or implement a method from a supertype

에러를 통해 재정의할 함수가 없다는 것을 알수 있어 이런 문제를 방지할 수 있습니다.


# String, StringBuffer, StringBuilder

Java 프로그래밍을 하면서 문자열은 자주 다루게 됩니다.

**코드**
```
public class Main {
	public static void main(String[] args) {
		String hello = "Hello";
		boolean strEquals = hello == "Hello";
		System.out.println("Equals => " + strEquals);
	}
}
```
**출력**
`Equals => true`

위의 코드를 실행해보면 true 를 반환합니다.

```
public class Main {
	public static void main(String[] args) {
		String hello = "Hello";
		String hello2 = "Hello";

		System.out.printf("hello => %s\n", hello);
		System.out.printf("hello2 => %s\n", hello2);

		boolean strEquals = hello == hello2 + "";
		System.out.println("Equals => " + strEquals);
	}
}
```
**출력**
```
hello => Hello
hello2 => Hello
Equals => false
```

하지만 위 코드도 동일한 값 ("Hello") 이지만 false 를 반환합니다.

String은 객체이므로 "값" 이 아니라 가상 메모리 "주소"를 변수에 저장합니다.

그리고 == 기호는 객체일때 가상 메모리 주소를 비교합니다.

문자열 리터럴 (") 를 사용해서 작성했을때는 같은 문자일 경우 string constant pool 이라는 영역에 저장되어 재사용됩니다.

따라서 처음 "Hello" === "Hello" 는 String Pool 영역에서 가져오기 때문에 동일한 값을 참조합니다.

그럼 두번째도 같은 단어인데 왜 false 를 반환할까 의문을 가질 수 있습니다.

String 은 불변 객체(Immutable Object)입니다.

불변 객체란 생성 후 상태를 바꿀 수 없는 객체를 의미합니다.

다른 데이터 타입에서 연산자(operator) 를 사용해 값이 변경되고 변수값이 업데이트 되는것을 보면 원본 데이터가 변경된 것으로 보일 수 있지만

String 에서 + 연산자로 다른 문자를 결합할 경우 새로운 String 객체가 생성되며 GC가 실행되기 전까지 메모리에는 기존 String 객체와 새로운 String 객체가 모두 존재하게 됩니다.
 
따라서 "Hello" 를 통해 String Pool 에 생성된 문자에 더하기 연산을 사용하면 "새로운" String 객체가 생성되고 이로인해 새로운 객체에 대한 주소를 참조해 false 를 반환하게 됩니다.

**코드**
```
public class Main {
	public static void main(String[] args) {
		String hello = new String("Hello");
		String hello2 = new String("Hello");

		System.out.printf("hello => %s\n", hello);
		System.out.printf("hello2 => %s\n", hello2);

		boolean strEquals = hello == hello2;
		System.out.println("Equals => " + strEquals);
	}
}
```
**출력**
```
hello => Hello
hello2 => Hello
Equals => false
```

같은 문자열이라도 연산자(+)를 통해 새로운 String 객체를 생성하거나 new 연산자를 사용해 String 객체를 생성해도 다른 값을 참조하는것을 알 수 있습니다.

String 처럼 객체간에 동일한지 비교할때는 == 연산자가 아닌 equals 함수를 사용합니다.

데이터를 다루는 대부분 객체는 최상위 클래스인 Object에서 받은 toString, equals, hashCode 를 구현하고 있습니다.

String 또한 내부에 있는 char 배열을 비교하는 코드가 equals 메소드에 구현되어 있어 같은 문자열을 가지면 true 를 반환합니다.

String 객체가 있는데 StringBuffer, StringBuilder 가 있는 이유는 위에 설명했듯 String 은 불변 객체입니다.

예를들어 0 ~ 9 까지 문자열을 순차적으로 더하는 반복문을 사용할 경우 String 객체가 Heap Memory 에 10개 [0, 01, 012, 0123, 01234, 012345, 0123456, 01234567, 012345678, 0123456789] 생성되고 이는 GC 를 더 자주 발생시키는 원인이 됩니다.

그럼 StringBuilfer 를 쓰면 차이점이 무엇일까.

String 은 내부에 char 배열을 가지고 있습니다.

StringBuffer는 내부에 있는 char 배열을 조작합니다.

예를들어 미리 16칸 정도의 char 배열을 만들고 문자열이 추가될때마다 char 배열의 해당 인덱스 값만 갱신합니다.

그리고 문자열 길이가 기존 배열 크기를 초과할 경우 적절한 크기를 계산해서 새로운 배열을 만들고 기존 데이터를 복사합니다.

반대로 배열 길이가 큰 상태에서 문자열이 일정 크기 이상 작아지면 적절한 크기를 계산해서 새로운 배열을 만들고 기존 데이터를 복사함으로 메모리를 절약합니다.

이런식으로 내부적인 로직으로 적절한 메모리 사용과 GC 를 최소화해 사용할 수 있습니다.

그럼 StringBuilder와 StringBuffer의 차이점은 무엇인가.

둘다 내부에 있는 char 배열을 조작하지만 StringBuilder는 동기화를 보장하지 않고 StringBuffer는 보장합니다.

하나의 쓰레드 안에서만 실행된다면 동기화 로직이 없는 StringBuffer가 더 빠른 성능을 가질 수 있습니다.

하지만 여러 쓰레드 (멀티쓰레드)에서 객체를 공유하며 문자열이 추가될 경우 동시에 접근하면서 기존 작업에 덮어씌워질 수 있습니다

**코드**
```
public class Main implements Runnable {
	private final static StringBuffer buffer = new StringBuffer();
	private final static StringBuilder builder = new StringBuilder();

	public static void main(String[] args) {
		Main main = new Main();
		Thread[] threads = new Thread[50];

		for (int i = 0; i < threads.length; i++) {
			threads[i] = new Thread(main);
			threads[i].start();
		}

		Thread print = new PrintThread();
		print.start();
	}

	@Override
	public void run() {
		for (int i = 0; i < 100; i++) {
			buffer.append("0123456789");
			builder.append("0123456789");
		}
	}

	public static class PrintThread extends Thread {
		@Override
		public void run() {
			try {
				Thread.sleep(1000);
				System.out.println("buffer => " + buffer.length());
				System.out.println("builder => " + builder.length());
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
}
```
**결과**
```
buffer => 50000
builder => 49920
```

동일한 append 메소드를 호출하고 멀티 쓰레드에서 호출하지만 StringBuffer는 실행 횟수만큼 정확히 추가되고 StringBuilder는 일부 문자열이 덮어씌워진 것을 알 수 있습니다.

(Thread 가 완전히 끝난 다음 문자열 크기를 확인해야 합니다)
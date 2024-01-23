# Interface, Annotation, Reflection

## Interface (인터페이스)
자바에서 인터페이스란 구현을 작성하지 않고 추상 메서드만을 정의합니다.

또한 단일 상속만 가능한 클래스와 다르게 구현이 없는 인터페이스는 다중 상속이 가능합니다.

**코드**
```
public interface MyFile {
	/** 파일 삭제 */
	public void delete();

	/** 파일 이름 */
	public String getFileName();

	/** 파일 확장자 */
	public String getExtName();
}

public interface MyTextFile extends MyFile {
	/** 텍스트 파일 문자열 읽기 */
	public String readText();
}

public interface MyAudioFile extends MyFile {
	/** 오디오 재생 */
	public void play();
}

public class MyFileImpl implements MyFile {
	private String fileName;

	public MyFileImpl(String fileName) {
		this.fileName = fileName;
	}

	@Override
	public void delete() {
		System.out.println("TOOD Delete");
	}

	@Override
	public String getFileName() {
		return this.fileName;
	}

	@Override
	public String getExtName() {
		String token[] = this.fileName.split("\\.");
		return token[Math.min(token.length - 1, 0)];
	}
}

public class MyAudioFileImpl extends MyFileImpl implements MyAudioFile {

	public MyAudioFileImpl(String fileName) {
		super(fileName);
	}

	@Override
	public void play() {
		System.out.println("Play Audio");
	}
}

public class MyTextFileImpl extends MyFileImpl implements MyTextFile {

	public MyTextFileImpl(String fileName) {
		super(fileName);
	}

	@Override
	public String readText() {
		return "TEXT DATA, TEXT DATA, TEXT DATA";
	}

}

import java.util.Objects;

public class Main {
	public static void main(String[] args) {
		MyFile audioFile = new MyAudioFileImpl("audio.mp3");
		MyFile textFile = new MyTextFileImpl("source.txt");

		execute(audioFile);
		execute(textFile);
	}

	public static void execute(MyFile myfile) {

		System.out.println("FileName => " + myfile.getFileName());

		if (Objects.equals(myfile.getExtName(), "mp3")) {
			MyAudioFile audioFile = (MyAudioFile) myfile;
			audioFile.play();
		}

		if (Objects.equals(myfile.getExtName(), "txt")) {
			MyTextFile textFile = (MyTextFile) myfile;
			System.out.println("READ => " + textFile.readText());
		}

		myfile.delete();
	}
}
```

**출력**
```
FileName => audio.mp3
TOOD Delete
FileName => source.txt
TOOD Delete
```

상위 인터페이스 MyFile 에 선언된 (getFileName, getExtName) 메소드는 상위 인터페이스에 선언해 서로 다른 구현체라도 별도의 분기없이 호출할 수 있으며

(위 코드예시에서는 상속을 했지만 완전히 별도로 구현해도 문제없이 실행할 수 있습니다)

예를들어 DB를 사용한 데이터관리와 File System 을 사용한 데이터관리 모두 동일한 인터페이스를 상속해 구현했다면 생성 인스턴스만 교체해 동작을 변경할 수 있습니다.

## Class.forName

DB 예제들을 보면 Class.forName 으로 JDBC 초기화 하는 코드를 자주 볼 수 있습니다.

Class.forName 으로 클래스 이름을 매개변수로 전달하면 ClssLoader 가 해당 클래스를 찾아 초기화 하게 되며

초기화시 static 블럭에서 DriverManager 에 자신의 인스턴스를 생성해 등록하게 됩니다.

아래는 동작을 단순하게 구현한 예시 입니다.

Class.forName으로 UserRepository라는 공통 인터페이스를 구현한 클래스를 로드하게 되면 static 블럭에서 UserManager 의 static 변수에 초기화됩니다.

UserRepository 라는 추상 메소드를 구현한 클래스이지만 로드되는 클래스에 따라 다른 동작을 하게 됩니다.

**코드**
```
public class UserManager {
	private static UserRepository repository = null;

	public static synchronized void initRepository(UserRepository repository) {
		UserManager.repository = repository;
	}

	public static UserRepository getUserRepository() {
		if (UserManager.repository == null) {
			throw new RuntimeException("Repository 를 초기화 해주세요");
		}
		return UserManager.repository;
	}
}

public interface UserRepository {
	public int insert(UserDto dto);
	public void delete(UserDto dto);
	public void update(UserDto dto);
	public void select(UserDto dto);
}

public class UserFileRepository implements UserRepository {
	static {
		UserManager.initRepository(new UserFileRepository());
	}

	@Override
	public int insert(UserDto dto) {
		System.out.println(this.getClass() + "insert" + dto);
		return 0;
	}

	@Override
	public void delete(UserDto dto) {
		System.out.println(this.getClass() + "delete" + dto);
	}

	@Override
	public void update(UserDto dto) {
		System.out.println(this.getClass() + "update" + dto);
	}

	@Override
	public void select(UserDto dto) {
		System.out.println(this.getClass() + "select" + dto);
	}
}

public class UserDBRepository implements UserRepository {
	static {
		UserManager.initRepository(new UserFileRepository());
	}

	@Override
	public int insert(UserDto dto) {
		System.out.println(this.getClass() + "insert" + dto);
		return 0;
	}

	@Override
	public void delete(UserDto dto) {
		System.out.println(this.getClass() + "delete" + dto);
	}

	@Override
	public void update(UserDto dto) {
		System.out.println(this.getClass() + "update" + dto);
	}

	@Override
	public void select(UserDto dto) {
		System.out.println(this.getClass() + "select" + dto);
	}
}

public class UserDto {
	private int pk = -1;
	private String name;

	public UserDto(int pk, String name) {
		this.pk = pk;
		this.name = name;
	}

	public UserDto(int pk) {
		this.pk = pk;
	}

	public UserDto(String name) {
		this.name = name;
	}

	@Override
	public String toString() {
		return "UserDto [pk=" + pk + ", name=" + name + "]";
	}
}

public class Main {
	public static void main(String[] args) throws ClassNotFoundException {

		Class.forName("UserFileRepository");
		UserRepository repository = UserManager.getUserRepository();

		int pk = repository.insert(new UserDto("테스트"));
		repository.delete(new UserDto("테스트"));
		repository.update(new UserDto("테스트"));
		repository.select(new UserDto(pk));
	}
}
```

**출력**
```
class UserFileRepositoryinsertUserDto [pk=-1, name=테스트]
class UserFileRepositorydeleteUserDto [pk=-1, name=테스트]
class UserFileRepositoryupdateUserDto [pk=-1, name=테스트]
class UserFileRepositoryselectUserDto [pk=0, name=null]
```

## Annotation (어노테이션)

## Reflection (리플렉션)

## AbstractProcessor
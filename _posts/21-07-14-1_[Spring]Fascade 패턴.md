# 디자인패턴6-퍼사드 패턴 Fascade Pattern

- 구조 패턴
- Fascade = "건물의 앞쪽 정면" ▶️ 건물 뒷쪽에 어떤 것이 있는지는 알 수 없는 상태
- 여러 개의 객체와 실제 사용하는 서브 객체 사이에 복잡한 의존관계가 있을 때, 중간에 fascade라는 객체를 두고 여기에서 제공하는 인터페이스만을 활용하여 기능을 사용하는 방식
- fascade는 자신이 갖고 있는 각 클래스의 기능을 명확히 알아야 함

먼저, 퍼사드가 존재하지 않을 때 클라이언트측에서 관리를 하는 경우를 살펴보자

FTP 서버를 이용해서 파일을 생성하고 읽는 경우를 빗대어 접근해보자

먼저 

- 호스트와 파일 경로, 포트번호를 필요로 하는 FTP서버
- 파일명을 이용해서 파일을 생성하고 읽는 Wrtier, Reader

가 있다고 생각해보고 각각을 만들어보자

```java
package com.designPattern.structure.fascade;

public class FTP {
    private String host;
    private int port;
    private String path;

    //host가 누구인지, 포트정보와 통로는 어떻게 설정될 지 필요함!
    public FTP(String host, int port, String path){
        this.host = host;
        this.port =port;
        this.path = path;
    }

    public void connect(){
        System.out.println("FTP host :" +host+", port:  "+port+"로 연결합니다");
    }

    public void moveDirectory(){
        System.out.println("path: "+path+"로 이동합니다");
    }

    public void disconnect(){
        System.out.println("FTP연결을 종료합니다");
    }

}
```

```java
package com.designPattern.structure.fascade;

public class Writer {
    String fileName;

    public Writer(String fileName){
        this.fileName = fileName;
    }

    public void fileConnect(){
        String msg = String.format("Writer %s로 연결합니다",fileName);
        System.out.println(msg);
    }
    public void write(){
        String msg = String.format("Writer %s로 내용을 출력합니다",fileName);
        System.out.println(msg);
    }

    public void fileDisconnect(){
        String msg = String.format("Writer %s의 연결을 종료합니다",fileName);
        System.out.println(msg);
    }

}
```

```java
package com.designPattern.structure.fascade;

public class Reader {
    private String fileName;

    public Reader(String fileName){
        this.fileName= fileName;
    }
    public void fileConnect(){
        String msg = String.format("Reader %s로 연결합니다",fileName);
        System.out.println(msg);
    }
    public void fileRead(){
        String msg = String.format("Reader %s의 내용을 읽어옵니다",fileName);
        System.out.println(msg);
    }

    public void fileDisconnect(){
        String msg = String.format("Reader %s로 연결 종료합니다",fileName);
        System.out.println(msg);
    }

}
```

그리고 이를 클라이언트에서 접근한다면

```java
package com.designPattern.structure.fascade;

public class FascadeMain {
    public static void main(String[] args){
        FTP ftpClient = new FTP("http://www.naver.com",22,"/home/etc");
        Reader reader = new Reader("test.txt");
        Writer writer = new Writer("test.txt");

        ftpClient.connect();//FTP host :http://www.naver.com, port:  22로 연결합니다
        ftpClient.moveDirectory();//path: /home/etc로 이동합니다

        writer.fileConnect();//Writer test.txt로 연결합니다
        writer.write();//Writer test.txt로 내용을 출력합니다

        reader.fileConnect();//Reader test.txt로 연결합니다
        reader.fileRead();//Reader test.txt의 내용을 읽어옵니다

        //파일을 만들고 읽기까지 다 했으므로 연결 종료하기
        reader.fileDisconnect();//Reader test.txt로 연결 종료합니다
        writer.fileDisconnect();//Writer test.txt의 연결을 종료합니다
        ftpClient.disconnect();//FTP연결을 종료합니다

    }
}
```

파사드가 없는 상태에서는 각각에 대한 메서드를 이용해서 접근하게 된다

하지만 파사드는

- 위에서 필요한 기능을 파사드 클래스 내부에서 메서드로 관리해서 묶어줌으로써 객체에 대한 의존성을 파사드객체가 가져가게 된다!
- 더불어 새로운 기능적 인터페이스로써 메서드를 제공하게 된다!(아래에서는 connect, disconnect, read, write)

와 같이 기능적으로 같이 운영될 필요가 있는 여러 객체가 존재할 때, 이를 하나의 객체에서 기능적 관리를 하는 역할을 담당한다!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4/%EA%B5%AC%EC%A1%B0%ED%8C%A8%ED%84%B4/Fascade%ED%8C%A8%ED%84%B4/%ED%8C%8C%EC%82%AC%EB%93%9C%ED%8C%A8%ED%84%B4.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4/%EA%B5%AC%EC%A1%B0%ED%8C%A8%ED%84%B4/Fascade%ED%8C%A8%ED%84%B4/%ED%8C%8C%EC%82%AC%EB%93%9C%ED%8C%A8%ED%84%B4.png?raw=true)

파사드 패턴

위의 객체들을 접근할 수 있는 파사드 객체를 만들어보자

```java
package com.designPattern.structure.fascade;

public class Fascade {
    private FTP ftp;
    private Reader reader;
    private Writer writer;

    public Fascade(FTP ftp , Reader reader, Writer writer){
        this.ftp = ftp;
        this.reader = reader;
        this.writer = writer;
    }
    //오버로딩
    public Fascade(String host, int port, String path, String fileName){
        this.ftp = new FTP(host,port,path);
        this.reader = new Reader(fileName);
        this.writer = new Writer(fileName);
    }

    public void connect(){
        ftp.connect();
        ftp.moveDirectory();
        writer.fileConnect();
        reader.fileConnect();
    }

    public void disconnect(){
        reader.fileDisconnect();
        writer.fileDisconnect();
        ftp.disconnect();
    }

    public void read(){
        reader.fileRead();
    }

    public void write(){
        writer.write();
    }

}
```

위에서 클라이언트(메인)에서 사용된 기능적 단위를 묶는다면,

1. 연결- ftp연결, 경로로 이동, writer 연결, reader 연결
2. 연결해제-reader 연결해제, writer 연결해제, ftp연결 해제
3. reader를 이용해서 읽기
4. writer를 이용해서 쓰기

가 있었다

따라서 각각은 위와 같이 connect(), disconnect(), read(), write()메서드 처럼 묶여질 수 있을 것이다

이를 다시 클라이언트에서 확인해보자

```java
package com.designPattern.structure.fascade;

public class FascadeMain {
    public static void main(String[] args){
        
        Fascade fascade = new Fascade("http://www.naver.com",22,"/home/etc","test2.txt");
        fascade.connect();
        fascade.write();
        fascade.read();
        fascade.disconnect();
        /*
        * FTP host :http://www.naver.com, port:  22로 연결합니다
path: /home/etc로 이동합니다
Writer test2.txt로 연결합니다
Reader test2.txt로 연결합니다
Writer test2.txt로 내용을 출력합니다
Reader test2.txt의 내용을 읽어옵니다
Reader test2.txt로 연결 종료합니다
Writer test2.txt의 연결을 종료합니다
FTP연결을 종료합니다
        *
        * */
    }
}
```

그러면 위에서 확인한 것과 동일하게 작동됨을 확인해볼 수 있다

다만 차이점은 퍼사드를 통해서

- **공통작업**에 대해서 간편한 메서드를 제공함으로써 지금은 하나의 클래스뿐이지만, 라이브러리를 사용하기 쉽게 해줄 수 있다
- **코드의 가독성**을 높여준다
- 퍼사드 바깥의 객체를 이용하기 때문에, **퍼사드 외부의 객체가 퍼사드를 기반으로 한 라이브러리나 클래스에 의존하는 일을 감소시켜준다**

라는 효율성 증대의 측면을 높여줄 수 있다는 점이다!

그리고 이러한 퍼사드패턴을 통해서 퍼사드 객체는 정면만 바라볼 수 있게 된다!

그럼과 동시에 기능은 퍼사드가 제공할 수 있게 된다!

[퍼사드 패턴 - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/%ED%8D%BC%EC%82%AC%EB%93%9C_%ED%8C%A8%ED%84%B4#:~:text=%ED%8D%BC%EC%82%AC%EB%93%9C(%ED%94%84%EB%9E%91%EC%8A%A4%EC%96%B4%3A%20fa%C3%A7ade%5B,%EC%9D%98%20%EC%A0%95%EB%A9%B4%22%EC%9D%84%20%EC%9D%98%EB%AF%B8%ED%95%9C%EB%8B%A4.)
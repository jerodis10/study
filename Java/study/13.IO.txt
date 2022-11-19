13.IO
	13.1 입출력이란?
	13.2 바이트 기반 스트림 - InputStream, OutputStream
	13.3 보조 스트림
	13.4 문자기반 스트림
	13.5 NIO (New Input/Output)
	13.6 표준 입출력 - System.in, System.out, System.err
	13.7 직렬화
	13.8 파일 읽고 쓰기
	
	
	13.1 입출력이란?
		- 입출력은 컴퓨터 내부 또는 외부 장치와 프로그램간의 데이터를 주고 받는 것을 말한다.
		- 스트림(stream)
			- 자바에서 어느 한 쪽에서 다른 쪽으로 데이터를 전달하려면, 두 대상을 연결하고 데이터를 전송할 수 있는 역할을 수행한다.
		  	- 스트림이란 데이터를 운반하는데 사용되는 연결 통로이다.
		  	- 스트림은 연속적인 데이터의 흐름을 물에 비유해서 붙여진 이름이다. 
		  	  따라서 물이 한 쪽 방향으로만 흐르는 것과 같이 스트림은 단방향통신만 가능하다.
		  	  즉, 하나의 스트림으로 입력과 출력을 동시에 처리할 수 없다.
		  	  입력과 출력을 동시에 처리하기 위해서는 입력을 위한 입력 스트림과 출력을 위한 출력 스트림, 2개가 필요하다.
		  	- 스트림은 먼저 보낸 데이터를 먼저 받게 되어 있으며, 중간에 건너뜀 없이 연속적으로 데이터를 주고 받는다. 
		  	- 여러개의 데이터를 보낼시 큐와 같은 FIFO 구조로 되어 있다고 생각하면 된다.
		  	
	13.2 바이트 기반 스트림 - InputStream, OutputStream	
		- 스트림은 바이트단위로 데이터를 전송하며 입출력 대상에 따라서 다음과 같은 입출력 스트림이 있다.
			- FileInputStream		FileOutputStream		파일
			- ByteArrayInputStream	ByteArrayOutputStream	메모리(byte배열)
			- PipedInputStream		PipedOuputStream		프로세스(프로세스간 통신)
			- AudioInputStream		AudioOutputStream		오디오 장치
			
			- 위 입출력 스트림은 각각 InputStream과 OutputStream의 자손들이며, 
			  각각 읽고 쓰는데 필요한 추상메서드를 자신에 맞게 구현해놓은 구현체이다.
		
		- 입출력스트림의 부모 InputStream, OutputStream
			- InputStream의 read()와 OutputStream의 write(int b)는 입출력의 대상에 따라 읽고 쓰는 방법이 다를 것이기 
			  때문에, 각 상황에 알맞게 구현하라는 의미의 추상 메서드로 정의되어 있다. read()와 wirte(int b)를 제외한 나머지 
			  메서드들은 추상메서드가 아니니까 굳이 추상메서드인 read()와 write(int b)를 구현하지 않아도 이들을 사용하면 될 것이라 
			  생각할 수 있겠지만, 사실 추상메서드인 read()와 write(int b)를 이용해서 구현한 것들임으로 
			  read()와 write(int b)가 구현되어 있지 않으면 이들은 아무런 의미가 없다.	  
			  
	13.3 보조 스트림		  
		- 스트림의 기능을 보완하기 위해 보조스트림이라는 것이 제공된다.
		- 보조스트림은 실제 데이터를 주고받는 스트림이 아니기 때문에 데이터를 입출력할 수 있는 기능은 없지만, 
		  스트림의 기능을 향상시키거나 새로운 기능을 추가할 수 있다.
		  즉, 스트림을 먼저 생성한 다음에 이를 이용해 보조스트림을 생성해서 활용한다.
		  
		- Buffer를 사용하면 좋은 이유  
		  모아서 보내면 왜 빨라질까?
		  한 바이트씩 바로바로 보내는 것이 아니라 버퍼에 담았다가 한번에 모아서 보내는 방법인데 왜 이렇게 하는것이 빠를까?
	      입출력 횟수가 포인트이다.
		  단순히 모아서 보낸다고 이점이 있는 것이 아니다 -> 시스템 콜의 횟수가 줄어들었기 때문에 성능상 이점이 생기는 것이다.
		  OS 레벨에 있는 시스템 콜의 횟수 자체를 줄이기 때문에 성능이 빨라지는 것이다.
		  
		- 예) test.txt라는 파일을 읽기 위해 FileInputStream을 사용할 때, 입력 성능을 향상시키기 위해 버퍼를 사용하는 
		     보조스트림인 BufferedInputStream을 사용할 수 있다.    		  
			  
			//기반 스트림 생성
			FileInputStream fileInputStream = new FileInputStream("test.txt");
			
			//기반 스트림을 이용해 보조 스트림을 생성
			BufferedInputStream bufferedInputStream = new BufferedInputStream(fileInputStream);
			
			// Buffered**Stream 생성 시 사이즈도 정의하여 생성 가능(2번째 파라미터)
			// default : 8192
			BufferedInputStream bis = new BufferedInputStream(fileInputStream, 8192);
			
			// 보조스트림을 이용해 데이터를 읽는다.
			bufferedInpuStream.read(); 
			
			코드만 보았을 때 보조스트림은 BufferedInputStream이 입력 기능을 수행하는 것처럼 보이지만, 
			실제 입력 기능은 BufferedInputStream과 연결된 FileInputStream이 수행한다.
			
			BufferedInputStream은 버퍼만 제공한다. 버퍼를 사용한 입출력과 사용하지 않은 입출력은 성능상 상당한 차이가 나기 때문에 
			대부분 버퍼를 이용한 보조스트림을 사용하게 된다.
			
		- 예2) 알고리즘 문제 풀때 Scanner방식보다 BufferedReader 방식을 사용하는 주된이유
			- 버퍼를 사용하지 않은 입력 
				- 키보드의 입력이 키를 누르는 즉시 바로 전달됨
			- 버퍼를 사용한 입력
				- 키보드의 입력이 있을 때마다 한 문자씩 버퍼로 전송
				- 버퍼가 가득차거나, 개행문자가 나타나면 버퍼의 내용을 한 번에 전송
			
		- 보조스트림을 이용해 조립하는 것이 가능하다.
			
		- java.io - 데코레이터 패턴
			- 자기자신의 타입을 감싸는 패턴이라고 보면 된다.
			- 데코레이터 패턴
			  객체에 추가적인 요건을 동적으로 첨가한다. 데코레이터는 서브클래스를 만드는 것을 통해서 기능을 
			  유연하게 확장할 수 있는 방법을 제공한다.  	
			- java.io 패키지는 데코레이터 패턴으로 만들어졌다.
			- 데코레이터 패턴이란, A 클래스에서 B 클래스를 생성자로 받아와서, B 클래스에 추가적인 기능을 덧붙여서 제공하는 패턴이다.
			
			- BufferedReader는 Reader의 하위 클래스 중 하나를 받아와서, 버퍼를 이용한 기능을 추가한 기능을 제공한다.
			- BufferedReader 처럼 출력을 담당하는 래퍼 클래스는 출력을 하는 주체가 아니라 도와주는 역할이다.
			- Stream을 사용한 클래스들에서 이렇게 도와주는 클래스들을 보조스트림이라 한다.
			  
		- 보조스트림 종류
			- FilterInputStream		FilterOutputStream		필터를 이용한 입출력 처리
			- BufferedInputStream	BufferedOutputStream	버퍼를 이용한 입출력 성능향상	  
			- DataInputStream		DataOutputStream		int, float와 같은 primitive Type으로 데이터 처리
			- SequenceInputStream	없음						두 개의 스트림을 하나로 연결
			- LineNumberInputStream	없음						읽어온 데이터의 라인번호를 카운트
			- ObjectInputStream		ObjectOuputStream		데이터를 객체단위로 읽고 쓰는 데 사용	
															주로 파일을 이용하며 객체 직렬화와 관련
			- 없음					PrintStream				버퍼를 이용하며, 추가적인 print 관련 기능
															(print, printf, println 메서드)
			- PushbackInputStream	없음						버퍼를 이용해서 읽어온 데이터를 다시 되돌리는 기능
															(unread, push back to buffer)
															
			- 지금까지 알아본 스트림은 모두 바이트 기반의 스트림이다.
			- 바이트기반이라 하는 것은 입출력의 단위가 1byte라는 의미이다.
			- but Java에서는 한 문자를 의미하는 char형이 1byte가 아니라 2byte이기 때문에 
			  바이트기반의 스트림으로 2byte인 문자를 처리하는데 어려움이 있다.
			  
	13.4 문자기반 스트림
		- 바이트기반의 입출력 스트림의 단점(1byte -> 2byte)을 보완하기 위해 문자기반의 스트림을 제공한다.
		  InputStream -> Reader
		  OutputStream -> Writer	
		  
		- 바이트기반과 문자기반 스트림 비교
			바이트기반 스트림							문자기반 스트림
			- FileInputStream						FileReader
			- FileOutputStream						FileWriter
			- ByteArrayInputStream					CharArrayReader
			- ByteArrayOutputStream					CharArrayWriter
			- PipedInputStream						PipedReader
			- PipedOutputStream						PipedWriter
			- StringBufferInputStream(deprecated)	StringReader 
			- StringBufferOututStream(deprecated)	StringWriter 	
			
		- 보조스트림 역시 문자기반 보조스트림이 존재하며 사용목적과 방식은 바이트 기반 보조스트림과 같다.
			
	13.5 NIO (New Input/Output)
		- 기존 IO의 단점을 개선하기 위해 java4부터 추가된 패키지이다(java.io)
		
		- IO와 NIO의 차이점
		  	- IO와 NIO는 데이터를 입출력한다는 목적은 동일하지만, 방식에서 큰 차이가 나타난다.
		  
			- IO
				- 입출력 방식: 스트림
				- 버퍼 방식: non-buffer
				- 비동기 방식: 지원안함
				- 블로킹 방식: 블로킹 방식만 지원 (동기)	 	  
			- NIO
				- 입출력 방식: 채널
				- 버퍼 방식: buffer
				- 비동기 방식: 지원
				- 블로킹 방식: 블로킹/넌블로킹 방식 모두 지원 (동기/비동기 모두 지원)
			  
		- 스트림 vs 채널
			- IO는 스트림 기반이다.
			  스트림은 입력 스트림과 출력 스트림으로 구분되어 있기 때문에 데이터를 읽기 위해서는 입력 스트림을 생성해야 하고, 
			  데이터를 출력하기 위해서는 출력 스트림을 생성해야 한다.	
			- NIO는 채널 기반이다.
			  채널은 스트림과 달리 양방향으로 입력과 출력이 가능하다. 그렇기 때문에 입력과 출력을 위한 별도의 채널을 만들 필요가 없다.
			  
		- 넌버퍼 vs 버퍼
			- IO에서는 출력 스트림이 1바이트를 쓰면 입력 스트림이 1아티를 읽는다. 이러한 시스템은 대체로 느리다.
			- 이것보다는 버퍼(Buffer : 메모리 저장소)를 사용해서 복수 개의 바이트를 한꺼번에 입력받고 
			  출력하는 것이 성능에 이점을 가지게 된다.	  
			- 그래서 IO는 버퍼를 제공해주는 보조 스트림인 BufferedInputStream, 
			  BufferedOutputStream을 연결해 사용하기도 한다.  
			- NIO는 기본적으로 버퍼를 사용해서 입출력을 하기 때문에 IO보다 높은 성능을 가진다.
			
		- IO는 스트림에서 읽은 데이터를 즉시 처리한다.	  									
			- 스트림으로부터 입력된 전체 데이터를 별도로 저장하지 않으면, 입력된 데이터의 위치를 이동해가면서 자유롭게 이용할 수 없다.
		- NIO는 읽은 데이터를 무조건 버퍼에 저장한다.
			- 버퍼 내에서 데이터의 위치 이동을 해가면서 필요한 부분만 읽고 쓸 수 있다.
			
		- 블로킹 vs 넌블로킹
			- IO는 블로킹 된다.
				- 입력 스트림의 read() 메소드를 호출하면 데이터가 입력되기 전까지 Thread는 블로킹(대기상태)가 된다.
				- 마찬가지로 출력 스트림의 write() 메소드를 호출하면 데이터가 출력되기 전까지 Thread는 블로킹된다.
				- IO Thread가 블로킹되면 다른 일을 할 수 없고 블로킹을 빠져나오기 위해 인터럽트 할 수 없다.
					- 블로킹을 빠져나오는 유일한 방법은 스트림을 닫는 것이다.
			- NIO는 블로킹과 넌블로킹 특징을 모두 가진다.			
				- IO블로킹과 NIO 블로킹과의 차이점은 NIO 블로킹은 Thread를 인터럽트 함으로써 빠져나올 수 있다.	
		  		- 블로킹의 반대개념이 넌블로킹인데, 입출력 작업 시 Thread가 블로킹되지 않는 것을 말한다.
		  		- NIO의 넌블로킹은 입출력 작업 준비가 완료된 채널만 선택해서 작업 Thread가 처리하기 때문에 
		  		  작업 Thread가 블로킹되지 않는다.
		  	
		- 자바 I/O 프로세스
			1) 프로세스가 커널에 파일 읽기 명령을 내림
			2) 커널은 시스템 콜[read()]을 사용해 디스크 컨트롤러가 물리적 디스크로부터 읽어온 파일 데이터를 커널 영역안의 버퍼에 쓴다.
				DMA(Direct Memory Access) : CPU의 도움없이 물리적 디스크에서 커널영역의 버퍼로 데이터를 읽어오는 것
			3) 모든 파일 데이터가 버퍼에 복사되면 다시 프로세스 안의 버퍼로 복사한다.
			4) 프로세스 안의 버퍼의 내용으로 프로그래밍한다.  	  
		  	
		  	- 위의 I/O 프로세스에서 첫번째 문제는 3)의 과정이 너무나 비효율적이라는 것이다. 
		  	  왜냐하면 커널안의 버퍼 데이터를 프로세스 안으로 다시 복사하기 때문이다.
		  	- 만약 3)의 과정을 없애고 커널영역에 바로 접근할 수 있다면 어떻게 될까? 만약 이게 가능하다면 버퍼를 복사하는 
		  	  CPU를 낭비하지도, GC관리를 따로 하지 않아도 I/O를 사용할 수 있다.
		  	  
		  	- 커널 버퍼란 운영체제가 관리하는 메모리 영역에 생성되는 버퍼 공간
		  	- 자바는 외부데이터를 가져올 때 OS의 메모리 버퍼에 먼저 담았다가 JVM 내의 버퍼에 한 번 더 옮겨줘야하기 때문에 
		  	  시스템 메모리를 직접 다루는 C언어에 비해 입출력이 느리다.   
		    - 이러한 단점을 개선하기 위해 나온 ByteBuffer클래스의 allocateDirect() 메소드를 사용하면 
		      커널 버퍼를 사용할 수 있다. 그 외로 만들어지는 버퍼는 모두 JVM 내에 생성되는 버퍼이다.	  
		      
	13.6 표준 입출력 - System.in, System.out, System.err
		- 표준입출력은 콘솔을 통한 데이터 입력과 콘솔로의 데이터 출력을 의미한다.
		- 자바에서는 표준 입출력(standard I/O)를 위해 3가지 입출력 스트림을 제공한다.
			- System.in
			- System.out
			- System.err
		- 이들은 자바 어플리케이션의 실행과 동시에 사용할 수 있게 자동적으로 생성되기 때문에 
		  개발자가 별도로 스트림을 생성하는 코드를 작성하지 않아도 된다.	
		  
		- System 클래스를 살펴보면
			- in, out, err는 System클래스에 선언된 클래스 변수(static)이다.  
			- 선언부만 봐서는 in, out, err의 타입이 InputStream과 PrintStream이지만 실제로는 버퍼를 이용하는 
			  BufferedInputStream과 BufferedOutputStream의 인스턴스를 사용한다.	  	  
		  	- err은 버퍼링을 지원하지 않는다. 이것은 err이 보다 정확하고 빠르게 출력되어야 하기 때문이다. 버퍼링을 하던 도중 
		  	  프로그램이 멈추면 버퍼링된 내용은 출력되지 않기 때문이다.  
		  	  
	13.7 직렬화
		- 직렬화(serialization)란 객체를 데이터 스트림으로 만드는 것을 뜻한다.	  	  
		- 객체에 저장된 데이터를 스트림에 쓰기(write)위해 연속적인(serial) 데이터로 변환하는 것을 의미한다.
		- 반대로 스트림으로부터 데이터를 읽어서 객체를 만드는 것을 역직렬화(deserialization)이라 한다.
		
		- 객체에 대해 다시 짚고 넘어가보자.
			- 객체는 클래스에 정의된 인스턴스변수의 집합이다.
			- 객체에는 클래스변수나 메서드가 포함되지 않는다. 객체는 오직 인스턴스 변수들로만 구성되어 있다.
			- 표현시에 인스턴스변수와 메서드를 함께 그리곤 했지만, 사실 객체에는 메소드가 포함되지 않는다.
			- 인스턴스변수는 인스턴스마다 다른 값을 가질 수 있어야 하기 때문에 별도의 메모리 공간이 필요하지만 메서드는 변하는 것이 
			  아니라서 메모리를 낭비해 가면서 인스턴스마다 같은 내용의 코드(메서드)를 포함시킬 이유가 없다.
			- 객체를 저장한다는 것은 바로 객체의 모든 인스턴스변수의 값을 저장한다는 것과 같은 의미이다. 어떤 객체를 저장하고자 한다면, 
			  현재 객체의 모든 인스턴스변수의 값을 저장하기만 하면된다. 그리고 저장했던 객체를 다시 생성하려면, 
			  객체를 생성한 후에 저장했던 값을 읽어서 생성한 객체의 인스턴스변수에 저장하면 되는 것이다.
			  
	13.8 파일 읽고 쓰기		  
		- 텍스트 파일인 경우 문자 스트림 클래스들을 사용하면 되고, 바이너리 파일인 경우 바이트 스트림을 기본적으로 사용한다.
		- 입출력 효율을 위해 Buffered 계열의 보조 스트림을 사용하는 것이 좋다.	
		
		- 텍스트파일인 경우
			BufferedReader br = new BufferedReader(new FileReader("a.txt"));
			BufferedWriter bw = new BufferedWriter(new FileWriter("b.txt"));
			String s;
			while ((s = br.readLine()) != null) {
			    bw.write(s + "\n");
			}
			
		- 이진파일인 경우
			BufferedInputStream is = new BufferedInputStream(new FileInputStream("a.jpg"));
			BufferedOutputStream os = new BufferedOutputStream(new FileOutputStream("b.jpg"));
			byte[] buffer = new byte[16384];
			while (is.read(buffer) != -1) {
			    os.write(buffer);
			}
		
		  	  
		  	  
		  	    
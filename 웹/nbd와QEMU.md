# nbd

소켓으로 기계의 block 장치를 현재 컴퓨터의 block장치처럼 쓰게하는 리눅스의 기능

물론 꼭 저 멀리 떨어져있을 필요는 없으며, 꼭 block장치여야 한다는 법은 없다.

특정한 위치에서 특정한 길의 데이터를 read 하거나 write할 수만 있으면 상관없다.

![image](https://user-images.githubusercontent.com/5876149/212929322-209d2965-026b-4bf8-b764-e30e56821ff9.png)

# QEMU

Quick EMUlator 로 가상화 오픈소스이다. 

image로 만들어서 NDB를 이용해서 사용할 수 있다.

# Docker mount

![image](https://user-images.githubusercontent.com/5876149/212929388-aaea367f-748b-49eb-9409-e67096ceef14.png)

공식 설명을 다시 읽어봅시다.

바인드 마운트는 볼륨에 비해 기능이 제한되어있다. **바인드 마운트를 사용하면 호스트 시스템의 파일 또는 디렉토리가 컨테이너에 마운트 된다.** 파일 또는 디렉토리가 호스트 시스템의 전체 또는 상대 경로로 참조된다.

이게 뭔소리냐

차이점이라면 볼륨은 도커 영역안에서 관리된다는 점이다.

또 다른 차이점은 바인드 마운트의 경우 외부(host)에서 컨테이너 안쪽으로 내용을 추가할 수 있지만 볼륨의 경우 그렇지 않다.

볼륨이 바인드 마운트보다 좋은 장점을 docs에선 이렇게 적어놨다.

1. 백업하거나 이동시키기 쉽다.
2. docker CLI 명령어로 볼륨을 관리할 수 있다.
3. 볼륨은 리눅스, 윈도우 컨테이너에서 모두 동작한다.
4. 컨테이너간에 볼륨을 안전하게 공유할 수 있다.
5. 볼륨드라이버를 사용하면 볼륨의 내용을 암호화하거나 다른 기능을 추가 할 수 있다.
6. 새로운 볼륨은 컨테이너로 내용을 미리 채울 수 있다.

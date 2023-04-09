Docker compose란, 여러 개의 컨테이너로부터 이루어진 서비스를 구축, 실행하는 순서를 자동으로 하여, 관리를 간단히하는 기능이다.

Docker compose에서는 compose 파일을 준비하여 커맨드를 1회 실행하는 것으로, 그 파일로부터 설정을 읽어들여 모든 컨테이너 서비스를 실행시키는 것이 가능하다.

docker-compose.yml 파일은 아래와 같이 yaml으로 Docker 컨테이너에 관한 실행 옵션(build 옵션도 포함되어 있는 경우도 있다)를 기재한 파일이 된다.
# PM2

&nbsp;

| 1. [개요](#1-개요) <br>2. [프로젝트에서](#2-프로젝트에서)<br>3. [출처](#4-출처) |
| ------------------------------------------------------------------------------- |

## 1. 개요

![](https://i.imgur.com/bT3ZPU8.jpg)

> pm2.io 의 첫 화면

&nbsp;pm2 는 Node.js 로 만들어진 Application 을 지원하는 곳에 특화 되어있는 프로세스 관리자입니다. ( 또한, 다른 프로그램도 사용할 수 있다고 합니다. ) 서비스를 계속 이어갈 수 있게하고, Monitor 를 통해서 상태를 체크할 수 있는 기능을 가지고 있으며, 클러스터 모드로 여러 쓰레드에 사용할 수 있게 해줍니다.

### 1-1. 사용법

```
$ pm2 start server.js
```

&nbsp;다음과 같이 Application 의 서비스를 시작할 수 있습니다.

![](https://i.imgur.com/JGs3tB0.png)

```
$ pm2 list
```

&nbsp;현재 Application 의 동작들을 쉽게 확인할 수 있습니다.

```
$ pm2 stop     <app_name|namespace|id|'all'|json_conf>
$ pm2 restart  <app_name|namespace|id|'all'|json_conf>
$ pm2 delete   <app_name|namespace|id|'all'|json_conf>
```

&nbsp;다음과 같은 명령어들로 Application 들을 조작할 수 있습니다.

```
$ pm2 start api.js -i <processes>
```

&nbsp; 클러스터 모드를 지원해서 Node.js Load Balancing & Zero Downtime Reload 을 지원합니다. 그럼으로 다중 프로세서로 시작할 수 있음으로, 더 높은 performance 을 보여줍니다.

```
$ pm2 reload all
```

pm2에서는 Zero Downtime Reload 으로써, 어떤한 DownTime 없이 업데이트를 해준다고 합니다.

### 1-2. 클러스터 모드

1. PM2 - Cluster mode는 Node.js - Cluster module을 통해 작동합니다.
2. Node.js Cluster module은 Node.js에서 멀티코어 시스템을 이용할 수 있도록 Port를 공유하는 Child process를 생성합니다.
3. 요청을 Parent process가 받은 후 Child process들에 재분배합니다.
4. 각 Child process는 각각의 V8에서 작동하므로, Node 인스턴스를 더 띄운 것과 같이 동작합니다.
5. 인메모리에 의존하지 않는 프로그램을 만들면 위와 같은 확장에 더 용이합니다.

pm2 cluster mode에서는 session이나 websocket이 동작 하지 않는다. (출처: https://lahuman.jabsiri.co.kr/220)

## 2. 프로젝트에서

- **사용한 이유** : 일단 우리가 사용하는 서버의 최대 효율을 사용하기 위해 만들었으며, 리눅스 환경에서 서버의역활을 하는 node.js application 이 무중단으로 서비스하기 위해서, 사용했습니다.

---

- **Graceful START/SHUTDOWN** : PM2 의 클러스터 모드를 사용하면서 나는 Graceful 에 대해서 알게 되었다. 무엇이 Graceful 한걸까 ?
  ![](https://i.imgur.com/aCMhPLI.jpg)

일단 사전적인 의미는 이렇다. 내가 이곳 저곳에서 찾아본 결과 graceful 한 종료는 종료한다는 의미에 신호를 보내면 사용한것들을 정리하고 종료하는 것을 의미하고 , Graceful 하지 않은 것은 강제로 shutdown 하는 것이라고 한다. ( 데스크톱을 예를 들면, 컴퓨터 끄기를 실행시켜 끄는 것이고, ) Graceful 을 하는 이유는 로직이 제대로 완료 되지 않고 꺼져버리면 생길 수 있는 side effect 를 줄이기 위해서 이다.

프로세스에서는 유닉스 시그널을 바탕으로 만든 신호로 통신한다.

```
kill -l
```

로 현재 사용할 수 있는 시그널을 확인할 수 있다고 한다.
우리가 일상적으로 ctl+c (SIGINT), ctl+z (SIGSTOP) 같은 것들이 있다고 한다.

다시 이야기로 돌아와서 우리 프로젝트에 Graceful 하게 종료하고 시작하기 위해서 시작해 보도록하자.

시그널 이야기가 나온것은 PM2 에서 cluster 모드에서 process 가 종료되었을 때 SIGINT 를 사용하고, 시간안에 끝나지 않으면 SIGKILL 을 하는 설정이 들어있다.

우리는 Cluster 모드일 때 SIGINT 와 SIGKILL 사이의 시간을 조정할 수 있으며, 우리의 코드에서도 SIGINT 일 때 무엇을 할 것 인지 결정할 수 있다.

```
process.on('SIGINT', function() {
   db.stop(function(err) {
     process.exit(err ? 1 : 0);
   });
});
```

다음처럼 디비를 끄는 것도 할 수 있다 !!

또한 프로세스가 필요 이상으로 오래걸린다면, 설정파일에서 강제로 끄는 설정도 넣을 수 있다.

ecosystem.config.js 에 pm2 에 설정을 넣을 수 있다.

작성법은

```
module.exports = {
  apps: [
    {
      name: 'app',
      script: 'server.js',
      instances: 4,
      exec_mode: 'cluster', // 실행 모드. cluster로 명시하지 않으면 기본 fork 모드가 된다.
      wait_ready: true, // Node.js 앱으로부터 앱이 실행되었다는 신호를 직접 받겠다는 의미
      listen_timeout: 50000, // 앱 실행 신호까지 기다릴 최대 시간. ms 단위.
      kill_timeout: 5000, // 새로운 프로세스 실행이 완료된 후 예전 프로세스를 교체하기까지 기다릴 시간
      max_memory_restart: '500M',
      env: {
        NODE_ENV: 'development',
      },
      env_production: {
        NODE_ENV: 'production',
        PORT: '8081',
      },
    },
  ],
}
```

사용법은

```
$ pm2 [start|restart|stop|delete] ecosystem.config.js
```

이다.

또 다른 경우는 시작 하는 부분이다. node 가 시작되면 pm2 에게 ready 가 되었다는 명령어를 보내는데 프로세스가 시작될 때 보낸다고 한다. 다만, 이 프로세스가 시작되었다고 모든 서비스를 할 준비가 되어 있는 것은 아니다. ( 프로세스가 시작된거지, DB 도 연결되고 , 뭐 API 불러오는 것들도 있고, 이런 것들은 프로세스가 시작되자마자 같이 사용 가능 상태인 것은 아니다.)

일단, 공식 문서에서는 옵션에 `wait_ready: true` 를 추가하고

```
var listener = app.listen(0, function() {
  console.log('Listening on port ' + listener.address().port);
  // Here we send the ready signal to PM2
  process.send('ready');
});
```

다음 처럼 세팅이 모두 끝나고 process.send 를 해주면 됩니다. 다만 child 만들지 않았을 때, process.send 는 정의되지 않았을 수 있기때문에, 조건을 걸어주면 됩니다. 저희 코드에서는

```
    if (typeof process.send === 'function') {
      process.send('ready');
    }
```

처럼 하면 해결할 수 있습니다.

---

## 3. 출처

- https://pm2.io/
- https://www.npmjs.com/package/pm2
- https://engineering.linecorp.com/ko/blog/pm2-nodejs/
- https://short-term.tistory.com/6
- https://2kindsofcs.tistory.com/53
- https://blog.rhostem.com/posts/2019-10-23-pm2-graceful-reload

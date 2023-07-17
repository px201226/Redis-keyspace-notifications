

## Redis Keyspace Notifications
Redis에는 키 및 값의 변경 사항을 실시간으로 수신할 수 있는 Pub/Sub 기능을 제공한다.


## Notifications 유형
Redis 데이터 공간에 영향을 미치는 모든 작업에 대해 다음 두 가지 유형의 이벤트가 발생된다.
예를 들어 키를 삭제하는 명령인 `del myKey1` 이 실행되면 Redis는 다음 두 가지 명령이 트리거된다.
```redis
PUBLISH __keyspace@0__:mykey1 del
PUBLISH __keyevent@0__:del mykey1
```
첫번째 PUBLISH 명령은 Key 중심 이벤트로, `del`이 이벤트의 메시지고
두번쨰 PUBLISH 명령은 명령 중심 이벤트로, `myKey1`이 이벤트의 메시지이다. 
Sub 하는 쪽에서는 Subscribe 패턴 매칭을 통해서 두 가지 유형 중 관심있는 이벤트만 선택해서 알림을 받을 수 있다.


## Redis Keyspace Notifications 설정
기본적으로 Keyspace Notifications은 비활성화되어 있기 때문에, `redis.conf` 또는 `CONFIG SET` 명령어로 `notify-keyspace-events` 옵션을 활성화 할 수 있다.
옵션에는 다음 값들이 있다.
이벤트 종류
- K   Keyspace events, publish prefix "__keyspace@<db>__:".
- E   Keyevent events, publish prefix "__keyevent@<db>__:".
- g   공통 명령: del, expire, rename, ...
- $   스트링(String) 명령
- l   리스트(List) 명령
- s   셋(Set) 명령
- h   해시(Hash) 명령
- z   소트 셋(Sorted set) 명령
- x   만료(Expired) 이벤트 (키가 만료될 때마다 생성되는 이벤트)
- e   퇴출(Evicted) 이벤트 (최대메모리 정책으로 키가 삭제될 때 생성되는 이벤트)
- A   모든 이벤트(g$lshzxe), "AKE"로 지정하면 모든 이벤트를 받는다.

여기서 K 또는 E는 필수적으로 있어야 하며, 나머지 값들은 선택적으로 설정할 수 있다.
예를 들어, 어떤 Key가 Expire되는지만 수신하고 싶다면 아래와 같이 설정하면 된다.   
```
notify-keyspace-events "Kx": 키 이벤트 + 만료 이벤트 발생
```

Redis 명령에 따른 이벤트 종류를 보고 싶다면 [공식문서](https://redis.io/docs/manual/keyspace-notifications/)를 참조하자. 





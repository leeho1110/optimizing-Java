# 8장: GC 로깅, 모니터링, 튜닝, 툴

### 개요

이번 장에서는 GC를 로깅하는 모니터링에 대해서 알아봅니다. 조금은 가볍게 알쓸신잡 느낌으로 넘어가보겠습니다.

---

### 로깅 켜놓기

- GC 로깅은 오버헤드가 거의 없습니다. 로그를 생성하고 별도로 GC 로그를 보관하는 것은 필수입니다.
    - `-Xloggc:gc.log`
        - GC 이벤트를 로깅할 파일을 지정합니다.
    - 기존 플래그 `verbose:gc`를 지우고 `-XX:+PrintGCDetails`
        - GC 이벤트 세부 정보를 로깅하는 옵션입니다.
    - `-XX:+PrintTenuringDistribubtion`
        - 툴링에 꼭 필 요한 부가적인 GC 이벤트지만 이 플래그를 통해 제공받은 정보를 사람이 이용하긴 어렵습니다.
        - Memory pressure(메모리 할당 압박) 효과, 조기 승격 등의 이벤트 계산 시 필요한 기초 데이터를 제공하는 옵션입니다.
    - `-XX:+PrintGCTimeStamps`,`-XX:+PrintGCDateStamps`
        - 나열 순대로 GC 이벤트와 애플리케이션 이벤트(로그파일)을 연관시키는 역할과 GC와 다른 내부 JVM 이벤트를 연관짓는 역할로 쓰입니다.
    - `-XX:+UseGCLogFileRoation`
        - 로그의 순환 기능을 켭니다.
    - `-XX:+NumberOfGCLogFiles=<n>`
        - 보관 가능한 최대 로그파일 개수를 설정합니다.
    - `-XX:+GCLogFileSize=<size>`
        - 순환 직전 각 파일의 최대 크기를 설정합니다.

---

### GC 기본 튜닝

- GC 튜닝을 시도해야 한다면 아래 조건을 기억합시다.
    - GC가 성능 문제의 근원 혹은 그렇지 않다고 판단하는 행위는 비용이 적습니다.
    - UAT(User acceptance testing)에서 GC 플래그를 켜는 것 역시 비용이 적습니다.
    - 하지만 메모리 프로파일러, 실행 프로파일러를 설정하는 작업은 신중해야 합니다.
- 힙 크기를 조정할 수 있는 플래그입니다.
    - `-Xms<size>`
        - 힙 메모리의 최소 크기를 설정합니다.
    - `-Xmx<size>`
        - 힙 메모리의 최대 크기를 설정합니다.
    - `-XX:MaxMetaspaceSize=<size>`
        - 메타스페이스 메모리의 최대 크기를 설정합니다.
        - 만약 자바 8 애플리케이션에 `-XX:MaxPermSize=<size>` 설정값을 발견한다면 어차피 적용되지 않으니 지우면 됩니다. 자바 8부터 펌젠 스페이스는 사라졌습니다.
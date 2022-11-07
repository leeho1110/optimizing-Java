### 개요

벤저민 J. 에번스의 '자바 최적화'를 읽고 정리한 레포지토리입니다.

---

### 서평

JVM에서 적용할 수 있는 가비지 컬렉터의 종류와 내부 동작 원리가 궁금해 읽게 되었습니다. GC 관련 내용을 학습하며, JVM 내부 아키텍쳐에 대한 이해도 필요했었는데요. [Java Performance Fundamental](https://performeister.tistory.com/75) 저자분게서 PT 자료를 공유해주신 것과 여러 페이지들을 통해 학습할 수 있었습니다. 관련되어 정리한 자료는 [여기](https://github.com/leeho1110/optimizing-Java/blob/main/JVM%20Internal/JVM%20Internal.md)서 확인하실 수 있습니다.

서적 초반에는 성능 최적화를 위해 시도할 수 있는 방법과 모니터링에 대한 이야기들이 나옵니다. 가비지 컬렉션에 대한 본격적인 내용은 6장부터 시작됩니다. 

실무에서 JVM을 직접 다룰 일은 많지 않습니다. 실제 JVM 구현체를 만들 일도 거의 없죠. 그럼에도 불구하고 학습해야 하는 이유는 뭘까요? 자신이 사용하는 기술을 제대로 알고 활용하는 것과 아닌 것은 효율성 및 견고함에서 차이가 발생하기 때문입니다.

장시간의 STW로 인해 서버 장애가 발생했다면 어떻게 해결할까요? 서버가 OOM Killer에 의해 계속 죽는다면 어떻게 해결할까요? 그 때 우리가 장애 해결을 위해 고려해야하는 시야는 발 밑에 축적되온 지식의 부피에 따라 달라집니다. 

더 많은 지식을 갖고 있고 이를 활용한다면, 더 높은 시야에서 더 넓게 문제점을 정의하고 해결해나갈 수 있습니다. 이는 코드로써 비즈니스를 지탱하는 개발자에게는 고객의 불편함과 장애 해결에 필요한 시간 비용을 줄일 수 있다는 것을 의미합니다. 이것이 제가 꾸준히 학습하는 이유입니다. 

이번 서적을 통해 GC에 대해 딥다이브할 수 있었고 난이도가 꽤 어려웠습니다. 만약 GC에 대한 내용을 알고 싶지만, [공식 문서](https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html)를 읽기 부담스러우시다면 읽어보셔도 좋을 듯 합니다.

---

### Q&A

- ***Q. GC는 무엇인가요? 왜 필요할까요?***
    
    GC는 Garbage Collector의 약자로써 **더 이상 사용(참조)되지 않는 객체들을 메모리에서 제거해 Heap Memory를 재사용할 수 있도록 정리해주는 프로그램**입니다. 자바에서는 개발자가 메모리를 직접 핸들링하지 않습니다. 대신 별도의 프로그램이 메모리를 할당하고 해제하는 작업을 수행합니다. 이 때 ‘해제’의 대상이 되는 더 이상 사용되지 않는 메모리 공간을 Garbage라고 부릅니다. 이러한 Garbage는 말 그대로 쓰레기이기 때문에 Heap 메모리 영역의 재사용을 위해선 반드시 제거되어야 합니다. 그래야 다음에 사용할 데이터들을 적재할 수 있으니까요. 
    
    또한 해제,할당 과정에서는 메모리 공간이 정렬되지 않아, 사용 가능한 메모리 공간의 크기가 실제 빈 공간보다 적은 Memory Fragmentation 현상이 나타나는데요. 이런 문제점들도 해결해줍니다. 결국은 **효율적인 메모리 재사용을 위해서 존재**하는 것이죠.
    
- ***Q. Garbage Collector에서 Young, Old Memory는 왜 존재할까요? 존재하지 않을수도 있을까요?***
    
    Generationl Algorithm은 Weak Generational Hypothesis, 약한 세대 가설에 기반합니다. 새로 새성된 객체 중 아주 짧은 시간만 살아있는 객체가 대부분이고, 만약 살아남는다면 객체의 기대 수명이 매우 길다는 가정을 하는 것이죠. 기대 수명이 짧은 객체들은 빠르게 GC를 통해 메모리 가용성을 높히는 것이 좋습니다. 반면 기대 수명이 긴 객체들은 애플리케이션의 프로세스에 방해가 되지 않도록 효율적으로 처리해야하죠. 객체의 기대 수명에 맞는 작업을 수행할 수 있도록 그 위치를 Young, Survivor, Old 영역으로 나눠 배치하게 된 것입니다. 
    
    물론 Garbage First GC, G1 GC부터는 이러한 메모리의 물리적 구분을 없애고 Region 방식을 사용해 배치하게 됩니다. 물리적 구분만 없을 뿐 Generational Algorithm은 존재합니다.
    
    - ***Q. Generationl Algorithm은 무엇일까요?***
        
        Generational Argorithm은 Heap 영역을 Young, Survivor, Old으로 나눠 GC하는 알고리즘입니다. Heap을 age별로 sub heap으로 나누고 그 중 Youngest generation sub heap을 자주 GC하는 방식이죠. Generationl Algorithm은 각 Sub-heap에서 다른 Mark-and-sweep 알고리즘 등을 함께 사용하는 경우가 많습니다. 이를 통해 Memory Fragmentation같은 문제점을 해결할 수 있습니다.

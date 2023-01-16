# 생성자 대신 정적 팩터리 메서스를 고려하라.

### 정정 팩토리 메서드의 장점
1. 이름 가질 수 있어 반환될 객체의 특성 쉽게 묘사
2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 됨. 인스턴스를 미리 만들어 놓거나 생성한 인스턴스를 캐싱하여 재활용 가능
3. 반환 타입의 하위 타입 객체를 반환할 수 있음.
4. 입력 매개변수에 따라매번 다른 클래스의 객체를 반환 가능. 반환타입의 하위타입이기만 하면 됨.
5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 됨.

### Static Factory Method 자주 사용하는 명명 방식
- from : 해당 타입의 인스턴스를 반환하는 형변환 메서드
    ```java
    Date d = Date.from(instant);
    ``` 
- of : 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드
    ```java
    Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
    ```
- valueOf : from과 of의 더 자세한 버전
    ```java
    BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
    ```
- instance, getInstance : 매개변수로 명시한 인스턴스를 반환.
    ```java
    StackWalker luke = StackWalker.getInstance(options);
    ```    
- create, newInstance : 매번 새로운 인스턴스를 생성해 반환 
    ```java
    Object newArray  = Array.newInstance(classObject, arrayLen);
    ``` 
- getType : getInstacne와 같으나 다른 클래스를 반환하는 팩터리 메서드 정의. get{반환할 객체의 타입} 
   ```java
    FileStore fs = Files.getFileStore(path);
    ```
- newType : newInstance와 같으나 다른 클래스를 반환하는 팩터리 메서드 정의, new{반환할 객체의 타입}
    ```java
    BufferedReader br = Files.newBufferedReader(path);   
    ```
- type : getType, newType의 간결한 버젼. type으로만 이름 지음
    ```java
    List<Complaint> litany = Collections.list(legacyLitany);
    ```



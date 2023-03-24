### 형 변환
```java
//String -> 숫자
String sNum = "1234";
int i1 = Integer.parseInt(sNum);
int i2 = Integer.valueOf(sNum);
double d1 = Double.parseDouble(sNum);
//float, long,short

//숫자 -> String
int i = 1234;
String i1 = String.valueOf(i);
String i2 = Integer.toString(i);
double d = 1.23;
String d1 = String.valueOf(d);
//long, float, short
```

### 배열 초기화
```java
//같은 값으로 초기화
int[] A = new int[5];
Arrays.fill(A, 10);
```

### 입력 받기
Scanner는 속도가 느리므로 코테에서는 BufferedReader를 사용하자.
```java
//Scanner
Scanner sc = new Scanner(System.in);
int N = sc.nextInt();
String s = sc.next();

//BufferedReader
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
StringTokenizer stringTokenizer = new StringTokenizer(bufferedReader.readLine());
int suNo = Integer.parseInt(stringTokenizer.nextToken());
```

### 출력
StringBuffer
```java
StringBuffer sb = new StringBuffer();
sb.append("wisdom");
System.out.println(sb.toString());
```
StringBuilder
- 싱글 스레드에서 StringBuffer보다 성능에 이점이 있다.
```java
StringBuilder sb = new StringBuilder();
sb.append("wisdom");
System.out.println(sb.toString());
```
BufferedWriter
- flush() : 버퍼의 내용을 즉시 출력스트림으로 보냄
- close() : 스트림 닫음. 내부적으로 flush()를 호출 후 닫음
```java
BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
bw.write("wisdom");
bw.append("\nlove")'
bw.flush();
bw.close();
```

### 자주 사용하는 함수
```java
//split
String str = "1 2 3 4 5 6";
String[] strArr = str.split(" ");
```

### Deque
Double-Ended Queue로 양쪽에서 add,remove 가능
```java
 Deque<Integer> DQ = new LinkedList<>();
 DQ.addLast(3);
 DQ.addFirst(2);
 System.out.println(DQ.getLast());
 DQ.removeFirst();
 DQ.removeLast();
 if(DQ.isEmpty()) System.out.println("Deque is empty");
```

### Stack
후입선출(LIFO)    
- 용어    

|||
|--|--|
|top|최상단 위치, 삽입 삭제 일어나는 위치|
|push|top에 데이터 삽입|
|pop|top의 데이터 삭제하고 확인|
|peek|top의 데이터 확인|
```java
Stack<Integer> st = new Stack<>();
st.push(1);
if(st.empty()){
    System.out.println(st.peek());
    st.pop();         
}
```
### 큐
선입선출(FIFO)
- 용어    
  
|||
|--|--|
|rear|가장 끝 위치|
|front|가장 앞 위치|
|add|rear에 데이터 삽입|
|poll|front에 데이터 삭제하고 확인|
|peek|front에 있는 데이터 확인|
```java
Queue<Integer> Q = new LinkedList<>();
if(Q.isEmpty()){
    Q.add(1);
}
int front = Q.poll();
Q.add(front);
System.out.println(Q.peek());
```

### 우선순위 큐
default로 작은값이 높은 우선순위를 가진다.
```java
 PriorityQueue<Integer> pQ1 = new PriorityQueue<>();
 pQ1.add(1);
 pQ1.add(2);
 pQ1.peek(); //1


/**
  절대값이 크면 높은 우선순위. 절대값을 같을 경우 값이 더 크면 높은 우선순위 가지도록 함
  o2-o1이 양수이면 큰수가 우선순위, o1-o2가 양수이면 작은수가 우선순위
*/
 PriorityQueue<Integer> pQ2 = new PriorityQueue<>((o1, o2) -> {
    int res = Math.abs(o2) - Math.abs(o1);
    return res==0 ? o2-o1: res;
 });
pQ2.add(3);       
pQ2.add(4);
pQ2.add(-4);
//4
```

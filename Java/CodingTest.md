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

```java
//입력 받기

//Scanner
Scanner sc = new Scanner(System.in);
int N = sc.nextInt();
String s = sc.next();

//BufferedReader
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
StringTokenizer stringTokenizer = new StringTokenizer(bufferedReader.readLine());
int suNo = Integer.parseInt(stringTokenizer.nextToken());
```
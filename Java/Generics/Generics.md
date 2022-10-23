# Generics
  다양한 타입의 객체를 다루는 메소드나 컬렉션 클래스에 컴파일 시의 타입 체크를 해주는 기능

## 1. 제네릭 클래스 선언
```java
class Box<T> {
    T item;
    void setItem(T item){this.item = item;}
    T getITem(){return item;}
}
Box<String> b = new Box<String>();
b.setItem(new Object()); //Error String type만 가능
b.setItem("ABC");
```
### 용어
  - Box<T> : Generic Class T의 Box
  - T를 타입변수(type variable)이라고 함.  
    E: element, K: key, V:value 등 여러 알파벳을 의미에 따라 사용 가능
  - Box 원시타입
### 제한
```java
  class Box<T>{
    static T int; //에러,모든 객체에 동일하게 동작해야 하므로 허용X
    T[] itemArr; //OK T타입의 배열을 위한 참조변수
    T[] toArray() {
      T[] tmpArr = new T[itemArr.length]; //지네릭 배열 생성 불가 error
      //지네릭 배열 생성X : new 연산자는 컴파일 시점에 타입 T를 정확히 알아야 하므로
    }
  } 
```

## 2. Generic Class 객체 생성과 사용
```java
  class Fruit{}
  class Apple extends Fruit {}
  class Grape extends Fruit {}
  class Toy {}
  class Box<T> {
    ArrayList<T> list = new ArrayList<T>();
    void add(T item) {list.add(item);}
    T get(int i) { return list.get(i);}
  }

  class FruitBox {
    public static void main(String[] args){
      Box<Fruit> fruitBox = new Box<Fruit>();
      Box<Fruit> fruitBox = new Box<Apple>(); //에러 ,상속관계여도 타입 달라 에러
      Box<Apple> appleBox = new Box<>(); //생략 가능
      Box<Grape> grapeBox = new Box<Apple>(); //에러 Type 불일치

      fruitBox.add(new Fruit());
      fruitBox.add(new Apple()); //OK void add(Fruit item)
      appleBox.add(new Apple());
      appleBox.add(new Toy()); //에러, Box<apple>에는 Toy담을 수 없음
      toyBox.add(new Toy());
      toyBox.add(new Apple()); //에러. Box<Toy>에는 Apple 담을 수 없음

    }  
  }
//참고 자료형 추론(자바10부터)
var list = new ArrayList(); //var는 ArrayList 타입을 추론함.

```
    

## 3. 제한된 지네릭 클래스
### <T extends 클래스> 사용하기
- 자료형의 범위를 제한할 수 있음
- 상위 클래스에서 선언하거나 정의하는 메서드를 활용할 수 있음
```java
public abstract class Parents {
        //공통으로 쓰일 메서드를 정의해두고 override해서 사용 가능
    public abstract void do();
}
public class GenericClass<T extends Parents>{
    private T t;
}
```
Fruit의 자손이면서 Etable interface를 구현한 클래스만 타입 매개변수로 대입받고자 할경우
     Box 제네릭 클래스를 상속 받음. 주의할 것은 interface도 extends라는 것.
```java     
class FruitBox<T extends Fruit & Eatable> extends Box<T>
```
## 4. 와일드 카드
 - 하기와 같이 Fruit의 모든 하위 클래스를 사용하기 위한 makeJuice라는 정적 함수를 만든다고 가정.
 - static 메소드는 타입 매개변수 T 사용 못함. 특정타입을 정해줘야 하므로 여러개의 매개변수를 갖는
   makeJuice를 만들어야 한다. 그러나 지네릭 타입이 다른 것만으로는 오버로딩이 성립되지 않아
   에러가 발생한다.
```java
static String makeJuice(FruitBox<Fruit> box){
        String tmp="";
        for(Fruit t: box.getList()){
            tmp += t.toStr();
        }
        return tmp;
    }

static String makeJuice(FruitBox<Apple> box){ //오버로딩 성립안됨. 에러 발생
        String tmp="";
        for(Apple t: box.getList()){
            tmp += t.toStr();
        }
        return tmp;
}
```

 - 이럴 경우 사용하기 위한 것이 와일드 카드
```  
     <? extends T> T와 그 자손들만 가능  
     <? super T> T와 그 조상들만 가능  
     <?> 제한 없음. 모든 타입 가능. <T extends Object>와 동일`  
```

```java
//와일드 카드 적용하면 정상 동작 함.
static String makeJuice(FruitBox<? extends Fruit> box) {
    String tmp = "";
    for (Fruit t : box.getList()) {
        tmp += t.toStr();
    }
    return tmp;
}
FruitBox<Fruit> fruitBox = new FruitBox<Fruit>();
FruitBox<Apple> appleBox = new FruitBox<Apple>();
```


- "<? super T>" 적용 예제  

```java
class AppleComp implements  Comparator<Apple> {
    @Override
    public int compare(Apple o1, Apple o2) {
        return o2.weight - o1.weight; //내림 차순
    }
}
class GrapeComp implements Comparator<Grape> {
    @Override
    public int compare(Grape o1, Grape o2) {
        return o2.weight - o1.weight;
    }
}
class FruitComp implements Comparator<Fruit> {
    @Override
    public int compare(Fruit o1, Fruit o2) {
        return o1.weight - o2.weight; //오름차순
    }
}

class main {
    public static void main(String[] args) {
        FruitBox<Apple> appleBox = new FruitBox<Apple>();
        FruitBox<Grape> grapeBox = new FruitBox<Grape>();

        appleBox.add(new Apple("GreenApple",300));
        appleBox.add(new Apple("GreenApple",100));
        appleBox.add(new Apple("GreenApple",200));

        grapeBox.add(new Grape("GreenGrape", 400));
        grapeBox.add(new Grape("GreenGrape", 300));
        grapeBox.add(new Grape("GreenGrape", 200));

        Collections.sort(appleBox.getList(), new AppleComp());
        Collections.sort(grapeBox.getList(), new GrapeComp());
        System.out.println("appleBox = " + appleBox);
        System.out.println("grapeBox = " + grapeBox);

        Collections.sort(appleBox.getList(), new FruitComp());
        Collections.sort(grapeBox.getList(), new FruitComp());
        System.out.println("grapeBox = " + grapeBox);
        System.out.println("appleBox = " + appleBox);
    }
}
```


Collections.sort() 선언부는 하기와 같다.  
`static <T> void sort(List<T> list, Comparator<? super T> c);`  
만약 Comparator<T>로 되어 있다면 Apple,Grape정렬 위해 각각의 Comparator 구현해줘야한다. 그러나 Comparator<? super T>이므로 T의 조상이 가능하다. 그래서 Comparator<Fruit>으로 모든 하위 클래스에 사용가능하다.

## 5. 제네릭 메서드
메소드의 선언부에 제네릭 타입이 선언된 메서드  
  `static <T> void sort(List<T> list, Comparator<? super T> c)`

  ```java
class FruitBox<T> { //이 T랑 sort의 T는 다른거다..
    ...
    static <T> void sort(List<T> list, Comparator<? super T> c){}
}
```
- 제네릭 클래스에 정의된 타입 매개변수와 제네릭 메서드에 정의된 타입 매개변수는 같은 문자를 사용해도 별개의 것 임.
- static 멤버에는 타입 매개변수 사용할 수 없지만 메서드에 지네릭 타입 선언하고 사용하는 것은 가능. 메서드에 선언된 제네릭 타입은 지역 변수를 선언한 것과 같다고 생각하면 됨.
    
```java 
    class Juicer {
      static Juice makeJuice(FruitBox<? extends Fruit> box){}
    }
    
    // => generic method로 변경
    class Juicer {
      static <T extends Fruit> Juice makeJuice(FruitBox<T> box){}
    }
    //호출할 때 타입 명시해야 함. 근데 컴파일러가 타입 추정가능하기에 생략해도 된다.
    System.out.println(Juicer.<Fruit>makeJuice(fruitBox));
    //Juicer.makeJuice(fruitBox);
```
  - 복잡한 제네릭 메소드의 예  
    `public static <T extends Comparable<? super T>> void sort(List<T> list)`  
    타입 T를 요소로 하는 리스트를 매개변수로 허용  
    T는 T와 그 조상의 타입을 비교하는 Comparable을 구현한 클래스이어야 함.
    
## 지네릭 타입의 형변환
  
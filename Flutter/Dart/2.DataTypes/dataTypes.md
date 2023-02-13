### 타입은 Object다
- String, bool,int,double 등 모두 Object
- int,double은 num class를 상속받으며 num은 int,double 값 모두 가질 수 있음

### list
```dart
var numbers = [1,2,3,4];
List<int> numbers = [1,2,3,4]; //명시적으로 선언
numbers.add(4);

var giveMeFive = true;
  var numbers = [
    1,
    2,
    3,
    4,
    if (giveMeFive) 5,  //Collection if
    //끝에 쉼표 후 ;하면 세로로 줄을 나열해줌
  ];  
```

### String Interpolation
```dart
var name = 'wisdom';
var age = 10;
var greeting = "Hello everyone, my name is $name and I'm ${age+2}";
```

### Collection
```dart
var oldFriends = ['nico', 'lynn'];
  var newFriends = [
    'lewis',
    'ralph',
    'darren',
    for (var friend in oldFriends) "new $friend", //Collection for
  ];
```

### Map
```dart
var player = { //Map<String, Object>
    'name': 'nico',
    'xp': 19.99,
    'superpower': false,
  };

Map<int, bool> player = {
    1:true,
    2:true,
    3:true
};

Map<List<int>, bool> player = {
    [1,2,3,4]:true,
};

List<Map<String, Object>> players = [
    {'name': 'nico', 'xp': 1999},
    {'name': 'nico', 'xp': 1999}
];
```

### Set
```dart
var numbers = {1, 2, 3, 4};
  //Set<int> numbers = {1, 2, 3, 4};
  numbers.add(1); //요소가 유니크하다. 추가해도 추가되지 않음
```









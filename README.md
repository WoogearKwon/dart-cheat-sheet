# Dart 언어 스터디 저장소
- 모든 것을 적는 대신 몰랐던 것, 애매하게 알고 있었던 것을 위주로 나중에 사용할 목적으로 기록한다.


# Language Cheat Sheet
참고 링크: [Dart cheatsheet codelab](https://dart.dev/codelabs/dart-cheatsheet)

## Null-aware operators
Dart에는 Nullable한 값을 다룰 때 사용할 수 있는 연산자들있다. 
- **??=** 연산자
```dart
int? a; // = null
a ??= 3;
print(a); // <-- Prints 3.

a ??= 5;
print(a); // <-- Still prints 3.
```

- **??** 연산자
```dart
print(1 ?? 3); // <-- Prints 1.
print(null ?? 12); // <-- Prints 12.
```

## Cascades
같은 객체에 대해 여러 동작을 수행할 때 casecades(..)를 사용한다.
```dart
myObject.someMethod()
```
위의 경우 객체의 함수를 호출하면 그 함수의 반환값을 리턴한다.

```dart
myObject..someMethod()
```
반면 cascade를 사용하면 함수는 호출되더라도 함수의 반환값이 아닌 객체의 레퍼런스를 리턴한다.
따라서 cascade를 사용해서 여러 동작을 묶어서 하나의 문장으로 작성할 수 있다. 
```dart
var button = querySelector('#confirm');
button.text = 'Confirm';
button.classes.add('important');
button.onClick.listen((e) => window.alert('Confirmed!'));
```
위의 코드에서 cascade를 사용해 아래와 같이 작성할 수 있다.
```dart
querySelector('#confirm')
..text = 'Confirm'
..classes.add('important')
..onClick.listen((e) => window.alert('Confirmed!'));
```

## Optional Positional Parameters
Dart에는 두 가지 함수 파라미터가 있다. 하나는 positional이고 하나는 named이다. Optional Positional Parameter는 대괄호를 사용한다.

- positional 파라미터는 아래와 같이 optional 하게 작성할 수 있다.
- ptional 파라미터는 항상 뒤에 위치해야 한다.
- 값을 전달해주지 않았거나 기본값을 설정해주지 않았다면 기본값은 null이다. 
```dart
int sumUpToFive(int a, [int? b, int? c, int? d, int? e]) {
  int sum = a;
  if (b != null) sum += b;
  if (c != null) sum += c;
  if (d != null) sum += d;
  if (e != null) sum += e;
  return sum;
}
// ···
  int total = sumUpToFive(1, 2);
  int otherTotal = sumUpToFive(1, 2, 3, 4, 5);
```

아래와 같이 기본값을 설정해줄 수도 있다.
```dart
int sumUpToFive(int a, [int b = 2, int c = 3, int d = 4, int e = 5]) {
// ···
}
// ···
  int newTotal = sumUpToFive(1);
  print(newTotal); // <-- prints 15
```

### Optional Named Parameters
중괄호를 사용하여 파라미터 이름을 정의할 수 있다. 
```dart
void printName(String firstName, String lastName, {String? suffix}) {
  print('$firstName $lastName ${suffix ?? ''}');
}
// ···
  printName('Avinash', 'Gupta');
  printName('Poshmeister', 'Moneybuckets', suffix: 'IV');
```

마찬가지로 파라미터 기본값을 정의해주지 않으면 기본값은 null이며, non-nullable한 파라미터라면 기본값을 제공하거나 @required 표시를 해줘야 한다.
```dart
void printName(String firstName, String lastName, {String suffix = ''}) {
  print('$firstName $lastName $suffix');
}
```

## Exceptions
on 키워드를 사용해 특정 예외 타입을 필터링하여 처리할 수 있다. 
```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // 특정 예외 타입(OutOfLlamasException) 발생시 여기서 처리
  buyMoreLlamas();
} on Exception catch (e) {
  // 그 외 다른 타입의 예외를 여기서 처리
  print('Unknown exception: $e');
} catch (e) {
  // 명시되지 않은 예외를 모두 처리
  print('Something really unknown: $e');
}
```

한 부분에서 발생한 예외를 자신을 호출한 함수에서 알아챌 수 있도록 전파하기 위해서는 rethrow를 사용한다
```dart
try {
  breedMoreLlamas();
} catch (e) {
  print('I was just trying to breed llamas!');
  rethrow;
}
``` 

## Named Constructors
Dart는 named constructor를 지원함으로써 여러 개의 생성자를 가질 수 있다. 
```dart
class Point {
  double x, y;

  Point(this.x, this.y);

  Point.origin()
      : x = 0,
        y = 0;
}
```

named constructor를 사용하려면 아래와 같이 전체 이름을 호출한다.
```dart
final myPoint = Point.origin();
```

## Redirecting Constructors
종종 어떤 생성자는 같은 클래스 내의 다른 생성자로 리다이렉팅을 해주는 목적만을 위해 존재하기도 한다. 리다이렉팅 생성자의 바디는 비어있으며 콜론(:) 다음에 생성자 호출 코드를 가진다. 
```dart
class Automobile {
  String make;
  String model;
  int mpg;

  // 이 클래스의 메인 생성자
  Automobile(this.make, this.model, this.mpg);

  // 메인 생성자로 작업을 넘긴다.
  Automobile.hybrid(String make, String model) : this(make, model, 60);

  // named 생성자로 작업을 넘긴다.
  Automobile.fancyHybrid() : this.hybrid('Futurecar', 'Mark 2');
}
``` 

## Const Constructors
만일 어떤 클래스가 절대 변하지 않는 객체를 생성한다면, 그 객체들을 컴파일 타임 상수로 만들수 있다. 그러려면 const 생성자를 정의하고 모든 인스턴스 변수를 final로 선언하면 된다. 
```dart
class ImmutablePoint {
  const ImmutablePoint(this.x, this.y);

  final int x;
  final int y;

  static const ImmutablePoint origin = ImmutablePoint(0, 0);
}
```

# Iterable Collections
참고 링크: [Iterable Collections](https://dart.dev/codelabs/iterables)

## 예제: firstWhere() 사용하기
특정 조건을 만족하는 첫 번째 요소 아이템을 찾을 때 사용한다.
```dart
String element = iterable.firstWhere((element) => element.length > 5);
```

리스트 안의 문자열 중 5글자를 초과하는 첫 번째 문자열을 리턴하는 예제코드이다.
```dart
bool predicate(String element) {
  return element.length > 5;
}

main() {
  var items = ['Salad', 'Popcorn', 'Toast', 'Lasagne'];

  // 간단한 표현식으로 찾음
  var element1 = items.firstWhere((element) => element.length > 5);
  print(element1);

  // 함수 블록을 사용함
  var element2 = items.firstWhere((element) {
    return element.length > 5;
  });
  print(element2);

  // 함수의 레퍼런스로 전달
  var element3 = items.firstWhere(predicate);
  print(element3);

  // 아무것도 찾지 못했을 경우 `orElse` 함수를 사용할 수도 있다. 
  var element4 = items.firstWhere(
    (element) => element.length > 10,
    orElse: () => 'None!',
  );
  print(element4);
}
```
아래는 조건식을 작성하는 세 가지 방법이다.
- 표현식(Expression): 화살표(=>)를 사용한 표현식으로 조건을 정의할 수 있다.
- 코드 블록(Block): 중괄호를 사용해 여러 줄의 코드를 작성하고 return 문을 사용할 수 있다.
- 함수(Function): 외부 함수를 생성해 firstWhere()의 파라미터로 넘길 수 있다.

위 세 가지 방법 중 무엇이 항상 더 좋거나 옳은 것이 아니라 자신의 상황에 맞게 적절한 방식을 사용하면 된다.

## 조건 확인하기 (Checking Conditions)
Iterable 안의 모든 요소가 특정 조건을 만족하는지 확인하기 위해서는 어떤 방법을 사용해야 할까?

일반적으로 for-in 루프를 사용한 아래와 같은 방식은 좋은 방식이 아니다. 
```dart
for (var item in items) {
  if (item.length < 5) {
    return false;
  }
}
return true;
```

대신 every() 메서드를 사용하여 더 쉽게 구현할 수 있다.
```dart
return items.every((element) => element.length >= 5);
```

## 예제: any()와 every() 사용하기 
```dart
void main() {
  var items = ['Salad', 'Popcorn', 'Toast'];
  
  if (items.any((element) => element.contains('a'))) {
    print('At least one element contains "a"');
  }
  
  if (items.every((element) => element.length >= 5)) {
    print('All elements have length >= 5');
  }
}
```


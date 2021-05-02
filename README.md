# Dart 언어 스터디 저장소
- 참고 링크: [Dart cheatsheet codelab](https://dart.dev/codelabs/dart-cheatsheet)
- 모든 것을 적는 대신 몰랐던 것, 애매하게 알고 있었던 것을 위주로 나중에 사용할 목적으로 기록한다.


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
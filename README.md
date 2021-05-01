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
on 키워드를 사용해 특정 예외 타입을 필터링하여 처리할 수 있다. 

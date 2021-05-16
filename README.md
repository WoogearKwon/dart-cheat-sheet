# Dart 언어 스터디 저장소
- 모든 것을 적는 대신 몰랐던 것, 애매하게 알고 있었던 것을 위주로 나중에 사용할 목적으로 기록한다.


# Language Cheat Sheet
[출처 링크](https://dart.dev/codelabs/dart-cheatsheet)

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
[출처 링크](https://dart.dev/codelabs/iterables)

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
    print('최소 하나의 항목이 "a"를 포함하고 있음');
  }
  
  if (items.every((element) => element.length >= 5)) {
    print('모든 요소들이 >= 5의 길이를 가진다');
  }
}
```

## Filtering
where() 메서드를 사용하면 Iterable 내의 특정 조건을 만족하는 모든 요소들을 찾을 수 있다.
아래는 where() 메서드를 통해 필터링한 evenNumbrs를 for-in 루프 안에서 출력하는 예제이다. 

```dart
var evenNumbers = numbers.where((number) => number.isEven);
for (var number in evenNumbers) {
  print('$number is even');
}
``` 

아래는 any()를 where()와 함께 사용한 예제이다.
```dart
main() {
  var evenNumbers = [1, -2, 3, 42].where((number) => number.isEven);

  for (var number in evenNumbers) {
    print('$number is even.');
  }

  if (evenNumbers.any((number) => number.isNegative)) {
    print('evenNumbers 는 음수를 포함한다.');
  }

  // If no element satisfies the predicate, the output is empty.
  var largeNumbers = evenNumbers.where((number) => number > 1000);
  if (largeNumbers.isEmpty) {
    print('largeNumbers 는 비어있다.');
  }
}
```

## 예제: takeWhile 사용하기
takeWhile()과 skipWhile() 또한 Iterable을 필터링하는데 사용하기 좋은 메서드이다.
```dart
main() {
  var numbers = [1, 3, -2, 0, 4, 5];

  var numbersUntilZero = numbers.takeWhile((number) => number != 0);
  print('Numbers until 0: $numbersUntilZero');

  var numbersAfterZero = numbers.skipWhile((number) => number != 0);
  print('Numbers after 0: $numbersAfterZero');
}
```

## Mapping
map() 메서드를 사용해 Iterable의 모든 요소를 바꿀 수 있다. 아래는 numbers의 모든 요소에 10을 곱한 Iterable을 리턴하는 예제이다.
```dart
// output은 numbers의 모든 요소에 10을 곱한 Iterable이다.
Iterable<int> output = numbers.map((number) => number * 10);
```

또한 map()을 사용해 Iterable의 요소를 다른 객체로 변환할 수 있다. 아래는 모든 int를 String으로 변환하는 예제이다. 
```dart
Iterable<String> output = numbers.map((number) => number.toString());
```

```dart
main() {
  var numbersByTwo = [1, -2, 3, 42].map((number) => number * 2);
  print('Numbers: $numbersByTwo.');
  // 출력 결과 
  // Numbers: (2, -4, 6, 84).
}
```

# 비동기 프로그래밍: futures, async, await
[출처 링크](https://dart.dev/codelabs/async-await)

## future
Future의 인스턴스로서, 비동기 작업의 결과를 나타낸다. future는 uncompleted 또는 comple3ted 두 가지 상태를 가진다. 

### Uncompleted
사용자가 비동기 함수를 호출하면, 그 함수는 uncompleted future를 반환한다. future는 그 함수의 비동기 작업이 끝나기를 기다리거나 에러를 던진다.

### Completed
비동기 작업이 성공하면 future는 값과 함께 작업을 완료한다. 아니면 에러와 함께 작업을 완료한다.

### 리턴값과 함께 작업 완료하기
```Future<T>``` 타입의 future는 T 타입의 리턴값을 가지고 작업을 완료한다. 예를들어, Future<String> 타입의 future는 String 값을 생성해낸다. 만약 future가 사용가능한 값을 생성하지 않는다면 그 futue의 타입은 Future<void>이다. 

### 에러와 함께 작업 완료하기
어떤 이유에서건 future에 의해 실행된 비동기 작업이 실패하면, future는 에러와 함께 완료된다. 

## future 사용하기: async와 await
async와 await 키워드를 단순히 선언하는 방식을 통해 비동기 함수와 그 결과를 정의할 수 있다. 아래의 두 가지 기본 가이드라인을 기억하자.
- async 함수를 정의하려면 함수의 바디 앞에 async를 붙인다.
- await 키워드는 async 함수 안에서만 사용이 가능하다. 

예제코드: 동기 함수(아래)
```dart
String createOrderMessage() {
  var order = fetchUserOrder();
  return 'Your order is: $order';
}

Future<String> fetchUserOrder() =>
    // 이 함수가 더 복잡하고 느리다고 상상할 것
    Future.delayed(
      Duration(seconds: 2),
      () => 'Large Latte',
    );

void main() {
  print('Fetching user order...');
  print(createOrderMessage());
}
```

예제코드: 비동기 함수(아래)
```dart
Future<String> createOrderMessage() async {
  var order = await fetchUserOrder();
  return 'Your order is: $order';
}

Future<String> fetchUserOrder() =>
    // 이 함수가 더 복잡하고 느리다고 상상할 것
    Future.delayed(
      Duration(seconds: 2),
      () => 'Large Latte',
    );

Future<void> main() async {
  print('Fetching user order...');
  print(await createOrderMessage());
}
```

위의 두 가지를 제외하면 동기적 코드와 매우 유사하다. 비동기 함수는 세 가지 측면에서 차이가 있다.
- createOrderMessage()의 리턴타입이 String에서 Future<String>로 바뀌었다.
- async 키워드가 createOrderMessage()와 main() 함수의 바디 앞에 추가되었다.
- await 키워드가 비동기 함수 fetchUserOrder()와 createOrderMessage()를 호출하는 코드 앞에 추가되었다.

# 비동기 프로그래밍: Streams
[출처 링크](https://dart.dev/tutorials/language/streams)

Future는 즉시 완료되지 않는 처리를 대표한다. 읾반적인 함수는 결과값을 반환하는 반면, 비동기 함수는 Future를 반환하는데, 이 또한 결국 결과값을 가진다. Future는 결과값이 언제 준비되었는지 알려준다.

Stream은 연속적 비동기 이벤트로서, 비동기 iterable과 비슷하다. 사용자가 요청할 때 다음 이벤트를 수행하는 대신 stream은 준비가 되면 사용자에게 다음 이벤트가 있다고 알려준다. 

## Stream 이벤트 수신하기
Stream은 여러 방법으로 생성될 수 있지만, 사용방법은 모두 같다. 비동기 루프(보통 그냥 await for라고 부른다.)가 Stream 이벤트를 도는데 마치 iterable을 도는 for 루프와 같다. (아래 예시) 
```dart
Future<int> sumStream(Stream<int> stream) async {
  var sum = 0;
  await for (var value in stream) {
    sum += value;
  }
  return sum;
}
```
위 코드는 그저 정수 이벤트의 스트림 이벤트를 각각 받아서 더하고, 그 합계를 반환한다. 루프가 종료되면 함수는 다음 이벤트가 도착하거나 스트림이 끝날 때까지 멈춘다.

함수가 async 키워드로 마크되어있는데, 이는 await for 루프를 사용하기 위해 필요하다. 

아래는 앞의 코드를 테스트하기 위해 async* 함수를 사용해 간단한 정수 stream을 생성하는 코드이다.
```dart
import 'dart:async';

Future<int> sumStream(Stream<int> stream) async {
  var sum = 0;
  await for (var value in stream) {
    sum += value;
  }
  return sum;
}

Stream<int> countStream(int to) async* {
  for (int i = 1; i <= to; i++) {
    yield i;
  }
}

main() async {
  var stream = countStream(10);
  var sum = await sumStream(stream);
  print(sum); // 55
}
```

Stream 사용하기
Stream Class는 iterable과 비슷하게 여러 helper 메서드를 포함하고 있다. 예를 들면 lastWhere()를 사용해 스트림에서 가장 마지막에 있는 양의 정수를 찾을 수 있다. 
```dart
Future<int> lastPositive(Stream<int> stream) =>
    stream.lastWhere((x) => x >= 0);
```

## Stream을 가공하는 메서드 목록
```dart
Future<T> get first;
Future<bool> get isEmpty;
Future<T> get last;
Future<int> get length;
Future<T> get single;
Future<bool> any(bool Function(T element) test);
Future<bool> contains(Object needle);
Future<E> drain<E>([E futureValue]);
Future<T> elementAt(int index);
Future<bool> every(bool Function(T element) test);
Future<T> firstWhere(bool Function(T element) test, {T Function() orElse});
Future<S> fold<S>(S initialValue, S Function(S previous, T element) combine);
Future forEach(void Function(T element) action);
Future<String> join([String separator = ""]);
Future<T> lastWhere(bool Function(T element) test, {T Function() orElse});
Future pipe(StreamConsumer<T> streamConsumer);
Future<T> reduce(T Function(T previous, T element) combine);
Future<T> singleWhere(bool Function(T element) test, {T Function() orElse});
Future<List<T>> toList();
Future<Set<T>> toSet();
```
drain()과 pipe()을 제외한 위의 모든 함수들은 Iterable의 함수들과 유사하다. 각각의 메서드는 async 함수에 await for를 작성해서 쉽게 사용할 수 있다. 

## stream을 수정해주는 메서드 목록
아래의 메서드들은 원래의 stream을 기반으로한 새로운 stream을 반환한다. 아래의 메서드들은 Iterable에도 비슷하게 존재하는 메서드들이다.  
```dart
Stream<R> cast<R>();
Stream<S> expand<S>(Iterable<S> Function(T element) convert);
Stream<S> map<S>(S Function(T event) convert);
Stream<T> skip(int count);
Stream<T> skipWhile(bool Function(T element) test);
Stream<T> take(int count);
Stream<T> takeWhile(bool Function(T element) test);
Stream<T> where(bool Function(T event) test);
```

아래의 asyncExpand()와 asyncMap() 함수들은 expand()와 map()과 유사하지만 파라미터로 비동기 함수를 받을 수 있다.
```dart
Stream<E> asyncExpand<E>(Stream<E> Function(T event) convert);
Stream<E> asyncMap<E>(FutureOr<E> Function(T event) convert);
Stream<T> distinct([bool Function(T previous, T next) equals]);
```

아래의 마지막 함수 세 개는 더욱 특별하다. await for 루프는 할 수 없는 에러 취급 코드를 포함하고 있다. 첫 에러가 루프에 도달하면 루프와 스트림의 구독이 종료된다. 
```dart
Stream<T> handleError(Function onError, {bool test(error)});
Stream<T> timeout(Duration timeLimit,
    {void Function(EventSink<T> sink) onTimeout});
Stream<S> transform<S>(StreamTransformer<T, S> streamTransformer);
```

아래는 handlerError()를 사용하는 예제로, await for 루프에서 사용되기 전에 stream에서 에러를 제거한다.
```dart
Stream<S> mapLogErrors<S, T>(
  Stream<T> stream,
  S Function(T event) convert,
) async* {
  var streamWithoutErrors = stream.handleError((e) => log(e));
  await for (var event in streamWithoutErrors) {
    yield convert(event);
  }
}
```

## listen() 메서드
listen 메서드는 저수준(low-level)의 메서드로, 모든 stream 함수들은 listen()에 대해 정의되어 있다.

```dart
StreamSubscription<T> listen(void Function(T event) onData,
    {Function onError, void Function() onDone, bool cancelOnError});
```
새로운 Stream 타입을 만드려면, Stream 클래스를 상속하고 listen() 메서드를 구현하면 된다. 모든 stream의 메서드는 동작을 위해 listen()을 호출하게 되어있다.

listen() 메서드는 사용자로 하여금 stream을 청취하기 시작하게 한다. 그 전까지 stream은 비활성상태의 객체로 어떤 이벤트를 사용자가 보길 원하는지 설명해준다. 사용자가 청취하면 StreamSubscription 객체가 반환되는데, 이는 이벤트를 생산하는 활성화된 stream을 대표한다. 이 특징은 Iterable이 그저 객체의 집합이지만, 반복을 수행하는 주체는 iterator라는 점과 매우 흡사하다.

stream 구독은 사용자가 구족을 잠시 멈추고, 다시 재개하고, 완전히 취소할 수 있게 해준다. 사용자는 콜백을 설정해서 스트림이 종료되었을 때 각각의 데이터 이벤트나 에러 이벤트가 호출될 수 있게 할 수 있다. 
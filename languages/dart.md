# Dart

Effective Dart: Design - https://dart.dev/guides/language/effective-dart/design

## Hello World

```dart
void main() {
  print('Hello World');
}
```

## Variables

Dart is type-safe, but just like Golang, you do not need to explicitly declare types, thanks to inference

```dart
var name = 'Voyager I';
var year = 1977;
var antennaDiameter = 3.7;
var flybyObjects = ['Jupiter', 'Saturn', 'Uranus', 'Neptune'];
var image = {
  'tags': ['saturn'],
  'url': '//path/to/saturn.jpg'
};

// not restricted to a single type
dynamic name = 'Bob';

// explicitly declare
String name = 'Bob';

// no change to a variable
final name = 'Bob'; // can be set only once
const name = 'Bob'; // must be available at compile-time
```

Uninitialized variables have an initial value of null. Even variables with numeric types are initially null, because numbers like everything else in Dart are objects.

## Collections

```dart
// LIST
var list = [1, 2, 3];
var constantList = const [1, 2, 3]; // compile-time constant

var list2 = [0, ...list];
assert(list2.length == 4);

var list;
var list2 = [0, ...?list];
assert(list2.length == 1);

// collection if
var nav = [
  'Home',
  'Furniture',
  'Plants',
  if (promoActive) 'Outlet'
];

// collection for
var listOfInts = [1, 2, 3];
var listOfStrings = [
  '#0',
  for (var i in listOfInts) '#$i'
];

// SET - unordered
var halogens = {'fluorine', 'chlorine', 'bromine', 'iodine', 'astatine'};
var names = <String>{};
final constantSet = const {
  'fluorine',
  'chlorine',
  'bromine',
  'iodine',
  'astatine',
};

// MAP
var nobleGases = {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};
var nobleGases = Map();
nobleGases[2] = 'helium';
nobleGases[10] = 'neon';
nobleGases[18] = 'argon';

// null returned on missing key
var gifts = {'first': 'partridge'};
assert(gifts['fifth'] == null);
```

## Control flow

```dart
if (year >= 2001) {
  print('21st century');
} else if (year >= 1901) {
  print('20th century');
}

for (var object in flybyObjects) {
  print(object);
}

for (int month = 1; month <= 12; month++) {
  print(month);
}

while (year < 2016) {
  year += 1;
}
```

## Functions

Functions in Dart are objects and have a type, Function. This means that functions can be assigned to variables or passed as arguments to other functions.

```dart
int fibonacci(int n) {
  if (n == 0 || n == 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

var result = fibonacci(20);

// one line function
flybyObjects.where((name) => name.contains('turn')).forEach(print);

// parameters can have named parameters or optional positional parameters but not both

// named parameters
void enableFlags({bool bold, bool hidden}) {
  // ...
}
// named parameters are optional but you can set @required
void enableFlags({bool bold, @required bool hidden}) { ... }

// default parameter values
void enableFlags({bool bold = false, bool hidden = false}) { ... }

// optional positional parameters
String say(String from, String msg, [String otherThingsToSay]) {
  // ...
}

// default parameter values
String say(String from, String msg, [String otherThingsToSay = 'umm nothing']) { ... }
```

Every app must have a top-level main() function, which serves as the entrypoint to the app.

```dart
void main(List<String> args) {
  ...
}
```

## Comments

```dart
// This is a normal, one-line comment.

/// Documentation summary.
///
/// This is a documentation comment, used to document libraries,
/// classes, and their members. Tools like IDEs and dartdoc treat
/// doc comments specially.

/* Comments like these are also supported. */
```

## Imports

```dart
// Importing core libraries
import 'dart:math';

// Importing libraries from external packages
import 'package:lib1/lib1.dart';
import 'package:lib2/lib2.dart' as lib2;

// Importing files
import 'path/to/my_other_file.dart';

// Import all names EXCEPT foo.
import 'package:lib2/lib2.dart' hide foo;

// lazy loading a library. Reduces a web app's initial startup time
import 'package:greetings/hello.dart' deferred as hello;

// when you need it then call loadLibrary()
Future greet() async {
  await hello.loadLibrary();
  hello.printGreeting();
}
```

## Classes

```dart
class Spacecraft {
  String name;
  DateTime launchDate;

  // Constructor, with syntactic sugar for assignment to members.
  Spacecraft(this.name, this.launchDate) {
    // Initialization code goes here.
  }

  // Named constructor that forwards to the default one.
  Spacecraft.unlaunched(String name) : this(name, null);

  int get launchYear =>
      launchDate?.year; // read-only non-final property

  // Method.
  void describe() {
    print('Spacecraft: $name');
    if (launchDate != null) {
      int years =
          DateTime.now().difference(launchDate).inDays ~/
              365;
      print('Launched: $launchYear ($years years ago)');
    } else {
      print('Unlaunched');
    }
  }
}

var voyager = Spacecraft('Voyager I', DateTime(1977, 9, 5));
voyager.describe();

var voyager3 = Spacecraft.unlaunched('Voyager III');
voyager3.describe();

// factory constructors
// the factory keyword implies that the constructor does not always create a new instance of its class. It can return an instance from a cache
class Logger {
  final String name;
  bool mute = false;

  // _cache is library-private, thanks to
  // the _ in front of its name.
  static final Map<String, Logger> _cache =
      <String, Logger>{};

  factory Logger(String name) {
    return _cache.putIfAbsent(
        name, () => Logger._internal(name));
  }

  factory Logger.fromJson(Map<String, Object> json) {
    return Logger(json['name'].toString());
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}

// getters and setters
class Rectangle {
  double left, top, width, height;

  Rectangle(this.left, this.top, this.width, this.height);

  // Define two calculated properties: right and bottom.
  double get right => left + width;
  set right(double value) => left = value - width;
  double get bottom => top + height;
  set bottom(double value) => top = value - height;
}

void main() {
  var rect = Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -8);
}
```

## Inheritance

Dart has single inheritance

```dart
class Orbiter extends Spacecraft {
  double altitude;
  Orbiter(String name, DateTime launchDate, this.altitude)
      : super(name, launchDate);
}

// A person. The implicit interface contains greet().
class Person {
  // In the interface, but visible only in this library.
  final _name;

  // Not in the interface, since this is a constructor.
  Person(this._name);

  // In the interface.
  String greet(String who) => 'Hello, $who. I am $_name.';
}

// An implementation of the Person interface.
class Impostor implements Person {
  get _name => '';

  String greet(String who) => 'Hi $who. Do you know who I am?';
}

String greetBob(Person person) => person.greet('Bob');

void main() {
  print(greetBob(Person('Kathy')));
  print(greetBob(Impostor()));
}
```

## Mixins

Reuse code in multiple class hierarchies. To implement a mixin, create a class that extends Object and declares no constructors. Unless you want your mixin to be usable as a regular class, use the mixin keyword instead of class.

```dart
class Piloted {
  int astronauts = 1;
  void describeCrew() {
    print('Number of astronauts: $astronauts');
  }
}

class PilotedCraft extends Spacecraft with Piloted {
  // ···
}
```

## Interfaces and abstract classes

All classes implicitly define an interface

```dart
class MockSpaceship implements Spacecraft {
  // ···
}
```

```dart
abstract class Describable {
  void describe();

  void describeWithEmphasis() {
    print('=========');
    describe();
    print('=========');
  }
}
```

## Async

```dart
const oneSecond = Duration(seconds: 1);

Future<void> printWithDelay(String message) async {
  await Future.delayed(oneSecond);
  print(message);
}

// equivalent to
Future<void> printWithDelay(String message) {
  return Future.delayed(oneSecond).then((_) {
    print(message);
  });
}

// You can also use async*, which gives you a nice, readable way to build streams
Stream<String> report(Spacecraft craft, Iterable<String> objects) async* {
  for (var object in objects) {
    await Future.delayed(oneSecond);
    yield '${craft.name} flies by $object';
  }
}
```

## Exceptions

Use throw

```dart
if (astronauts == 0) {
  throw StateError('No astronauts.');
}

// catch exceptions
try {
  for (var object in flybyObjects) {
    var description = await File('$object.txt').readAsString();
    print(description);
  }
} on IOException catch (e) {
  print('Could not describe object: $e');
} finally {
  flybyObjects.clear();
}
```

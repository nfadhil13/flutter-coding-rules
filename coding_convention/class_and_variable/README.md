
## Class
---

- **DO** name Classes, enum types, typedefs, and type parameters using `UpperCamelCase`

  **`GOOD`**

  ```dart
  class JobPosition {...}
  class Department {...}
  extension MyFancyList<T> on List<T> { ... }
  ```

  **`BAD`**

  ```dart
  class Job_Postion {...}
  class department {...}
  ```

---

- **DO** use named parameter for class constructor if the class constructor need more than two properties.

  **`GOOD`**

  ```dart
  class Person {
    final String name;
    final String address;
    final int age;

    const Person ({required this.name; required this.address, required this.age}); //DO Use named parameter when properties more than two
  }
  class Department {
    final int id;
    final String name;

    const Department (this.id, this.name);
  }
  ```

  **`BAD`**

  ```dart
  class Person {
    final String name;
    final String address;
    final int age;
    final String job;

    const Person (this.name; this.address, this.age, this.job); // This constructor has 4 params, it has to change to named params for coding convenience purpose
  }

  ```

---

- **DO** use `const` when it is possible.
- **DO** name the class the same as the file name. 

## Variable

---

- **DO** named a variable with `camelCase`. Use `camelCase` also for constant value

  **`GOOD`**

  ```dart
    const apiURL = "https://something.co.id"; // use camelCase also for a constant vlaue

    void main() {
      final fooBar = "foorBar" // use camelcase
    }
  ```

  **`BAD`**

  ```dart

    const API_URL = "https://something.co.id"; // Constant value must use camel case

    void main() {
      final FooBar = "FooBar" // DO NOT USE PASCAL CASE FOR A VARIABLE NAME
      final bar_foo = "bar_foo" // DO NOT USE LOWER CASE
    }

  ```

---

- **DO** use `final` by default to declare variable, if the declared variable is mutable than change it to `var` afterward

  **`GOOD`**

  ```dart
    void main() {
      final foo = "foo"; // Foo never change , then use final
      var bar = "bar"
      bar = foo; // Because value can change, then you should use var not final
      doSomething();
    }
  ```

  **`BAD`**

  ```dart
    //BAD
    void main() {
      var  foo = "foo"; // Foo never change , then use final
      var bar = "bar"
      bar = foo; // Because value can change, then you should use var not final
      doSomething();
    }

  ```

---

- **DO** use `late` when the variable can be guaranted that it will be initialized before the variable is used.

  **`GOOD`**

  ```dart

    late final String foo;

    void main() {
      foo = "foo"; // Foo use late because it is guaranted that the variable will be initialized before it is used
      print(foo);
    }
  ```

  **`BAD`**

  ```dart
    String? foo;
    void main() {
      foo = "foo"; // Do NOT USE NULLABLE STRING IF YOU CAN GUARANTED THE INITIALIZATION
      print(foo?.toString())  // BECAUSE U ARE USING NULLABLE ANOTATION THE PRINT PROCESS NEED TO BE CHECK WHETHER THE VARIABEL IS NUL OR NOT
    }

  ```

---

- **DO NOT** declare variable as `nullable` if the data is not `nullable`. Use `not-nullable` by default.

  ```
  With the assumsion if every `Person` must have an attribute
  of age,name,address  so it may not be nullable.
  ```

  **`GOOD`**

  ```dart
  class Person {
    final String name; // name variable MUST NOT BE NULLABLE because based on the asumpsion there is no way this data will be nullable
    final int age; // age variable MUST NOT BE NULLABLE because based on the asumpsion there is no way this data will be nullable
    final String address; //  address variable MUST NOT BE NULLABLE because based on the asumpsion there is no way this data will be nullable

    const Person({
      required this.name,
      required this.age,
      required this.address
    })
  }
  ```

  **`BAD`**

  ```dart
  class Person {
    final String? name; // name variable MUST NOT BE NULLABLE because based on the asumpsion there is no way this data will be nullable
    final int? age; // age variable MUST NOT BE NULLABLE because based on the asumpsion there is no way this data will be nullable
    final String? address; //  address variable MUST NOT BE NULLABLE because based on the asumpsion there is no way this data will be nullable

    const Person({
      this.name,
      this.age,
      this.address
    })
  }
  ```

---

- **DO NOT** use `dynamic` whenever it is possible. Dart is staticly typed language use `generic instead`
  **`GOOD`**

  ```dart
  class APIResponse<T>{
    final T data; // This data may change based on the api response so use generic to handle this kind of data
    final String message
    const APIResponse(this.data, this.message)
  }
  ```

  **`BAD`**

  ```dart
  class APIResponse{
    final dynamic data; // DO NOT USE DYNAMIC
    final String message
    const APIResponse(this.data, this.message)
  }
  ```

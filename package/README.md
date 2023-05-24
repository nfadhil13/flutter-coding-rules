# Package Documentation

## Fundamental Packages

---

Fundamental package is base necessarry packages for setup project.

### Routing

- [auto_route](https://pub.dev/packages/auto_route)
- [auto_route_generator](https://pub.dev/packages/auto_route_generator)

Auto route is used to manage route/navigation with clean and minimal amount of code, because auto route will generate everything needed for navigation inside the App.

### State Management

- [bloc](https://pub.dev/packages/bloc)
- [flutter_bloc](https://pub.dev/packages/flutter_bloc)

Bloc is one of many state management in flutter. Bloc has 2 basic concept, `Cubit` and `Bloc`.

- A `Cubit` is a class which extends BlocBase and can be extended to manage any type of state.
- A `Bloc` is a more advanced class which relies on `events` to trigger `state` changes rather than functions. `Bloc` also extends BlocBase which means it has a similar public API as Cubit
  `We use Cubit as the state management in this project.` We use cubit rather than bloc because cu
  bit is more simple, easier to understand and there is less code involved.

### Service Locator

- [get_it](https://pub.dev/packages/get_it)

It's a simple `Service Locator` for Dart and Flutter projects to `increase decoupling`. Get it offer two different ways to register objects to service locator, `Factory` and `Singleton`.

### API Request

- [http](https://pub.dev/packages/http)
  It's a package to make HTTP request.

## Storage

- [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage)
  This package is used to store data (Token, authentication ID, etc) in Secure Storage and it is wrapped with encryptions, separate for Android & iOS.
- [sqflite](https://pub.dev/packages/sqflite)
  It's a SQLite plugin for Flutter. This package will use if the project need some relational database for local storage

### Utilities

- [equatable](https://pub.dev/packages/sqflite)
  This package is used to `Simplify Equality Comparisons`. Equatable overrides == and hashCode, so we don't have to waste our time writing lots of boilerplate code.
- [logger](https://pub.dev/packages/sqflite)
  This package is used to print beautiful logs.
- [intl](https://pub.dev/packages/sqflite)
  This package is used to provides internationalization and localization facilities (translation, date/number formating and parsing, etc)

## Other Necessary Packages

1. [change_app_package_name](https://pub.dev/packages/change_app_package_name)
   This package is used to change app package name with single command. it will update AndroidManifest, build.gradle, MainActivity files & move MainActivity file to new directory structure automatically.
2. [flutter_launcher_icons](https://pub.dev/packages/flutter_launcher_icons)
   This package is used to update flutter app's launcher icon
3. [flutter_svg](https://pub.dev/packages/flutter_svg)
   This package is used to render SVG on flutter widget
4. [responsive_framework](https://pub.dev/packages/responsive_framework)
   This package is used to manage responsiveness of screen. In this package we can define breakpoint screen for mobile, table and desktop. It also has several widgets that help us to implement responsiveness in better way.

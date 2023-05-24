# Coding Flow

This documentation is about how to code with this [project architecture](../architecture/) sequently.
For example, we have to implement registration feature. The project structure is like below:

```
📦lib
 ┣ 📂infrastructure
 ┣ 📂modules
 ┃ ┣ 📂authenticated
 ┃ ┣ 📂common
 ┃ ┣ 📂guest
 ┃ ┃ ┣ 📂auth
 ┃ ┃ ┣ 📂registration
 ┃ ┃ ┃ ┣ 📂data
 ┃ ┃ ┃ ┣ 📂domain
 ┃ ┃ ┃ ┗ 📂presentation
 ┃ ┃ ┗ 📂splash
 ┃ ┗ 📜rukun_app.dart
 ┗ 📜main.dart
```

### Code Flow:

1.  First thing to do is we have to define models in domain layer that will be used for this feature.

    ```dart
    ...
    📦registration
    ┣ 📂data
    ┣ 📂domain
    ┃ ┗ 📂models
    ┃ ┃ ┗ 📜registered_user_model.dart
    ┗ 📂presentation

    class RegisteredUserModel extends Equatable { // Extends equatable to handling equality
      final String fullName;
      final String userRole;
      final String email;

      const RegisteredUserModel(
          {required this.fullName, required this.userRole, required this.email});

      @override
      List<Object?> get props => [fullName, userRole, email];
    }
    ```

2.  Create a mapper to map the `json` object obtained from remote/local dts to a `registeredUserModel` object

    ```dart
    ...
    ┣ 📂data
    ┃ ┗ 📂datasources
    ┃ ┃ ┗ 📂remote
    ┃ ┃ ┃ ┗ 📂mapper
    ┃ ┃ ┃ ┃ ┗ 📜registered_user_mapper.dart
    ┣ 📂domain
    ┗ 📂presentation

    // mapper class has to extends JSONMapper of RegisteredUserModel
     class RegisteredUserMapper extends JSONMapper <RegisteredUserModel> {
       @override
       RegisteredUserModel fromJSON(JSON json) {
           return RegisteredUserModel(
             fullName: json['fullName'],
             userRole: json['userRole'],
             email: json['email']);
       }

       @override
       JSON toJSON(RegisteredUserModel data) {
        ...
       }
     }

    ```

3.  Create `abstraction` file for registration remote datasource and its `implementation` inside `data layer`. Each method has to return **`APIResult`** of **`Model`** type.

    ```dart
    ...
    ┣ 📂data
    ┃ ┗ 📂datasources
    ┃ ┃ ┗ 📂remote
    ┃ ┃ ┃ ┣ 📂mapper
    ┃ ┃ ┃ ┗ 📂sources
    ┃ ┃ ┃ ┃ ┣ 📜registration_remote_dts.dart
    ┃ ┃ ┃ ┃ ┗ 📜registration_remote_dts_impls.dart
    ┣ 📂domain
    ┗ 📂presentation

     abstract class RegistrationRemoteDts { // inside 📜registration_remote_dts.dart
       Future<APIResult<RegisteredUserModel>> registerUser({
         required String email,
         required String fullName,
         required String password,
       });
     }

     class RegistrationRemoteDtsImpls implements RegistrationRemoteDts { // 📜registration_remote_dts_impls.dart
       final BaseHttpClient _client;
       RegistrationRemoteDtsImpls(this._client);

       @override
       Future<APIResult<RegisteredUserModel>> registerUser({
         required String email,
         required String fullName,
         required String password,
       }) async {
         final registeredUserMapper = RegisteredUserMapper();
         return _client.post(
           url: "/registration",
           mapper: (json) => registeredUserMapper.fromJSON(json),
           body: {
             "fullName": fullName,
             "password": password,
             "email": email,
           },
           query: {},
         );
       }
     }
    ```

4.  Create abstraction file for registration repository inside domain layer
    Each method in repo has to return **`Result`** of **`Model`** type.

    ```dart
    ...
    ┣ 📂data
    ┣ 📂domain
    ┃ ┗ 📂repositories
    ┃ ┃ ┗ 📜registration_repo.dart
    ┗ 📂presentation

     abstract class RegistrationRepo {
     //Method return result of registered user model
       Future<Result<RegisteredUserModel>> registerUser({
         required String email,
         required String fullName,
         required String password,
       });
     }
    ```

5.  Then create the implementation for registration repository inside data layer

    ```dart
    ...
    ┣ 📂data
    ┃ ┣ 📂datasources
    ┃ ┗ 📂repository_impls
    ┃ ┃ ┗ 📜registration_repo_impls.dart
    ┣ 📂domain
    ┗ 📂presentation

     class RegistrationRepoImpls implements RegistrationRepo {
       final RegistrationRemoteDts _registrationRemoteDts;

       RegistrationRepoImpls(this._registrationRemoteDts);
       @override
       Future<Result<RegisteredUserModel>> registerUser({
         required String email,
         required String fullName,
         required String password,
       }) {
         return _registrationRemoteDts.registerUser(
             email: email, fullName: fullName, password: password);
       }
     }
    ```

6.  Create usecase for register user inside domain layer. `RegisterUser` has to extends `Usecase<Params/NoParams, Result>`
    Method in usecase return `Resource` of `Model`, so we have to cast it to be `Resource` using getter `asResource`

    ```dart
    ...
    ┣ 📂data
    ┣ 📂domain
    ┃ ┣ 📂models
    ┃ ┣ 📂repositories
    ┃ ┗ 📂usecases
    ┃ ┃ ┗ 📜register_user.dart
    ┗ 📂presentation

     class RegisterUserParams { // Define params for RegisterUser usecase.
       final String fullName;
       final String password;
       final String email;

       RegisterUserParams(
           {required this.email, required this.password, required this.fullName});
     }

     class RegisterUser extends Usecase<RegisterUserParams, RegisteredUserModel> {
       final RegistrationRepo _registrationRepo;

       RegisterUser(this._registrationRepo);
       @override
       Future<Resource<RegisteredUserModel>> execute(
           RegisterUserParams params) async {
         final registrationResult = await _registrationRepo.registerUser(
           email: params.email,
           fullName: params.fullName,
           password: params.password,
         );
         return registrationResult.asResource; //return asResource using getter method
       }
     }

    ```

7.  Create `controller/Cubit` inside presentation layer to call `usecases` and handle state or interaction in screen.

    - Prepare/create states needed to handle the interactions that occur on the screen.

      ```dart
        ...
        ┣ 📂data
        ┣ 📂domain
        ┗ 📂presentation
        ┃ ┗ 📂cubit
        ┃ ┃ ┗ 📜registration_state.dart

        abstract class RegistrationState extends Equatable {}

        class RegistrationInitial extends RegistrationState {
          @override
          List<Object> get props => [];
        }

        class RegistrationLoading extends RegistrationState {
          @override
          List<Object> get props => [];
        }

        class RegistrationSuccess extends RegistrationState {
          final RegisteredUserModel data;

          RegistrationSuccess(this.data);

          @override
          List<Object> get props => [data];
        }

        class RegistrationFailed extends RegistrationState {
          final String message;

          RegistrationFailed(this.message);

          @override
          List<Object> get props => [message];
        }
      ```

    - Create cubit to emit/controll state and call usecase.

      ```dart
        ...
        ┣ 📂data
        ┣ 📂domain
        ┗ 📂presentation
        ┃ ┗ 📂cubit
        ┃ ┃ ┣ 📜registration_cubit.dart
        ┃ ┃ ┗ 📜registration_state.dart

        class RegistrationCubit extends Cubit<RegistrationState> {
          RegistrationCubit(this._registerUserUC) : super(RegistrationInitial());

          final RegisterUser _registerUserUC; // define usecase

          void registerUser({
            required String email,
            required String password,
            required String fullName,
          }) async {
            emit(RegistrationLoading()); //emit state loading when call register user
            final usecaseCall = await _registerUserUC(RegisterUserParams(
              email: email,
              password: password,
              fullName: fullName,
            ));
            usecaseCall.when(
                success: (data, message) => emit(RegistrationSuccess(data)), //emit state success if usecase call success
                error: (exception) => emit(RegistrationFailed(exception.message))); //emit state fail if usecase call error
          }
        }
      ```

8.  Create Screen and some widgets that necessary.

    ```dart
     ...
     ┣ 📂data
     ┣ 📂domain
     ┗ 📂presentation
     ┃ ┣ 📂cubit
     ┃ ┣ 📂widgets
     ┃ ┃ ┗ 📜registration_form.dart
     ┃ ┗ 📜registration_screen.dart

     class RegistrationScreen extends StatelessWidget {
       const RegistrationScreen({Key? key}) : super(key: key);

       @override
       Widget build(BuildContext context) {
         return Scaffold(
           body: BlocProvider(
             create: (context) => getIt<RegistrationCubit>(),
             child: BlocConsumer<RegistrationCubit, RegistrationState>(
               listener: (context, state) async {
                 if (state is RegistrationSuccess) {
                   await showDialog(
                       context: context,
                       builder: (_) => Column(
                             children: [
                               const Text('Registration Success'),
                               ElevatedButton(
                                   onPressed: () {
                                     // navigate to login screen
                                   },
                                   child: const Text('Back To Login'))
                             ],
                           ));
                 }
               },
               builder: (context, state) {
                 if (state is RegistrationInitial) {
                   return const RegistrationForm();
                 }
                 if (state is RegistrationLoading) {
                   return const Center(child: CircularProgressIndicator());
                 }
                 if (state is RegistrationFailed) {
                   return Center(child: Text(state.message));
                 }
                 return const SizedBox();
               },
             ),
           ),
         );
       }
     }
    ```

9.  Register `RegistrationScreen` to the **`router`**, so we can access `RegistrationRoute` for navigation.

    - Add registration route inside **`router.dart`** file.

    ```dart
      @AdaptiveAutoRouter(replaceInRouteName: "Screen,Route", routes: [
        AutoRoute(page: LoginScreen, path: "/login", initial: true),
        AutoRoute(page: RegistrationScreen, path: "/registration"), //Put the registration route in the same level as login route (outside authenticated route)
        AutoRoute(
            path: "/main",
            page: AuthenticatedScreen,
            children: [
              AutoRoute(page: HomeScreen, path: "home", initial: true),

            ],
            initial: false),
      ])
    ```

    - Run command below in the terminal to generate new route tree.
      ```
      flutter pub run build_runner build --delete-conflicting-outputs
      ```

10. Register injection/locator to the service locator for some dependencies (in cubit, usecase, repo, datasource, etc) that we've created earlier.

    - Register `RegistrationCubit` inside **`bloc_locator.dart`**.

      ```dart
      class BlocLocator {
        static void init() {
          getIt.registerFactory(() => LoginCubit(getIt()));
          getIt.registerFactory(() => RegistrationCubit(getIt()));
        }
      }
      ```

    - Register `RegisterUser` usecase inside **`usecase_locator.dart`**.
      ```dart
      class UsecaseLocator {
        static void init() {
          getIt.registerFactory(() => Login(getIt(), getIt()));
          getIt.registerFactory(() => RegisterUser(getIt()));
        }
      }
      ```
    - Register `RegistrationRepo` inside **`repo_locator.dart`**.
      ```dart
      class RepoLocator {
        static void init() {
          getIt.registerFactory<AuthRepo>(() => AuthRepoImpl(getIt()));
          getIt.registerFactory<SessionRepo>(() => SessionRepoImpl());
          getIt.registerFactory<RegistrationRepo>(() => RegistrationRepoImpls(getIt()));
        }
      }
      ```
    - Register `RegistrationRemoteDts` inside **`datasource_locator.dart`**.
      ```dart
      class DatasourceLocator {
        static void init() {
          getIt.registerFactory<AuthRemoteDts>(() => AuthRemoteDtsImpl(getIt()));
          getIt.registerFactory<RegistrationRemoteDts>(() => RegistrationRemoteDtsImpls(getIt()));
        }
      }
      ```
### WE'RE DONE!!!

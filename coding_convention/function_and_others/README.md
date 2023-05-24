## Function

---

- **DO** name function with `camelCase`.
  **`GOOD`**

  ```dart
  void getDepartmentList(){...}
  String getDepartmentName(String id){...}
  ```

  **`BAD`**

  ```dart
  void UpdateDepartment(int id){...} // DO NOT USE PASCAL CASE FOR FUNCTION NAME
  String department_name_builder(String id){...} //DO NOT USE SNAKE CASE FOR FUNCTION NAME
  ```

- **DO** use named parameter if the function has more than 2 params
  **`GOOD`**

  ```dart
  void createNewJob({
    required String name,
    required String description,
    required String manager,
  }) //Use named params when the function params more than 2
  {
    ...
  }
  String getDepartment(String id, String departmentName){...}
  ```

  **`BAD`**

  ```dart
  void updateDepartment(int id, String name, String jobPosition ){...} // NOT USING NAMED PARAMS
  ```

- **DO** use an arrow function when the function has no extra logic, just simply return value
  **`GOOD`**

  ```dart
  String getDepartment(String id) => service.getDepartment(id); // Declare function with single line using arrow function


  String getJobPositionDetail(int departmentId, String jobName){
    final department = service.getDepartment(departmentId);
    return service.getJobPositionDetail(department.name, jobName);
  }
  ```

  **`BAD`**

  ```dart
  String getDepartment(String id){
    return service.getDepartment(id); //This function has no logic, so use arrow function instead.
  }

  ```

- **DO** use `async-await` for asyncronous function.
  **`GOOD`**

  ```dart
  Future<String> getDepartment(String id) => service.getDepartment(id);


  Future<String> getJobPositionDetail(int departmentId, String jobName)async {
    final department = await getDepartment(departmentId); //USE ASYNC-AWAIT FOR CALL ASYNCRONOUS FUNCTION
    return service.getJobPositionDetail(department.name, jobName);
  }
  ```

  **`BAD`**

  ```dart
  Future<String> getDepartment(String id) => service.getDepartment(id);

  //DO NOT USE CALLBACK FOR CALLING ASYNCRONOUS FUNCTION
  Future<String> getJobPositionDetail(int departmentId, String jobName) {
    return getDepartment(departmentId)
          .then((value) => service
            .getJobPositionDetail(value.name, jobName)
          );
  }

  ```

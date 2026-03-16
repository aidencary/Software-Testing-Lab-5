## Student Registration Demo
- Link to the Google Sheet of TCIs and test cases: https://docs.google.com/spreadsheets/d/1bkR-zW2NEIPtpa-lkLYxRMJkL61DlqCsr22NQwFGD8g/edit?usp=sharing

## Specification
- Students and Courses are the two core resources, each identified by a numeric ID.
- Students have a `name` (1–255 chars), `major` (1–255 chars), and `gpa` (0.0–4.0).
- Courses have a `name`, `instructor` (ID), `maxSize`, `room`, and a `roster` of enrolled students.
- The `addStudent` endpoint (`PUT /api/courses/addStudent/{courseId}`) adds a student to a course roster by student ID.
- If the student or course ID does not exist in the repository, a `NullStudentException` or `NullCourseException` is thrown.
- If a course's roster size would exceed `maxSize`, a `@ValidCourseCapacity` constraint violation is returned.
- All validation errors are handled globally and returned as a map of field names to error messages.
- Courses are returned as `CourseResponse` DTOs that include student names rather than raw IDs to avoid JSON recursion.

## Requirements

- Java 17+
- Maven (or included wrapper `./mvnw`)
- Newman (for Postman-based integration tests): `npm install -g newman`

## Test Files

- `StudentRegDemoFinal.postman_collection.json`: Postman collection covering full CRUD operations for Students and Courses, organized into folders: `AddingStudents`, `AddingCourses`, `GetRequests`, `DeleteRequests`, and `UpdateRequests`. Includes positive cases, boundary tests, and negative cases (marked with `*`).
- `Local.postman_environment.json`: Postman environment file defining `base_url` (`localhost:8081`), `student_id`, and `course_id` variables.
- `SRDPMCollection.json`: Alternate/legacy Postman collection.

## Setup

- Clone the project and build it with `./mvnw clean package -DskipTests`
- Run the application: `./mvnw spring-boot:run` (API runs on port 8081)
- Access the H2 console at `http://localhost:8081/h2-console` (JDBC URL: `jdbc:h2:mem:studentdb`, user: `sa`, password: `password`)

### Running Integration Tests (Newman)

- Run `mvn verify` to start the application, execute the Postman collection via Newman, and stop the application automatically.

### Running Integration Tests (Postman)

1. Import `src/test/resources/postman/StudentRegDemoFinal.postman_collection.json` into Postman
2. Also import `Local.postman_environment.json` and select it as the active environment
3. In the **Collections** sidebar, click the **⋯** (three dots) next to **StudentRegDemoFinal** and select **Run collection**

## Troubleshooting

### `'C:\Users\<name>' is not recognized as an internal or external command` during `mvn verify`

Newman is installed as a `.cmd` script (e.g., via `npm install -g newman`), which Windows cannot execute directly without going through `cmd.exe`. If the path to Newman also contains spaces, the command breaks further. The `pom.xml` exec plugin configuration works around this by invoking `cmd /c newman` instead of `newman` directly, so no manual fix is needed. If you see this error after editing `pom.xml`, make sure the `<executable>` is set to `cmd` with `/c` and `newman` as the first two `<argument>` entries.

### Newman not found (`'newman' is not recognized`)

Newman must be installed globally before running `mvn verify`:
```
npm install -g newman
```
After installing, open a new terminal so the updated `PATH` is picked up.
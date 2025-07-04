# University Data Warehouse - Data Modeling Design

This README provides an overview of the data modeling design for a university data warehouse, built to analyze student enrollment, course information, faculty details, and academic performance. The design follows a dimensional model (star schema) optimized for analytical queries using Apache Spark and Databricks.

## Overview

The data warehouse uses a star schema with one fact table and four dimension tables to support efficient querying and analysis. The design captures key university metrics, including student enrollment, course details, faculty information, and academic performance, with clear relationships and hierarchies.

## Dimensional Model Design

### 1. Fact and Dimension Tables

#### Fact Table
- **Fact_EnrollmentPerformance**: Records student enrollment and academic performance metrics.
  - **Attributes**:
    - `enrollment_id`: Unique identifier for each enrollment record (Primary Key).
    - `student_id`: References `Dim_Student` (Foreign Key).
    - `course_id`: References `Dim_Course` (Foreign Key).
    - `instructor_id`: References `Dim_Faculty` (Foreign Key).
    - `semester_id`: References `Dim_Semester` (Foreign Key).
    - `enrollment_date`: Date of enrollment.
    - `grade`: Numeric grade on a 0–100 scale.
    - `credits_earned`: Credits earned, derived from `Dim_Course`.
    - `grade_point`: Calculated as `grade * credits` for GPA computations.

#### Dimension Tables
- **Dim_Student**: Stores student information.
  - **Attributes**:
    - `student_id`: Unique identifier for a student (Primary Key).
    - `first_name`: Student's first name.
    - `last_name`: Student's last name.
    - `enrollment_year`: Year the student enrolled at the university.
    - `major`: Student's major (e.g., Computer Science, Biology).
    - `email`: Student's contact email.
    - `status`: Enrollment status (e.g., Active, Graduated).

- **Dim_Course**: Stores course details.
  - **Attributes**:
    - `course_id`: Unique identifier for a course (Primary Key).
    - `course_name`: Name of the course (e.g., Introduction to Programming).
    - `department`: Department offering the course (e.g., Computer Science).
    - `credits`: Number of credits for the course.
    - `instructor_id`: References `Dim_Faculty` (Foreign Key).
    - `course_level`: Level of the course (e.g., Undergraduate, Graduate).

- **Dim_Faculty**: Stores faculty information.
  - **Attributes**:
    - `instructor_id`: Unique identifier for a faculty member (Primary Key).
    - `first_name`: Faculty's first name.
    - `last_name`: Faculty's last name.
    - `department`: Department affiliation.
    - `position`: Faculty position (e.g., Professor, Associate Professor).
    - `hire_date`: Date the faculty member was hired.

- **Dim_Semester**: Stores semester information for temporal analysis.
  - **Attributes**:
    - `semester_id`: Unique identifier for a semester (Primary Key).
    - `semester_name`: Name of the semester (e.g., Fall 2024, Spring 2025).
    - `year`: Academic year.
    - `start_date`: Semester start date.
    - `end_date`: Semester end date.
    - `academic_year`: Derived academic year (e.g., 2024–2025).

### 2. Primary and Foreign Keys
- **Fact_EnrollmentPerformance**:
  - Primary Key: `enrollment_id`
  - Foreign Keys:
    - `student_id` → `Dim_Student(student_id)`
    - `course_id` → `Dim_Course(course_id)`
    - `instructor_id` → `Dim_Faculty(instructor_id)`
    - `semester_id` → `Dim_Semester(semester_id)`
- **Dim_Student**:
  - Primary Key: `student_id`
- **Dim_Course**:
  - Primary Key: `course_id`
  - Foreign Key: `instructor_id` → `Dim_Faculty(instructor_id)`
- **Dim_Faculty**:
  - Primary Key: `instructor_id`
- **Dim_Semester**:
  - Primary Key: `semester_id`

### 3. Hierarchies and Relationships
- **Department Hierarchy**: The `department` attribute in `Dim_Course` and `Dim_Faculty` enables aggregation by department (e.g., total enrollment or faculty count per department).
- **Time Hierarchy**: The `Dim_Semester` table supports a hierarchy of `semester_name` → `year` → `academic_year`, allowing analysis by semester, year, or academic year.
- **Faculty-Course Relationship**: The `instructor_id` foreign key in `Dim_Course` links courses to faculty, facilitating analysis of teaching loads and course assignments.

### 4. Additional Attributes and Metrics
- **Fact_EnrollmentPerformance**:
  - `grade_point`: Supports GPA calculations by multiplying `grade` by `credits_earned`.
- **Dim_Student**:
  - `status`: Tracks whether a student is Active, Graduated, or Withdrawn.
- **Dim_Course**:
  - `course_level`: Distinguishes between Undergraduate and Graduate courses for segmented analysis.
- **Dim_Semester**:
  - `academic_year`: Simplifies reporting across academic years.

## Design Rationale
- **Star Schema**: Chosen for its simplicity and performance in analytical queries, with denormalized dimension tables reducing join complexity.
- **Delta Lake**: Used for table storage to leverage ACID transactions, time travel, and optimization features in Databricks.
- **Granularity**: The fact table’s granularity is at the enrollment level (one row per student-course-semester combination), enabling detailed analysis.
- **Extensibility**: Additional attributes (e.g., `course_level`, `status`) allow for future analytical requirements without schema redesign.

## Usage
This data model is implemented in Databricks using Spark SQL and Delta Lake. Refer to the accompanying tutorial for steps to create tables, load data, optimize performance, and run analytical queries. The schema supports queries such as:
- Calculating average grades per course.
- Identifying courses with the highest enrollment.
- Summing credits taken by students per semester.
- Determining faculty teaching loads by department.

## Contributing
To contribute to the data model design:
1. Propose changes by opening an issue or pull request.
2. Suggest additional attributes, hierarchies, or fact tables to support new analytical needs.
3. Ensure compatibility with Delta Lake and Spark SQL.

## License
This data model design is provided under the MIT License. See the LICENSE file for details.

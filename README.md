# Student-grading-system-using-sql
A relational SQL database project for managing student enrollments, course assignments, and automated grade tracking.
# 🗄️ Database Schema & Architecture
The database consists of 5 primary tables designed with entity integrity and referential constraints:students: Stores core profile data (student_id, first_name, last_name, email, enrollment_date, department).courses: Tracks academic course offerings (course_id, course_name, department, credits, instructor_id).enrollments: Maps students to enrolled courses across semesters (enrollment_id, student_id, course_id, semester, academic_year).assignments: Defines individual tests, projects, and weightages (assignment_id, course_id, title, max_score, weightage).grades: Stores raw assessment scores (grade_id, enrollment_id, assignment_id, score_obtained).

# 📊 Business Logic & Analysis Tasks
The analytical queries (03_analysis_tasks.sql) perform key operational and reporting tasks:Data Maintenance (DML): Create new entries, update student records, and purge invalid test records.   Student Performance Tracking: Multi-table JOIN operations to extract full score breakdowns per student.   Enrollment Aggregations: GROUP BY and HAVING queries identifying high-workload students enrolled in multiple classes.   Calculated Summaries (CTAS): Generate performance summary tables calculating weighted final course grades.   Departmental & Class Metrics: Grouped aggregations evaluating average scores and submission volume per assignment.   Submission Auditing: LEFT JOIN queries flagging missing or ungraded assignment submissions.   

# 🚀 How to Run the Project
Prerequisites- Any SQL Database Engine (PostgreSQL, MySQL, SQL Server, SQLite)MySQL Workbench, pgAdmin, or DBeaver

# CODE
```sql
-- ===================================================
-- DATABASE CREATION & SETUP
-- ===================================================
CREATE DATABASE student_grading_system;
USE student_grading_system;

-- ===================================================
-- 1. DATABASE SCHEMA CREATION (DDL)
-- ===================================================

CREATE TABLE students (
    student_id VARCHAR(10) PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    enrollment_date DATE,
    department VARCHAR(50)
);

CREATE TABLE courses (
    course_id VARCHAR(10) PRIMARY KEY,
    course_name VARCHAR(100) NOT NULL,
    department VARCHAR(50),
    credits INT,
    instructor_id VARCHAR(10)
);

CREATE TABLE enrollments (
    enrollment_id VARCHAR(10) PRIMARY KEY,
    student_id VARCHAR(10) REFERENCES students(student_id) ON DELETE CASCADE,
    course_id VARCHAR(10) REFERENCES courses(course_id) ON DELETE CASCADE,
    semester VARCHAR(10),
    academic_year INT
);

CREATE TABLE assignments (
    assignment_id VARCHAR(10) PRIMARY KEY,
    course_id VARCHAR(10) REFERENCES courses(course_id) ON DELETE CASCADE,
    title VARCHAR(100) NOT NULL,
    max_score DECIMAL(5,2) DEFAULT 100.00,
    weightage DECIMAL(3,2) CHECK (weightage > 0 AND weightage <= 1.00)
);

CREATE TABLE grades (
    grade_id VARCHAR(10) PRIMARY KEY,
    enrollment_id VARCHAR(10) REFERENCES enrollments(enrollment_id) ON DELETE CASCADE,
    assignment_id VARCHAR(10) REFERENCES assignments(assignment_id) ON DELETE CASCADE,
    score_obtained DECIMAL(5,2) CHECK (score_obtained >= 0)
);

-- ===================================================
-- 2. SAMPLE DATA INSERTION (DML)
-- ===================================================

INSERT INTO students VALUES 
('S101', 'Aarav', 'Sharma', 'aarav.s@univ.edu', '2023-08-15', 'Computer Science'),
('S102', 'Priya', 'Patel', 'priya.p@univ.edu', '2023-08-15', 'Data Science'),
('S103', 'Rohan', 'Gupta', 'rohan.g@univ.edu', '2024-01-10', 'Computer Science'),
('S104', 'Ananya', 'Singh', 'ananya.s@univ.edu', '2023-08-15', 'Mathematics');

INSERT INTO courses VALUES 
('C101', 'Database Systems', 'Computer Science', 4, 'E201'),
('C102', 'Data Structures', 'Computer Science', 4, 'E202'),
('C103', 'Linear Algebra', 'Mathematics', 3, 'E203');

INSERT INTO enrollments VALUES 
('E101', 'S101', 'C101', 'Fall', 2024),
('E102', 'S102', 'C101', 'Fall', 2024),
('E103', 'S103', 'C101', 'Fall', 2024),
('E104', 'S101', 'C103', 'Fall', 2024);

INSERT INTO assignments VALUES 
('A1', 'C101', 'Midterm Exam', 100.00, 0.30),
('A2', 'C101', 'Final Exam', 100.00, 0.50),
('A3', 'C101', 'SQL Project', 100.00, 0.20);

INSERT INTO grades VALUES 
('G1', 'E101', 'A1', 88.5),
('G2', 'E101', 'A2', 92.0),
('G3', 'E101', 'A3', 95.0),
('G4', 'E102', 'A1', 74.0),
('G5', 'E102', 'A2', 81.0);

-- ===================================================
-- 3. ANALYSIS & DATA MANIPULATION TASKS
-- ===================================================

-- Initial Verification
SELECT * FROM students;
SELECT * FROM courses;
SELECT * FROM enrollments;
SELECT * FROM assignments;
SELECT * FROM grades;

-- Task 1: Create a New Student Record
INSERT INTO students (student_id, first_name, last_name, email, enrollment_date, department)
VALUES ('S105', 'Kavya', 'Verma', 'kavya.v@univ.edu', '2024-08-15', 'Computer Science');

-- Task 2: Update an Existing Student's Email
UPDATE students
SET email = 'aarav.sharma_new@univ.edu'
WHERE student_id = 'S101';

-- Task 3: Delete a Specific Grade Record
DELETE FROM grades
WHERE grade_id = 'G5';

-- Task 4: Retrieve All Grades Earned by a Specific Student
SELECT 
    s.student_id, 
    s.first_name, 
    s.last_name, 
    c.course_name, 
    a.title, 
    g.score_obtained
FROM grades g
JOIN enrollments e ON g.enrollment_id = e.enrollment_id
JOIN students s ON e.student_id = s.student_id
JOIN assignments a ON g.assignment_id = a.assignment_id
JOIN courses c ON e.course_id = c.course_id
WHERE s.student_id = 'S101';

-- Task 5: List Students Who Are Enrolled in More Than One Course
SELECT 
    s.student_id, 
    s.first_name, 
    s.last_name, 
    COUNT(e.course_id) AS total_en

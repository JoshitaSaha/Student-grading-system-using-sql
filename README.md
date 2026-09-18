# Student-grading-system-using-sql
A relational SQL database project for managing student enrollments, course assignments, and automated grade tracking.
# 🗄️ Database Schema & Architecture
The database consists of 5 primary tables designed with entity integrity and referential constraints:students: Stores core profile data (student_id, first_name, last_name, email, enrollment_date, department).courses: Tracks academic course offerings (course_id, course_name, department, credits, instructor_id).enrollments: Maps students to enrolled courses across semesters (enrollment_id, student_id, course_id, semester, academic_year).assignments: Defines individual tests, projects, and weightages (assignment_id, course_id, title, max_score, weightage).grades: Stores raw assessment scores (grade_id, enrollment_id, assignment_id, score_obtained).

# 📊 Business Logic & Analysis Tasks
The analytical queries (03_analysis_tasks.sql) perform key operational and reporting tasks:Data Maintenance (DML): Create new entries, update student records, and purge invalid test records.   Student Performance Tracking: Multi-table JOIN operations to extract full score breakdowns per student.   Enrollment Aggregations: GROUP BY and HAVING queries identifying high-workload students enrolled in multiple classes.   Calculated Summaries (CTAS): Generate performance summary tables calculating weighted final course grades.   Departmental & Class Metrics: Grouped aggregations evaluating average scores and submission volume per assignment.   Submission Auditing: LEFT JOIN queries flagging missing or ungraded assignment submissions.   

# 🚀 How to Run the Project
Prerequisites- Any SQL Database Engine (PostgreSQL, MySQL, SQL Server, SQLite)MySQL Workbench, pgAdmin, or DBeaver

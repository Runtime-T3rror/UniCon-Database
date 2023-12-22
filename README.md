# UniCon-Database

UniCon-Database is a repo containing all info related to UniCon's database

## Database Schema

### Subject Table

- `id`: SERIAL PRIMARY KEY
- `name`: VARCHAR(255) NOT NULL
- `shortname`: VARCHAR(10) NOT NULL UNIQUE

### Faculty Table

- `id`: SERIAL PRIMARY KEY
- `name`: VARCHAR(255) NOT NULL UNIQUE
- `shortname`: VARCHAR(3) NOT NULL UNIQUE
- `subject_id`: INTEGER REFERENCES subject(id) NOT NULL

### Department Table

- `id`: SERIAL PRIMARY KEY
- `department_name`: VARCHAR(255) NOT NULL UNIQUE
- `hod_id`: INTEGER REFERENCES faculty(id) NOT NULL UNIQUE

### Batch Table

- `id`: SERIAL PRIMARY KEY
- `batch_name`: VARCHAR(255) NOT NULL
- `dep_id`: INTEGER REFERENCES department(id) NOT NULL

### Time Slot Table

- `id`: SERIAL PRIMARY KEY
- `start_time`: TIME NOT NULL
- `end_time`: TIME NOT NULL
- UNIQUE(start_time, end_time)

### Room Table

- `id`: SERIAL PRIMARY KEY
- `room_no`: INTEGER NOT NULL
- `room_type`: room_type NOT NULL
- UNIQUE(room_no, room_type)

### Timetable Table

- `id`: SERIAL PRIMARY KEY
- `time_id`: INTEGER REFERENCES time_slot(id) NOT NULL
- `day`: day_of_week NOT NULL
- `bat_id`: INTEGER REFERENCES batch(id) NOT NULL
- `fac_id`: INTEGER REFERENCES faculty(id) NOT NULL
- `room_id`: INTEGER REFERENCES room(id) NOT NULL
- UNIQUE(time_id, bat_id, fac_id, room_id)

### Proxy Timetable Table

- `id`: SERIAL PRIMARY KEY
- `time_id`: INTEGER REFERENCES time_slot(id) NOT NULL
- `day`: day_of_week NOT NULL
- `bat_id`: INTEGER REFERENCES batch(id) NOT NULL
- `fac_id`: INTEGER REFERENCES faculty(id) NOT NULL
- `room_id`: INTEGER REFERENCES room(id) NOT NULL
- UNIQUE(time_id, bat_id, fac_id, room_id)

### Degree Table

- `id`: SERIAL PRIMARY KEY
- `degree_name`: VARCHAR(100) UNIQUE NOT NULL

### Branch Table

- `id`: SERIAL PRIMARY KEY
- `branch_name`: VARCHAR(10) UNIQUE NOT NULL
- `degree_id`: INTEGER REFERENCES degree(id) NOT NULL

### Exam Timetable Table

- `id`: SERIAL PRIMARY KEY
- `exam_date`: DATE NOT NULL
- `sem`: INTEGER NOT NULL
- `test_phase`: test_phase NOT NULL
- `subject_id`: INTEGER REFERENCES subject(id) NOT NULL
- `branch_id`: INTEGER REFERENCES branch(id) NOT NULL
- `time_id`: INTEGER REFERENCES time_slot(id) NOT NULL
- UNIQUE(exam_date, subject_id, branch_id, sem)

### Basic Student Details Table

- `id`: SERIAL PRIMARY KEY
- `enrollment_no`: BIGINT NOT NULL UNIQUE
- `student_name`: VARCHAR(50) NOT NULL
- `branch_id`: INTEGER REFERENCES branch(id) NOT NULL

### Dynamic Student Details Table

- `id`: SERIAL PRIMARY KEY
- `batch_id`: INTEGER REFERENCES batch(id)
- `student_id`: INTEGER REFERENCES basic_student_details(id)
- `sem`: INTEGER NOT NULL
- `roll_no`: INTEGER NOT NULL

## SQL Script

```sql
-- Create Subject Table
CREATE TABLE subject (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    shortname VARCHAR(10) NOT NULL UNIQUE
);

-- Create Faculty Table
CREATE TABLE faculty (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL UNIQUE,
    shortname VARCHAR(3) NOT NULL UNIQUE,
    subject_id INTEGER REFERENCES subject(id) NOT NULL
);

-- Create Department Table
CREATE TABLE department (
    id SERIAL PRIMARY KEY,
    department_name VARCHAR(255) NOT NULL UNIQUE,
    hod_id INTEGER REFERENCES faculty(id) NOT NULL UNIQUE
);

-- Create Batch Table
CREATE TABLE batch (
    id SERIAL PRIMARY KEY,
    batch_name VARCHAR(255) NOT NULL,
    dep_id INTEGER REFERENCES department(id) NOT NULL
);

-- Create Time Slot Table
CREATE TABLE time_slot (
    id SERIAL PRIMARY KEY,
    start_time TIME NOT NULL,
    end_time TIME NOT NULL,
    UNIQUE(start_time, end_time)
);

-- Create Room Table
CREATE TYPE room_type AS ENUM('Classroom', 'Physics-Lab', 'Computer-Lab');

CREATE TABLE room (
    id SERIAL PRIMARY KEY,
    room_no INTEGER NOT NULL,
    room_type room_type NOT NULL,
    UNIQUE(room_no, room_type)
);

-- Create Timetable Table
CREATE TYPE day_of_week AS ENUM ('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday');

CREATE TABLE timetable (
    id SERIAL PRIMARY KEY,
    time_id INTEGER REFERENCES time_slot(id) NOT NULL,
    day day_of_week NOT NULL,
    bat_id INTEGER REFERENCES batch(id) NOT NULL,
    fac_id INTEGER REFERENCES faculty(id) NOT NULL,
    room_id INTEGER REFERENCES room(id) NOT NULL,
    UNIQUE(time_id, bat_id, fac_id, room_id)
);

-- Repeat the process for the remaining tables

-- Create Degree Table
CREATE TABLE degree (
    id SERIAL PRIMARY KEY,
    degree_name VARCHAR(100) UNIQUE NOT NULL
);

-- Create Branch Table
CREATE TABLE branch (
    id SERIAL PRIMARY KEY,
    branch_name VARCHAR(10) UNIQUE NOT NULL,
    degree_id INTEGER REFERENCES degree(id) NOT NULL
);

-- Create Exam Timetable Table
CREATE TYPE test_phase AS ENUM('t1','t2','t3','t4','t5','test','mid-sem','end-sem');

CREATE TABLE exam_timetable (
    id SERIAL PRIMARY KEY,
    exam_date DATE NOT NULL,
    sem INTEGER NOT NULL,
    test_phase test_phase NOT NULL,
    subject_id INTEGER REFERENCES subject(id) NOT NULL,
    branch_id INTEGER REFERENCES branch(id) NOT NULL,
    time_id INTEGER REFERENCES time_slot(id) NOT NULL,
    UNIQUE(exam_date, subject_id, branch_id, sem)
);

-- Create Basic Student Details Table
CREATE TABLE basic_student_details (
    id SERIAL PRIMARY KEY,
    enrollment_no BIGINT NOT NULL UNIQUE,
    student_name VARCHAR(50) NOT NULL,
    branch_id INTEGER REFERENCES branch(id) NOT NULL
);

-- Create Dynamic Student Details Table
CREATE TABLE dynamic_student_details (
    id SERIAL PRIMARY KEY,
    batch_id INTEGER REFERENCES batch(id),
    student_id INTEGER REFERENCES basic_student_details(id),
    sem INTEGER NOT NULL,
    roll_no INTEGER NOT NULL
);

-- End of SQL script

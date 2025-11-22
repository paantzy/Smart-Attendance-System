# SmartAttend Database Schema

## Database: Supabase (PostgreSQL)

---

## Entity Relationship Diagram

```
┌─────────────────────┐
│       USERS         │
├─────────────────────┤
│ id (PK)            │
│ email              │
│ role               │
│ child_id (FK)      │──┐
│ created_at         │  │
└─────────────────────┘  │
         │               │
         │ teaches       │ parent of
         │               │
         ▼               ▼
┌─────────────────────────────┐     ┌─────────────────────────┐
│  CLASS_TEACHER_STUDENT      │     │      STUDENTS           │
├─────────────────────────────┤     ├─────────────────────────┤
│ id (PK)                     │     │ student_id (PK)         │
│ class_name                  │     │ student_name            │
│ form                        │     │ grade                   │
│ teacher_id (FK) ────────────┤     │ class_name              │
│ student_id (FK) ────────────┼─────│ parent_contact          │
│ created_at                  │     │ email_student           │
└─────────────────────────────┘     │ qr_code                 │
                                    │ created_at              │
                                    └─────────────────────────┘
                                             │
                                             │ has
                                             ▼
┌─────────────────────────────┐     ┌─────────────────────────┐
│    CLASS_SESSIONS           │     │  ATTENDANCE_RECORDS     │
├─────────────────────────────┤     ├─────────────────────────┤
│ id (PK)                     │     │ id (PK)                 │
│ class_name                  │     │ student_id (FK) ────────┘
│ form                        │     │ class_name              │
│ teacher_id (FK) ────────────┤     │ check_in_time           │
│ start_time                  │     │ check_out_time          │
│ end_time                    │     │ status                  │
│ status                      │     │ teacher_name            │
│ created_at                  │     │ teacher_id (FK)         │
└─────────────────────────────┘     │ created_at              │
         │                          │ updated_at              │
         │ contains                 └─────────────────────────┘
         └──────────────────────────────────┘
```

---

## Table Definitions

### 1. **users**
Stores authentication information for teachers and parents.

| Column      | Type      | Constraints | Description                          |
|-------------|-----------|-------------|--------------------------------------|
| id          | uuid      | PRIMARY KEY | Unique user identifier               |
| email       | text      | UNIQUE, NOT NULL | User email address            |
| role        | text      | NOT NULL    | User role (teacher/parent)           |
| child_id    | uuid      | FOREIGN KEY | References students(student_id)      |
| created_at  | timestamp | DEFAULT NOW | Account creation timestamp           |

**Relationships:**
- One-to-One: Users (parent) → Students (via child_id)
- One-to-Many: Users (teacher) → Class_Teacher_Student
- One-to-Many: Users (teacher) → Class_Sessions
- One-to-Many: Users (teacher) → Attendance_Records

**Indexes:**
- Primary: id
- Unique: email
- Foreign: child_id → students(student_id)

---

### 2. **students**
Stores student personal information and QR codes.

| Column         | Type      | Constraints | Description                          |
|----------------|-----------|-------------|--------------------------------------|
| student_id     | uuid      | PRIMARY KEY | Unique student identifier            |
| student_name   | text      | NOT NULL    | Student full name                    |
| grade          | text      |             | Student's form/grade level           |
| class_name     | text      |             | Student's class                      |
| parent_contact | text      |             | Parent phone number                  |
| email_student  | text      |             | Student email address                |
| qr_code        | text      | UNIQUE      | Unique QR code string                |
| created_at     | timestamp | DEFAULT NOW | Record creation timestamp            |

**Relationships:**
- One-to-Many: Students → Attendance_Records
- Many-to-Many: Students ↔ Classes (via Class_Teacher_Student)
- One-to-One: Students → Users (parent, via users.child_id)

**Indexes:**
- Primary: student_id
- Unique: qr_code

---

### 3. **attendance_records**
Stores all attendance check-in and check-out records.

| Column         | Type      | Constraints | Description                          |
|----------------|-----------|-------------|--------------------------------------|
| id             | uuid      | PRIMARY KEY | Unique record identifier             |
| student_id     | uuid      | FOREIGN KEY | References students(student_id)      |
| class_name     | text      |             | Class name for this attendance       |
| check_in_time  | timestamp |             | Check-in timestamp                   |
| check_out_time | timestamp |             | Check-out timestamp                  |
| status         | text      |             | Status: present/absent/late          |
| teacher_name   | text      |             | Name of teacher                      |
| teacher_id     | uuid      | FOREIGN KEY | References users(id)                 |
| created_at     | timestamp | DEFAULT NOW | Record creation timestamp            |
| updated_at     | timestamp | DEFAULT NOW | Last update timestamp                |

**Relationships:**
- Many-to-One: Attendance_Records → Students
- Many-to-One: Attendance_Records → Users (teacher)

**Indexes:**
- Primary: id
- Foreign: student_id → students(student_id)
- Foreign: teacher_id → users(id)
- Index: status (for filtering)
- Index: check_in_time (for date queries)

**Business Rules:**
- status can be: 'present', 'absent', 'late'
- Absent records may have NULL check_in_time and check_out_time
- check_out_time can be NULL if student hasn't checked out yet

---

### 4. **class_teacher_student**
Junction table linking classes, teachers, and students.

| Column      | Type      | Constraints | Description                          |
|-------------|-----------|-------------|--------------------------------------|
| id          | uuid      | PRIMARY KEY | Unique record identifier             |
| class_name  | text      | NOT NULL    | Name of the class                    |
| form        | text      |             | Form/grade level                     |
| teacher_id  | uuid      | FOREIGN KEY | References users(id)                 |
| student_id  | uuid      | FOREIGN KEY | References students(student_id)      |
| created_at  | timestamp | DEFAULT NOW | Record creation timestamp            |

**Relationships:**
- Many-to-One: Class_Teacher_Student → Users (teacher)
- Many-to-One: Class_Teacher_Student → Students

**Indexes:**
- Primary: id
- Foreign: teacher_id → users(id)
- Foreign: student_id → students(student_id)
- Composite: (class_name, student_id) for unique enrollment

**Business Rules:**
- A student can be enrolled in multiple classes
- A class can have multiple students
- Each class-student combination is unique

---

### 5. **class_sessions**
Stores active and historical class sessions for QR scanning.

| Column      | Type      | Constraints | Description                          |
|-------------|-----------|-------------|--------------------------------------|
| id          | uuid      | PRIMARY KEY | Unique session identifier            |
| class_name  | text      | NOT NULL    | Class name                           |
| form        | text      |             | Form/grade level                     |
| teacher_id  | uuid      | FOREIGN KEY | References users(id)                 |
| start_time  | timestamp | DEFAULT NOW | Session start time                   |
| end_time    | timestamp |             | Session end time                     |
| status      | text      | DEFAULT 'active' | Status: active/ended            |
| created_at  | timestamp | DEFAULT NOW | Record creation timestamp            |

**Relationships:**
- Many-to-One: Class_Sessions → Users (teacher)
- One-to-Many: Class_Sessions → Attendance_Records (conceptual)

**Indexes:**
- Primary: id
- Foreign: teacher_id → users(id)
- Index: status (for filtering active sessions)

**Business Rules:**
- Only one active session per class at a time
- status can be: 'active', 'ended'
- end_time is NULL while session is active

---

## Data Flow Patterns

### 1. User Login Flow
```
users table
  ↓ (verify email and role)
Returns: user id, email, role, child_id
```

### 2. Attendance Recording Flow
```
QR Scan → students table (verify student_id)
  ↓
class_sessions table (verify active session)
  ↓
attendance_records table (INSERT check_in_time)
  ↓
Real-time update to UI
```

### 3. Parent Dashboard Flow
```
users table (get child_id)
  ↓
students table (get student info)
  ↓
attendance_records table (filter by student_id)
  ↓
Display attendance history with charts
```

### 4. Teacher Class Management Flow
```
class_teacher_student table
  ↓ (JOIN)
students table
  ↓ (JOIN)
users table (teacher info)
  ↓
Display class roster with student details
```

---

## Row Level Security (RLS) Policies

### users table
- Teachers can read their own record
- Parents can read their own record
- No public insert/update/delete

### students table
- Teachers can read all students
- Parents can only read their child's record (via users.child_id)
- Teachers can insert/update/delete students

### attendance_records table
- Teachers can read/write all records
- Parents can only read records where student_id matches their child_id
- Teachers can insert/update records

### class_teacher_student table
- Teachers can read/write for their classes
- Parents can read for their child only

### class_sessions table
- Teachers can read/write their own sessions
- Parents cannot access

---

## Sample Queries

### Get student attendance for a specific date
```sql
SELECT ar.*, s.student_name, s.class_name
FROM attendance_records ar
JOIN students s ON ar.student_id = s.student_id
WHERE DATE(ar.check_in_time) = '2025-11-22'
  AND ar.status = 'present'
ORDER BY ar.check_in_time DESC;
```

### Get all students in a teacher's class
```sql
SELECT s.*, cts.class_name, cts.form
FROM students s
JOIN class_teacher_student cts ON s.student_id = cts.student_id
WHERE cts.teacher_id = '<teacher_uuid>'
ORDER BY s.student_name;
```

### Get parent's child attendance history
```sql
SELECT ar.*, s.student_name
FROM attendance_records ar
JOIN students s ON ar.student_id = s.student_id
JOIN users u ON u.child_id = s.student_id
WHERE u.id = '<parent_uuid>'
ORDER BY ar.check_in_time DESC;
```

### Count present/absent/late for today
```sql
SELECT 
  status,
  COUNT(*) as count
FROM attendance_records
WHERE DATE(check_in_time) = CURRENT_DATE
GROUP BY status;
```

---

## Backup & Maintenance

### Recommended Backup Strategy
- Daily automated backups via Supabase
- Weekly manual exports for critical tables
- Retain backups for 30 days minimum

### Data Retention
- Attendance records: Retain indefinitely for historical analysis
- Class sessions: Archive after 1 year
- Students: Soft delete (mark as inactive) rather than hard delete

---

**Database Version**: PostgreSQL 15 (Supabase)  
**Last Schema Update**: November 2025  
**Timezone**: Asia/Kuala_Lumpur (all timestamps)

# SmartAttend - QR Code Based Attendance Management System

## Project Overview

**SmartAttend** is a mobile application built with React Native and Expo that enables efficient student attendance management using QR code technology. The system supports role-based access for Teachers and Parents, providing real-time attendance tracking, analytics, and notifications.

## Technology Stack

### Frontend
- **Framework**: React Native 0.81.5
- **SDK**: Expo SDK 54
- **Language**: TypeScript 5.9.2
- **Navigation**: Expo Router 6.0.14
- **UI Components**: React Native Gesture Handler, Reanimated
- **Charts**: react-native-chart-kit
- **QR Code**: react-native-qrcode-svg

### Backend
- **Database**: Supabase (PostgreSQL)
- **Authentication**: Supabase Auth
- **Real-time**: Supabase Realtime subscriptions

### Development Tools
- **Package Manager**: npm
- **Build Tool**: EAS Build
- **Development**: Expo Go for testing

## Key Features

### For Teachers
1. **Dashboard**
   - Real-time attendance statistics
   - 7-day attendance trend chart
   - Pie chart showing present/absent/late distribution
   - Recent class sessions overview

2. **QR Code Scanner**
   - Start/stop class sessions
   - Scan student QR codes for check-in/check-out
   - Real-time attendance recording
   - Student list with attendance status

3. **Student Management**
   - Add new students with details (name, grade, class, contact)
   - Edit student information
   - Delete students
   - Sync attendance records
   - Generate unique QR codes for each student

4. **Class Management**
   - Create new classes with form and teacher assignment
   - View class details and enrolled students
   - Delete classes

5. **Attendance Status**
   - View all attendance records
   - Filter by form, class, and date
   - Display check-in/check-out times
   - Special display for absent students showing date and details

6. **Reports & Analytics**
   - Weekly attendance trend chart
   - Student status by week chart
   - Filter by form, class, and month
   - Visual analytics with charts

7. **Student QR Codes**
   - View all student QR codes
   - Filter by form and class
   - Display student details with QR code

### For Parents
1. **Dashboard**
   - Child's attendance statistics
   - Attendance rate percentage
   - Pie chart showing attendance distribution
   - 7-day attendance trend
   - Recent attendance records with detailed information
   - Special display for absent days with date

2. **Attendance History**
   - Complete attendance history for child
   - Check-in and check-out times
   - Teacher and class information
   - Absent records with date display

## Database Schema

### Tables

#### 1. users
Stores user authentication and profile information
- `id` (uuid, PK): User unique identifier
- `email` (text): User email address
- `role` (text): User role (teacher/parent)
- `child_id` (uuid, FK): Reference to student (for parents)
- `created_at` (timestamp): Account creation time

#### 2. students
Stores student information
- `student_id` (uuid, PK): Student unique identifier
- `student_name` (text): Student full name
- `grade` (text): Student's form/grade
- `class_name` (text): Student's class
- `parent_contact` (text): Parent phone number
- `email_student` (text): Student email
- `qr_code` (text): Unique QR code string
- `created_at` (timestamp): Record creation time

#### 3. attendance_records
Stores attendance check-in/check-out records
- `id` (uuid, PK): Record unique identifier
- `student_id` (uuid, FK): Reference to student
- `class_name` (text): Class name
- `check_in_time` (timestamp): Check-in timestamp
- `check_out_time` (timestamp): Check-out timestamp
- `status` (text): Attendance status (present/absent/late)
- `teacher_name` (text): Teacher name
- `teacher_id` (uuid, FK): Reference to teacher
- `created_at` (timestamp): Record creation time
- `updated_at` (timestamp): Last update time

#### 4. class_teacher_student
Junction table linking classes, teachers, and students
- `id` (uuid, PK): Record unique identifier
- `class_name` (text): Class name
- `form` (text): Class form/grade
- `teacher_id` (uuid, FK): Reference to teacher
- `student_id` (uuid, FK): Reference to student
- `created_at` (timestamp): Record creation time

#### 5. class_sessions
Stores active class sessions for QR scanning
- `id` (uuid, PK): Session unique identifier
- `class_name` (text): Class name
- `form` (text): Class form/grade
- `teacher_id` (uuid, FK): Reference to teacher
- `start_time` (timestamp): Session start time
- `end_time` (timestamp): Session end time
- `status` (text): Session status (active/ended)
- `created_at` (timestamp): Session creation time

## Application Architecture

### Authentication Flow
1. User launches app
2. Checks for existing session
3. If not authenticated, shows login screen
4. User selects role (Teacher/Parent) and enters credentials
5. System validates against Supabase Auth
6. Verifies role matches user type in database
7. Redirects to appropriate dashboard

### Attendance Recording Flow
1. Teacher starts a class session
2. Selects form and class
3. Camera activates for QR scanning
4. Teacher scans student QR code
5. System validates QR code
6. Records check-in time in database
7. Sends notification to parent (if configured)
8. Updates student list in real-time
9. For check-out, scans QR code again

### Absent Student Handling
- System displays absent records with:
  - Red "ABSENT" badge
  - Date of absence
  - "No check-in/check-out recorded" message
  - Class and form information
  - Teacher name
- Special conditional rendering for absent vs present/late records

## Security Features
- Role-based access control (RBAC)
- Row Level Security (RLS) in Supabase
- Secure authentication with Supabase Auth
- Parent can only view their own child's data
- Teacher can only manage their assigned classes

## Timezone
All timestamps use Malaysia timezone (Asia/Kuala_Lumpur) via moment-timezone

## Installation & Setup

### Prerequisites
- Node.js 16+
- npm or yarn
- Expo Go app on mobile device
- Supabase account

### Installation Steps
1. Extract the source code
2. Install dependencies: `npm install`
3. Configure environment variables in `.env`
4. Start development server: `npx expo start`
5. Scan QR code with Expo Go app

### Building APK
- Run: `eas build --platform android --profile preview`
- Download APK from EAS build dashboard

## Project Structure
```
SAAFYP/
├── app/                    # Main application screens
│   ├── (tabs)/            # Tab-based screens
│   │   ├── dashboard.tsx         # Teacher dashboard
│   │   ├── parent-dashboard.tsx  # Parent dashboard
│   │   ├── scanner.tsx           # QR code scanner
│   │   ├── students.tsx          # Student management
│   │   ├── classes.tsx           # Class management
│   │   ├── attendance.tsx        # Attendance status
│   │   ├── attendance-history.tsx # Parent attendance history
│   │   ├── reports.tsx           # Reports & analytics
│   │   └── qr-codes.tsx          # Student QR codes
│   ├── login.tsx          # Login screen
│   ├── register.tsx       # Teacher registration
│   └── parent-register.tsx # Parent registration
├── components/            # Reusable UI components
├── contexts/              # React contexts (Auth, Notification)
├── utils/                 # Utility functions
│   ├── supabase.ts       # Supabase client
│   ├── studentOperations.ts   # Student CRUD
│   ├── attendanceOperations.ts # Attendance CRUD
│   ├── classOperations.ts     # Class CRUD
│   └── qrCodeGenerator.ts     # QR code generation
├── styles/               # Common styling
└── constants/            # App constants
```

## Screenshots Reference
Key screens to capture:
1. Login page with role selection
2. Teacher dashboard with statistics and charts
3. QR code scanner with active session
4. Student management list
5. Class management view
6. Attendance status with filters
7. Reports with charts
8. Parent dashboard
9. Parent attendance history
10. Student QR codes display

## Future Enhancements
- Push notifications for parents
- Export reports to PDF
- Biometric authentication
- Offline mode support
- Multi-language support
- Advanced analytics and insights

## Developer Notes
- Use `npx expo start --clear --lan` for clean development server
- QR codes are unique per student and stored in database
- Absent records require special handling in UI
- All database operations use Supabase client
- Real-time updates via Supabase subscriptions

## Contact & Support
This project was developed as part of a Final Year Project for school attendance management.

---
**Version**: 1.0  
**Last Updated**: November 2025  
**Platform**: Android (React Native + Expo)

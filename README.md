# 📱 SmartAttend - QR Code Based Attendance Management System

> A modern mobile application for efficient student attendance tracking using QR code technology

[![Platform](https://img.shields.io/badge/Platform-Android-green.svg)](https://www.android.com/)
[![Framework](https://img.shields.io/badge/Framework-React%20Native-blue.svg)](https://reactnative.dev/)
[![Expo](https://img.shields.io/badge/Expo-SDK%2054-black.svg)](https://expo.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.9.2-blue.svg)](https://www.typescriptlang.org/)
[![License](https://img.shields.io/badge/License-Academic-red.svg)](LICENSE)

---

## 📋 Table of Contents

- [Overview]
- [Features]
- [Technology Stack]
- [Installation]
- [Usage]
- [Database Schema]
- [Project Structure]
- [Project Documentation]
- [Development]
- [Building APK]
- [Contributing]

---

## 🎯 Overview

**SmartAttend** is a comprehensive mobile attendance management system designed for educational institutions. It leverages QR code technology to provide fast, accurate, and paperless attendance tracking. The application supports two user roles:

- **👨‍🏫 Teachers**: Manage students, classes, scan QR codes, and generate reports
- **👨‍👩‍👧 Parents**: View their child's attendance history and receive updates

The system provides real-time synchronization, analytics dashboards, and detailed reporting capabilities.

---

## ✨ Features

### For Teachers 👨‍🏫

#### 📊 Dashboard
- Real-time attendance statistics (Present, Absent, Late)
- Interactive pie chart showing attendance distribution
- 7-day attendance trend line chart
- Recent class sessions overview
- Quick access to all features

#### 📸 QR Code Scanner
- Start/Stop class sessions
- Real-time QR code scanning via camera
- Instant check-in and check-out recording
- Visual feedback for successful scans
- Student list with real-time status updates

#### 👥 Student Management
- Add new students with comprehensive details
- Edit existing student information
- Delete students (with confirmation)
- Auto-generate unique QR codes for each student
- Sync attendance records
- Filter and search capabilities

#### 📚 Class Management
- Create classes with form and teacher assignment
- View class details and enrolled students
- Manage class rosters
- Delete classes when needed

#### 📋 Attendance Status
- View all attendance records in one place
- Advanced filtering by:
  - Form/Grade level
  - Class name
  - Date range
- Detailed display of check-in/check-out times
- Special highlighting for absent students
- Export capabilities

#### 📈 Reports & Analytics
- Weekly attendance trend visualization
- Student status breakdown by week
- Filter reports by form, class, and month
- Interactive charts and graphs
- Statistical insights

#### 🎫 Student QR Codes
- View all student QR codes
- Filter by form and class
- Display student details alongside QR code
- Ready for printing or sharing

### For Parents 👨‍👩‍👧

#### 🏠 Dashboard
- Child's overall attendance statistics
- Attendance rate percentage with color coding
- Pie chart showing attendance distribution
- 7-day attendance trend
- Recent attendance records with full details
- Visual indicators for present/absent/late

#### 📜 Attendance History
- Complete attendance history
- Check-in and check-out timestamps
- Teacher and class information for each record
- Date-wise organization
- Special display for absent days

---

## 🛠 Technology Stack

### Frontend
| Technology | Version | Purpose |
|------------|---------|---------|
| React Native | 0.81.5 | Mobile app framework |
| Expo | SDK 54 | Development platform |
| TypeScript | 5.9.2 | Type safety |
| Expo Router | 6.0.14 | Navigation |
| React Native Chart Kit | - | Data visualization |
| React Native QRCode SVG | - | QR code generation |
| Moment Timezone | - | Date/time handling |

### Backend
| Technology | Purpose |
|------------|---------|
| Supabase | Database (PostgreSQL) |
| Supabase Auth | Authentication |
| Supabase Realtime | Live updates |

### Development Tools
- **Package Manager**: npm
- **Build System**: EAS Build
- **Testing**: Expo Go
- **Version Control**: Git

---

## 💻 Installation

### Prerequisites
- Node.js (v16 or higher)
- npm or yarn
- Expo Go app on Android device
- Supabase account (for database)

### Steps

1. **Extract the source code**
   ```bash
   unzip SmartAttend_SourceCode.zip
   cd SAAFYP
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Configure environment variables**
   Create a `.env` file in the root directory:
   ```env
   EXPO_PUBLIC_SUPABASE_URL=your_supabase_url
   EXPO_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
   ```

4. **Start the development server**
   ```bash
   npx expo start --lan
   ```

5. **Run on your device**
   - Open Expo Go app on your Android device
   - Scan the QR code displayed in the terminal
   - App will load and run on your device

---

## 🚀 Usage

### For Teachers

1. **First Time Setup**
   - Register as a teacher with email and password
   - Login with your credentials

2. **Add Students**
   - Navigate to Students tab
   - Tap "Add Student" button
   - Fill in student details
   - System auto-generates QR code

3. **Create Classes**
   - Navigate to Classes tab
   - Tap "Add Class" button
   - Enter class name, form, and assign students

4. **Mark Attendance**
   - Navigate to Scanner tab
   - Tap "Start Session"
   - Select form and class
   - Scan student QR codes
   - Tap "End Session" when done

5. **View Reports**
   - Navigate to Reports tab
   - Select filters (form, class, month)
   - View charts and statistics

### For Parents

1. **Registration**
   - Use "Parent Registration" option
   - Enter email, password, and child's student ID
   - System links your account to child

2. **View Attendance**
   - Login with your credentials
   - Dashboard shows child's overall statistics
   - Navigate to "Attendance History" for detailed records

---

## 🗄 Database Schema

### Tables Overview

1. **users** - Authentication and user profiles
2. **students** - Student information and QR codes
3. **attendance_records** - Check-in/check-out records
4. **class_teacher_student** - Class assignments
5. **class_sessions** - Active scanning sessions

### Key Relationships

```
users (teacher) → class_teacher_student → students
students → attendance_records
users (teacher) → class_sessions
users (parent) → students (via child_id)
```

*For detailed schema, see [DATABASE_SCHEMA.md](DATABASE_SCHEMA.md)*

---

## 📁 Project Structure

```
SAAFYP/
├── app/                           # Application screens
│   ├── (tabs)/                   # Tab navigation screens
│   │   ├── dashboard.tsx         # Teacher dashboard
│   │   ├── parent-dashboard.tsx  # Parent dashboard
│   │   ├── scanner.tsx           # QR code scanner
│   │   ├── students.tsx          # Student management
│   │   ├── classes.tsx           # Class management
│   │   ├── attendance.tsx        # Attendance status
│   │   ├── attendance-history.tsx # Parent history
│   │   ├── reports.tsx           # Analytics
│   │   └── qr-codes.tsx          # QR code display
│   ├── login.tsx                 # Login screen
│   ├── register.tsx              # Teacher registration
│   └── parent-register.tsx       # Parent registration
├── components/                    # Reusable UI components
│   ├── IconSymbol.tsx            # Icon component
│   └── button.tsx                # Custom buttons
├── contexts/                      # React contexts
│   ├── AuthContext.tsx           # Authentication state
│   └── NotificationContext.tsx   # Notifications
├── utils/                         # Utility functions
│   ├── supabase.ts               # Supabase client
│   ├── studentOperations.ts      # Student CRUD
│   ├── attendanceOperations.ts   # Attendance CRUD
│   ├── classOperations.ts        # Class CRUD
│   └── qrCodeGenerator.ts        # QR generation
├── styles/                        # Styling
│   └── commonStyles.ts           # Shared styles
├── constants/                     # Constants
│   └── Colors.ts                 # Color palette
├── app.json                       # Expo configuration
├── package.json                   # Dependencies
├── tsconfig.json                  # TypeScript config
└── eas.json                       # EAS Build config
```

---

## 👩‍💻 Development

### Running in Development Mode

```bash
# Start with LAN connection
npx expo start --lan

# Start with tunnel (for remote access)
npx expo start --tunnel

# Clear cache and start
npx expo start --clear
```

### Common Commands

```bash
# Install dependencies
npm install

# Check for updates
npx expo-doctor

# Run TypeScript check
npx tsc --noEmit

# View logs
npx expo start --android
```

### Development Tips

1. **Use --lan flag** for local network testing
2. **Clear cache** if experiencing issues: `npx expo start --clear`
3. **Check TypeScript** errors before building
4. **Test on real device** for accurate QR scanning
5. **Monitor Supabase** dashboard for database activity

---

## 📦 Building APK

### Using EAS Build

1. **Install EAS CLI**
   ```bash
   npm install -g eas-cli
   ```

2. **Login to Expo**
   ```bash
   eas login
   ```

3. **Configure build**
   ```bash
   eas build:configure
   ```

4. **Build APK**
   ```bash
   eas build --platform android --profile preview
   ```

5. **Download APK**
   - Build will appear on EAS dashboard
   - Download link will be provided
   - Install on Android device

### Build Profiles (eas.json)

- **development**: Development build with debugging
- **preview**: APK for testing (not optimized)
- **production**: Optimized production build

---

## 🤝 Contributing

This project was developed as a Final Year Project. For academic use only.

### If you'd like to contribute:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

---

## 📄 License

This project is developed for academic purposes as part of a Final Year Project.

---

## 👥 Authors

- **Developer**: Final Year Student
- **Institution**:Politeknik Ungku Omar
- **Year**: 2025

---

## 🙏 Acknowledgments

- Expo team for the excellent development platform
- Supabase for the backend infrastructure
- React Native community for components and support
- My supervisor and peers for guidance and feedback

---

## 📞 Support

For questions or issues:
- Check the [PROJECT_DOCUMENTATION.md](PROJECT_DOCUMENTATION.md)
- Review the [DATABASE_SCHEMA.md](DATABASE_SCHEMA.md)
- Contact: frhnikhmal1@gmail.com

---

## 🎓 Academic Use

This project is part of a Final Year Project submission. It demonstrates:
- Mobile app development with React Native
- Database design and implementation
- User authentication and authorization
- Real-time data synchronization
- QR code technology integration
- Data visualization and reporting
- Role-based access control

---

**Built with ❤️ using React Native + Expo**

*Last Updated: November 2025*

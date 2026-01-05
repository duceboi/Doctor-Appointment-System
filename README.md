# Doctor-Appointment-System using MERN stack

This project is a full-stack web application built using the MERN stack (MongoDB, Express.js, React.js, and Node.js) to provide a user-friendly and efficient system for managing doctor appointments. Patients can search for doctors by specialty, location, or availability, book appointments, view their appointment history, and manage their profile information. Doctors can manage their schedules, view patient information, and update appointment statuses.

## Conceptual Project Overview

### 1. **System Architecture**
   - **Three-Tier Architecture**: The system follows a traditional three-tier architecture pattern
     - **Presentation Layer**: React.js frontend for user interface
     - **Application Layer**: Express.js/Node.js backend for business logic
     - **Data Layer**: MongoDB for persistent data storage
   - **RESTful API Design**: Backend exposes RESTful endpoints for client-server communication
   - **JWT-based Authentication**: Stateless authentication using JSON Web Tokens

### 2. **Core Components**

#### A. **Data Models (Database Schema)**
   - **User Model**:
     - Stores user credentials (name, email, hashed password)
     - Role flags (isAdmin, isDoctor) for authorization
     - Notification arrays (notifcation, seennotification) for in-app messaging
     - Serves as the base authentication entity
   
   - **Doctor Model**:
     - Extended profile for medical professionals
     - Personal information (firstName, lastName, phone, email, address)
     - Professional details (specialization, experience, website)
     - Financial info (feesPerCunsaltation)
     - Availability (timings as object)
     - Status field (pending/approved) for admin approval workflow
     - References user via userId for authentication linkage
   
   - **Appointment Model**:
     - Links patients and doctors through userId and doctorId
     - Stores appointment metadata (date, time, status)
     - Denormalized data (doctorInfo, userInfo) for quick access
     - Status tracking (pending/approved/rejected) for appointment lifecycle
     - Timestamps for audit trail

#### B. **Controllers (Business Logic Layer)**
   - **User Controller**:
     - Authentication operations (register, login with bcrypt hashing)
     - Authorization verification (getUserData with JWT)
     - Doctor application submission (apply-doctor)
     - Notification management (get/delete notifications)
     - Patient-side appointment operations (book, view, check availability)
     - Doctor discovery (getAllDoctors)
   
   - **Doctor Controller**:
     - Profile management (getDoctorInfo, updateProfile)
     - Appointment management from doctor perspective
     - Status updates for appointments
     - Doctor-specific data retrieval by ID
   
   - **Admin Controller**:
     - User management (getAllUsers)
     - Doctor management (getAllDoctors)
     - Account approval workflow (changeAccountStatus)
     - Triggers notifications when doctor status changes

#### C. **Routes (API Endpoints)**
   - **User Routes** (`/api/v1/user/`):
     - POST `/login` - User authentication
     - POST `/register` - New user registration
     - POST `/getUserData` - Get authenticated user info (protected)
     - POST `/apply-doctor` - Submit doctor application (protected)
     - POST `/get-all-notification` - Fetch user notifications (protected)
     - POST `/delete-all-notification` - Clear notifications (protected)
     - GET `/getAllDoctors` - List all approved doctors (protected)
     - POST `/book-appointment` - Create new appointment (protected)
     - POST `/booking-availbility` - Check doctor availability (protected)
     - GET `/user-appointments` - Get user's appointments (protected)
   
   - **Doctor Routes** (`/api/v1/doctor/`):
     - POST `/getDoctorInfo` - Get doctor profile (protected)
     - POST `/updateProfile` - Update doctor profile (protected)
     - POST `/getDoctorById` - Get specific doctor details (protected)
     - GET `/doctor-appointments` - Get doctor's appointments (protected)
     - POST `/update-status` - Update appointment status (protected)
   
   - **Admin Routes** (`/api/v1/admin/`):
     - GET `/getAllUsers` - List all users (protected)
     - GET `/getAllDoctors` - List all doctors (protected)
     - POST `/changeAccountStatus` - Approve/reject doctors (protected)

#### D. **Middleware**
   - **Authentication Middleware**:
     - Validates JWT tokens from request headers
     - Extracts user ID from token and attaches to request
     - Protects routes from unauthorized access
     - Returns 401 for invalid/missing tokens

### 3. **User Roles and Permissions**

#### A. **Patient (Regular User)**
   - Can register and login to the system
   - Can view list of approved doctors
   - Can book appointments with available doctors
   - Can check doctor availability before booking
   - Can view their appointment history
   - Receives notifications about appointment status
   - Can apply to become a doctor

#### B. **Doctor (isDoctor = true)**
   - All patient privileges included
   - Can manage their professional profile
   - Can view appointments scheduled with them
   - Can approve or reject appointment requests
   - Can set their availability timings
   - Can update consultation fees
   - Must be approved by admin before becoming active

#### C. **Admin (isAdmin = true)**
   - Can view all users in the system
   - Can view all doctors (pending and approved)
   - Can approve or reject doctor applications
   - Controls doctor account activation
   - Manages overall system access

### 4. **Data Flow and System Workflows**

#### A. **User Registration and Authentication Flow**
   1. User submits registration with name, email, password
   2. System checks for existing email
   3. Password is hashed using bcrypt with salt
   4. User document created in MongoDB
   5. On login, password compared with hash
   6. JWT token generated with user ID payload
   7. Token sent to client with 1-day expiration
   8. Client includes token in Authorization header for protected routes

#### B. **Doctor Application and Approval Flow**
   1. Registered user fills doctor application form
   2. Application creates doctor document with userId reference
   3. Doctor status set to "pending"
   4. Admin receives notification of new application
   5. Admin reviews doctor details
   6. Admin approves or rejects application
   7. Doctor's user account updated (isDoctor flag)
   8. Doctor receives notification of status change
   9. If approved, doctor appears in patient's doctor list

#### C. **Appointment Booking Flow**
   1. Patient views list of approved doctors
   2. Patient selects doctor and checks availability
   3. System validates selected time slot against existing appointments
   4. Patient confirms booking with date and time
   5. Appointment created with "pending" status
   6. Doctor receives notification of new appointment
   7. Doctor reviews and approves/rejects appointment
   8. Patient receives notification of decision
   9. Appointment status updated in database

#### D. **Notification System Flow**
   1. System events trigger notification creation
   2. Notification object added to user's notifcation array
   3. User fetches notifications on login/page load
   4. User can mark notifications as seen
   5. Seen notifications moved to seennotification array
   6. User can delete all notifications

### 5. **Security Features**

#### A. **Password Security**
   - Passwords hashed using bcryptjs with 10 salt rounds
   - Original passwords never stored or logged
   - Secure comparison using bcrypt.compare()

#### B. **Authentication Security**
   - JWT tokens with secret key from environment variables
   - Token expiration set to 1 day for automatic logout
   - Tokens validated on every protected route access
   - User ID extracted from verified token

#### C. **Authorization Security**
   - Role-based access control using isAdmin and isDoctor flags
   - Middleware prevents unauthorized route access
   - User ID from token ensures users access only their data

### 6. **Technical Stack Details**

#### A. **Backend Dependencies**
   - **express**: Web framework for REST API
   - **mongoose**: MongoDB ODM for data modeling
   - **bcryptjs**: Password hashing for security
   - **jsonwebtoken**: JWT implementation for auth
   - **dotenv**: Environment variable management
   - **morgan**: HTTP request logger middleware
   - **moment**: Date/time manipulation for appointments
   - **colors**: Console output formatting
   - **nodemon**: Development server with auto-restart
   - **concurrently**: Run multiple npm scripts simultaneously

#### B. **Database Design**
   - **MongoDB**: NoSQL document database
   - Collections: users, doctors, appointments
   - Indexed on email for fast user lookup
   - References between collections via IDs
   - Timestamps for audit trails

### 7. **Key Design Patterns**

#### A. **MVC Architecture**
   - **Models**: Database schemas and data structure
   - **Views**: React frontend (separate from this backend)
   - **Controllers**: Business logic and request handling
   - Routes act as entry points connecting HTTP to controllers

#### B. **Middleware Chain**
   - Request → Morgan Logger → JSON Parser → Auth Middleware → Controller → Response
   - Each middleware can short-circuit the chain

#### C. **Repository Pattern**
   - Mongoose models abstract database operations
   - Controllers interact with models, not raw database
   - Clean separation of data access and business logic

### 8. **Application Features by User Journey**

#### A. **Patient Journey**
   1. Sign up and create account
   2. Login and receive JWT token
   3. Browse available doctors by specialization
   4. View doctor details (experience, fees, timings)
   5. Check availability for desired time slot
   6. Book appointment
   7. Receive booking confirmation
   8. View appointment status and history
   9. Get notifications on appointment updates

#### B. **Doctor Journey**
   1. Register as regular user
   2. Submit doctor application with credentials
   3. Wait for admin approval
   4. Receive approval notification
   5. Login with doctor privileges
   6. Set up profile (timings, fees, specialization)
   7. View incoming appointment requests
   8. Approve or reject appointments
   9. Manage schedule and availability

#### C. **Admin Journey**
   1. Login with admin credentials
   2. View dashboard of all users
   3. Review pending doctor applications
   4. Verify doctor credentials
   5. Approve or reject applications
   6. Monitor system usage
   7. Manage user accounts

### 9. **System Scalability Considerations**

#### A. **Current Design**
   - Stateless JWT authentication enables horizontal scaling
   - MongoDB can be clustered for database scaling
   - REST API allows frontend-backend separation
   - Microservices-ready architecture

#### B. **Potential Enhancements**
   - Add caching layer (Redis) for frequently accessed data
   - Implement message queue for notification delivery
   - Add API rate limiting for security
   - Implement database indexing optimization
   - Add connection pooling for MongoDB

### 10. **Error Handling Strategy**
   - Try-catch blocks in all controller methods
   - Consistent error response format with success flag
   - HTTP status codes (200, 201, 401, 500) for different scenarios
   - Error logging to console for debugging
   - User-friendly error messages returned to client

## Key Features:

- Patient registration and profile management
- Doctor registration and profile management
- Appointment search and booking
- Appointment cancellation and rescheduling
- Appointment history and notifications
- Secure authentication and authorization
- Responsive and user-friendly design

## Technologies:

- Front-end: React.js
- Back-end: Express.js, Node.js
- Database: MongoDB
- Other dependencies: Axios, Redux, Moment, Ant Design, Bootstrap

## Installation and Setup:

1. Clone the repository: git clone https://github.com/md0011/Doctor-Appointment-System
2. Install dependencies: npm install or yarn install
3. Create a .env file in the root directory and set environment variables for database connection, authentication, and other configurations.
4. Start the development server: npm start or yarn start

## Usage:

1. Open the application in your web browser (usually at http://localhost:3000).
2. Register as a patient or doctor (or use existing accounts if available).
3. Explore the features and functionality as needed.

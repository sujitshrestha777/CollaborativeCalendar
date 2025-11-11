# 📅 Collaborative Calendar

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![React](https://img.shields.io/badge/React-18+-61DAFB?logo=react)](https://reactjs.org/)
[![Node.js](https://img.shields.io/badge/Node.js-18+-339933?logo=node.js)](https://nodejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.3+-3178C6?logo=typescript)](https://www.typescriptlang.org/)

> A powerful team scheduling platform that intelligently manages team availability, automates meeting coordination, and optimizes schedule planning with priority-based algorithms.

## 🎯 About The Project

**Note:** *This repository is a fork, but I have developed approximately 95% of the codebase with complete architectural redesign, feature implementation, and advanced scheduling algorithms. look at the commit history*

This isn't just another calendar app—it's a smart scheduling system that:
- **Automatically finds optimal meeting times** based on team availability
- **Prioritizes meetings** using deadline, duration, and importance metrics
- **Manages team collaboration** with invite systems and member coordination
- **Protects individual time** with personal blocking features
- **Ensures security** with email verification and JWT authentication

## ✨ Key Features

### 🔐 Authentication & Security
- **Email Verification System** - Secure code-based email verification using Nodemailer
- **JWT Authentication** - Token-based secure authentication
- **Password Encryption** - Bcrypt hashing for password security

### 👥 Team Management
- **Create & Join Teams** - Build collaborative workspaces
- **Team Invitations** - Send and accept team invites
- **Member Selection** - Choose specific team members for meetings
- **Team Coordination** - Manage multiple teams simultaneously

### 📆 Smart Scheduling
- **Individual Time Blocking** - Reserve personal work time
- **Meeting Creation** - Schedule meetings with selected team members
- **Priority-Based Scheduling** - Algorithm considers:
  - Meeting deadline urgency
  - Duration requirements
  - Importance level
  - Weighted optimization for best time slots
- **Availability Management** - Track and respect everyone's free time

### 🎯 Advanced Features
- **Conflict Detection** - Prevents double-booking
- **Flexible Time Slots** - Customizable meeting durations
- **Multi-Member Meetings** - Coordinate with multiple team members
- **Personal vs Team Time** - Balance individual work with collaboration

## 🛠️ Tech Stack

### Frontend
- **React** - UI framework
- **Tailwind CSS** - Styling and design
- **TypeScript** - Type safety

### Backend
- **Node.js & Express** - Server framework
- **TypeScript** - Type-safe backend code
- **Prisma ORM** - Database management
- **Zod** - Schema validation

### Security & Communication
- **JWT (jsonwebtoken)** - Authentication tokens
- **Bcrypt** - Password hashing
- **Nodemailer** - Email verification
- **CORS** - Cross-origin security

### Development Tools
- **Nodemon** - Hot reloading
- **ts-node** - TypeScript execution
- **Prisma Client** - Type-safe database queries
## 📸 Screenshots

### 📊 Meeting Dashboard
![Meeting Dashboard](https://i.imgur.com/4MupPA0.png)
*Main dashboard showing all scheduled meetings and team coordination*

### 🎯 Smart Meeting Creation
![Create Event Form](https://i.imgur.com/0lKrxvS.png)
*Priority-based meeting form with deadline, importance, duration, and team member selection*

### 🔒 Individual Time Blocking (proper validation)
![Create Block Time](https://i.imgur.com/8yzh0XC.png)
*Block personal time for focused work (doctor appointments, personal tasks, etc.) *

![View Block Times](https://i.imgur.com/GTMQr9q.png)
*View and manage all your blocked time slots*

### ✉️ Email Verification System
![Signup Form](https://i.imgur.com/0oUD1qD.png)
*Secure signup with email verification*

![Email Code](https://i.imgur.com/tPQVmTM.png)
*Verification code sent to Gmail inbox*

![Verify Code Input](https://i.imgur.com/vWPK7Zk.png)
*Enter verification code to activate account*


## 🚀 Getting Started

### Prerequisites

- Node.js v18 or higher
- npm or yarn
- PostgreSQL/MySQL database
- Gmail account for email verification

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/your-username/collaborative-calendar.git
cd collaborative-calendar
```

2. **Install server dependencies**
```bash
cd server
npm install
```

3. **Install client dependencies**
```bash
cd ../client
npm install
```

4. **Set up environment variables**

Create a `.env` file in the server directory:

```env
# Database
DATABASE_URL="your_database_url"

# JWT
JWT_SECRET="your_jwt_secret_key"

# Email Configuration
EMAIL_USER="your_gmail@gmail.com"
EMAIL_APP_PASSWORD="your_gmail_app_password"

# Server
PORT=5000
NODE_ENV=development
```

**Getting Gmail App Password:**
1. Go to Google Account settings
2. Enable 2-Step Verification
3. Go to Security → App passwords
4. Generate a new app password for Mail

5. **Set up the database**
```bash
cd server
npx prisma migrate dev
npx prisma generate
```

6. **Run the application**

Terminal 1 (Backend):
```bash
cd server
npm run dev
```

Terminal 2 (Frontend):
```bash
cd client
npm run dev
```

The app should now be running on `http://localhost:3000` (frontend) and `http://localhost:5000` (backend).

## 💡 Usage Examples

### Creating a Team
1. Sign up and verify your email
2. Navigate to "Create Team"
3. Invite team members via email
4. Members accept invitations to join

### Scheduling a Smart Meeting
1. Select team members for the meeting
2. Set meeting parameters:
   - **Deadline**: When the meeting must happen by
   - **Duration**: How long the meeting needs
   - **Importance**: Priority level (1-5)
3. System calculates optimal time slot using weighted algorithm
4. Meeting is scheduled at the best available time

### Blocking Personal Time
1. Go to your calendar
2. Select time slots for individual work
3. Blocked time is excluded from team meeting suggestions
4. Maintain work-life balance and focus time

## 📊 Smart Scheduling Algorithm

The intelligent scheduling system I built uses a **4-step priority-based approach**:

### Step 1: Priority Calculation
Every meeting gets a priority score based on:
```typescript
priorityScore = calculatePriorityScore({
  importance: 1-5,      // User-defined importance
  deadline: Date,       // How soon it needs to happen
  duration: minutes     // Meeting length
});
```

### Step 2: Preferred Time Check
- Checks if all attendees are available at the requested time
- Validates against existing meetings and blocked time
- If available, schedules immediately

### Step 3: Smart Replacement
If preferred time is unavailable:
- Compares new meeting priority vs existing meetings
- **Automatically cancels lower priority meetings** to accommodate higher priority ones
- Ensures urgent/important meetings always get scheduled

### Step 4: Next Available Time
If replacement isn't justified:
- Searches the next 7 days for available slots
- Finds the earliest time all attendees are free
- Respects personal blocked time

### Step 5: Pending Queue
If no time found:
- Creates meeting as `PENDING` status
- Allows manual scheduling later
- Prevents urgent meetings from being lost

```typescript
// Real implementation from my codebase
const availability = await checkTimeSlotAvailability(
  attendeeIds, 
  preferredDate, 
  preferredEndTime
);

if (availability.allAvailable) {
  // Schedule immediately
} else if (await replaceLowPriorityMeeting(priorityScore)) {
  // Replace lower priority meeting
} else {
  const nextTime = await findNextAvailableTime(attendeeIds, duration);
  // Schedule at next available time
}
```

This ensures:
- High-priority meetings never get blocked
- Team coordination is optimized automatically
- No conflicts or double-booking
- Fair time allocation based on actual importance

## 🏗️ Key Implementations I Built

### Backend Architecture
- Complete TypeScript Express server with type safety
- Prisma ORM with custom schema design
- JWT authentication middleware
- Zod validation schemas for all endpoints
- RESTful API design

### Smart Scheduling Engine
- Priority calculation algorithm
- Availability checking across multiple users
- Automatic meeting replacement logic
- Next-available-time finder
- Conflict detection and resolution

### Authentication System
- Email verification with Nodemailer
- Secure password hashing with Bcrypt
- JWT token generation and validation
- Protected route middleware

### Team Management
- Team creation and invitation system
- Member status tracking (PENDING/ACCEPTED)
- Multi-team support per user
- Attendee validation for meetings

## 🗺️ Roadmap

- [x] User authentication with email verification
- [x] Team creation and invitation system
- [x] Personal time blocking
- [x] Priority-based meeting scheduling
- [x] Multi-member meeting coordination
- [ ] Calendar integrations (Google Calendar, Outlook)
- [ ] Recurring meeting patterns
- [ ] Mobile app (React Native)
- [ ] Real-time notifications
- [ ] Meeting reminders and alerts
- [ ] Analytics dashboard for time usage

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the project
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 🙏 Acknowledgments

- Built with modern web technologies and best practices
- Inspired by enterprise scheduling solutions

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 Author

**Your Name**

- GitHub: [@your-username](https://github.com/your-username)
- LinkedIn: [Your Profile](https://linkedin.com/in/your-profile)
- Email: your.email@example.com

---

## 🐛 Known Issues

- Email delivery may be delayed based on Gmail's sending limits
- Timezone handling requires proper configuration
- Large teams (50+ members) may need optimization

## 📧 Support

If you have any questions or run into issues, please open an issue on GitHub.

---

⭐ **Star this repo if you find it useful!**

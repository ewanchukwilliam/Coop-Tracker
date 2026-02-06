# Job Hunting Tracker

A full-stack web application for tracking job applications with automated web scraping capabilities. Built with React, Express, MySQL, and Docker.

## Features

### Core Functionality
- **Job Application Management**: Create, read, update, and delete job applications with detailed information
- **Automated Web Scraping**: Scrape job listings from Indeed.com using Puppeteer with stealth mode
- **User Authentication**: Secure JWT-based authentication with refresh tokens and httpOnly cookies
- **Analytics Dashboard**: Visualize application statistics with daily, weekly, and monthly charts
- **Dark-Themed UI**: Modern, responsive interface built with Tailwind CSS and Radix UI
- **Excel Export**: Format and download application data as Excel files

### Application Tracking
- Date of application
- Job title and company name
- Location
- Salary offer
- Job description
- User-specific data isolation

### Job Scraping
- Filter for junior/entry-level positions
- Keyword matching for developer roles
- Extract job details including posting dates and full descriptions
- Anti-detection measures with Puppeteer stealth plugin

## Tech Stack

### Frontend
- **Framework**: React 18.3.1
- **Routing**: React Router DOM v6
- **Styling**: Tailwind CSS 3.4.4
- **UI Components**: Radix UI (dialog, dropdown, select, tabs, tooltip)
- **Charts**: Recharts 2.12.7
- **Animation**: Framer Motion 11.2.12
- **HTTP Client**: Axios
- **Icons**: FontAwesome, Lucide React, React Icons
- **Date Handling**: date-fns 3.6.0

### Backend
- **Runtime**: Node.js 20 (Alpine)
- **Framework**: Express.js 4.19.2
- **Database**: MySQL 8.0
- **Authentication**: JWT (jsonwebtoken 9.0.2)
- **Web Scraping**: Puppeteer 22.13.1 with Puppeteer Extra (stealth plugin)
- **ORM**: Sequelize 6.37.3
- **Development**: Nodemon

### Infrastructure
- **Containerization**: Docker & Docker Compose
- **Reverse Proxy**: Nginx
- **Database Persistence**: Volume mount at `$HOME/database`

## Prerequisites

- Docker and Docker Compose
- Git
- At least 2GB of free disk space

## Installation & Setup

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/jobhunting.git
cd jobhunting
```

### 2. Environment Configuration

Create a `.env` file in the `server` directory:

```env
DB_HOST=mysql
DB_USER=root
DB_PASSWORD=your_password
DB_NAME=jobtracker
JWT_SECRET=your_jwt_secret
JWT_REFRESH_SECRET=your_refresh_secret
PORT=5050
```

### 3. Start the Application

```bash
docker-compose up -d
```

This will start four containers:
- **nginx** (Port 80): Reverse proxy
- **frontend** (Port 3000): React development server
- **backend** (Port 5050): Express API server
- **mysql** (Port 3306): MySQL database

### 4. Database Initialization

The MySQL database will automatically initialize with the required schema on first run:
- `users` table: username, password, email, login_token, refresh_token, unique_id
- `applications` table: date, title, company, location, offer, description, userid

### 5. Access the Application

Open your browser and navigate to:
```
http://localhost
```

## Usage

### Creating an Account
1. Navigate to the registration page
2. Enter username, email, and password
3. The system validates unique username/email combinations

### Adding Job Applications
1. Log in to your account
2. Click "Add Application" or navigate to `/create`
3. Fill in the job details:
   - Date applied
   - Job title
   - Company name
   - Location
   - Salary offer
   - Job description
4. Submit to save the application

### Managing Applications
- **View All**: See all your applications in a sortable/filterable table on the home page
- **Edit**: Click on an application to edit details at `/edit/:id`
- **Delete**: Remove applications from the table view
- **View Details**: See full application information at `/read/:id`

### Web Scraping
1. Navigate to the scraping section
2. Enter search keywords (e.g., "frontend developer", "backend engineer")
3. The scraper will fetch listings from Indeed.com
4. View and save interesting postings directly to your applications

### Analytics
- View application statistics on the dashboard
- See daily, weekly, and monthly application trends
- Track total applications and activity patterns

### Exporting Data
Download your application data as an Excel file for offline tracking and analysis.

## Project Structure

```
jobhunting/
├── client/                    # React frontend
│   ├── src/
│   │   ├── elements/         # Page components (Home, Create, Edit, Read)
│   │   ├── components/       # Reusable UI (Menu, Charts, Statistics)
│   │   ├── authentication/   # Auth provider & protected routes
│   │   ├── lib/             # Utility functions
│   │   └── index.js         # Entry point
│   ├── public/
│   └── package.json
├── server/                    # Express backend
│   ├── src/
│   │   ├── config/          # Environment configuration
│   │   ├── db/              # MySQL connection & operations
│   │   ├── middleware/      # JWT validation
│   │   ├── routes/          # API endpoints
│   │   ├── scrape/          # Puppeteer scraping logic
│   │   └── utils/           # Cookie utilities
│   └── package.json
├── nginx/                     # Reverse proxy configuration
├── mysql-init/               # Database initialization scripts
│   └── 01_init_db.sql
├── docker-compose.yaml       # Container orchestration
├── JobHunting.sql           # Database schema
└── README.md

```

## API Endpoints

### Authentication
- `POST /auth/login_user` - User login
- `POST /auth/create_user` - User registration

### Applications
- `GET /api/applications` - Get all user applications
- `POST /api/add_application` - Create new application
- `PUT /api/edit_user/:id` - Update application
- `DELETE /api/delete/:id` - Delete application
- `GET /api/applications/stats` - Get application statistics

### Statistics
- `GET /api/statistics` - Get daily/weekly/monthly statistics

### Web Scraping
- `GET /api/postings` - Get scraped job postings

### User
- `GET /user/*` - User information endpoints

## Development

### Local Development Setup

#### Frontend
```bash
cd client
npm install
npm start
```

#### Backend
```bash
cd server
npm install
npm run dev
```

### Docker Development

Rebuild containers after code changes:
```bash
docker-compose down
docker-compose up --build
```

View logs:
```bash
docker-compose logs -f [service-name]
```

Access MySQL:
```bash
docker exec -it jobhunting-mysql-1 mysql -u root -p
```

## Security Notes

### Current Implementation
- JWT tokens stored in httpOnly cookies (secure, sameSite=None)
- Access tokens expire after 1 hour
- Refresh tokens expire after 24 hours
- User data isolated by unique UUID
- CORS configured for localhost:3000

### Recommended Improvements
- Implement password hashing (bcrypt or argon2)
- Add rate limiting for API endpoints
- Implement CSRF protection
- Use environment-specific CORS configuration
- Add input validation and sanitization
- Implement SQL injection prevention with parameterized queries

## Network Architecture

All services are connected via an internal Docker bridge network (`internalnet`):
- Nginx acts as the entry point and proxies requests to the frontend
- Frontend communicates with backend via `http://backend:5050`
- Backend connects to MySQL via the `mysql` hostname
- External access only through Nginx on port 80

## Database Schema

### users
- `username` (VARCHAR, unique)
- `password` (VARCHAR)
- `email` (VARCHAR, unique)
- `login_token` (TEXT)
- `refresh_token` (TEXT)
- `unique_id` (VARCHAR, UUID)

### applications
- `id` (INT, auto-increment)
- `date` (DATE)
- `title` (VARCHAR)
- `company` (VARCHAR)
- `location` (VARCHAR)
- `offer` (VARCHAR)
- `description` (TEXT)
- `userid` (VARCHAR, foreign key to users.unique_id)

## Troubleshooting

### Containers won't start
```bash
# Check logs
docker-compose logs

# Restart all services
docker-compose restart

# Rebuild from scratch
docker-compose down -v
docker-compose up --build
```

### Database connection errors
- Verify MySQL container is running: `docker ps`
- Check environment variables in server/.env
- Ensure database initialization completed: `docker-compose logs mysql`

### Frontend can't reach backend
- Verify backend container is running on port 5050
- Check Axios baseURL configuration in frontend
- Ensure CORS settings allow localhost:3000

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Built with React, Express, and MySQL
- UI components from Radix UI and Tailwind CSS
- Web scraping powered by Puppeteer
- Charts by Recharts

## Support

For issues, questions, or contributions, please open an issue on GitHub.

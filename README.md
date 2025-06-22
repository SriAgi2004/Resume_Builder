
# Smart Resume Builder with AI Suggestions

A comprehensive full-stack web application for creating professional resumes with AI-powered improvement suggestions.

## Features

- 📝 **Interactive Resume Builder** - Create resumes with dynamic forms
- 🤖 **AI-Powered Suggestions** - Get improvement tips using OpenAI GPT-3.5
- 📄 **PDF Export** - Download professional PDF resumes
- 💾 **Data Persistence** - Save and manage resume data
- 📱 **Responsive Design** - Works on desktop and mobile
- 🎨 **Professional Templates** - Clean, modern resume layouts

## Tech Stack

### Frontend
- React 18 with TypeScript
- Tailwind CSS for styling
- Shadcn/UI components
- Vite for build tooling
- React Router for navigation

### Backend (Separate Deployment)
- Node.js with Express
- MongoDB with Mongoose
- OpenAI API integration
- Security middleware (Helmet, CORS, Rate Limiting)

## Getting Started

### Prerequisites
- Node.js 18+ 
- MongoDB Atlas account (for database)
- OpenAI API key (for AI suggestions)

### Frontend Setup

1. **Clone and install dependencies:**
```bash
git clone <repository-url>
cd resume-builder-frontend
npm install
```

2. **Start development server:**
```bash
npm run dev
```

3. **Open in browser:**
```
http://localhost:8080
```

### Backend Setup (Separate Project)

1. **Create new Node.js project:**
```bash
mkdir resume-builder-backend
cd resume-builder-backend
npm init -y
```

2. **Install dependencies:**
```bash
npm install express mongoose cors dotenv openai helmet express-rate-limit
npm install -D nodemon
```

3. **Copy backend code from `backend-structure.md`**

4. **Set up environment variables:**
```bash
# Create .env file
MONGODB_URI=your_mongodb_connection_string
PORT=5000
NODE_ENV=production
```

5. **Run backend server:**
```bash
npm run dev  # for development
npm start    # for production
```

## Deployment

### Frontend (Vercel/Netlify)
1. Connect repository to Vercel/Netlify
2. Update API endpoints to use deployed backend URL
3. Deploy

### Backend (Render/Railway/Glitch)
1. Push backend code to separate repository
2. Connect to hosting service
3. Set environment variables
4. Deploy

### Database (MongoDB Atlas)
1. Create MongoDB Atlas cluster
2. Set up database user and network access
3. Get connection string
4. Add to backend environment variables

## API Endpoints

- `POST /api/resume` - Save resume data
- `GET /api/resume/:userId` - Get resume by user ID
- `PUT /api/resume/:userId` - Update resume
- `POST /api/suggestions` - Get AI suggestions

## Usage

1. **Fill out resume sections** using the interactive forms
2. **Preview your resume** in real-time
3. **Get AI suggestions** by entering your OpenAI API key
4. **Export as PDF** for job applications
5. **Save progress** - data persists between sessions

## Key Components

- `ResumeForm` - Main form with all resume sections
- `PersonalInfoForm` - Contact information
- `ExperienceForm` - Work experience with dynamic entries
- `EducationForm` - Educational background
- `SkillsForm` - Skills with tag-based input
- `ProjectsForm` - Project showcase
- `CertificationsForm` - Professional certifications
- `ResumePreview` - Live preview and PDF export
- `AISuggestions` - AI-powered improvement tips

## AI Integration

The app integrates with OpenAI's GPT-3.5 model to provide:
- Content improvement suggestions
- Formatting recommendations
- Industry-specific advice
- Achievement quantification tips

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

MIT License - see LICENSE file for details.

## Support

For support and questions, please check the documentation or create an issue in the repository.
